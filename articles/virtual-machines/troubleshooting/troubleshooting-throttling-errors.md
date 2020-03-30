---
title: Rozwiązywanie problemów z ograniczaniem przepustowości na platformie Azure | Dokumenty firmy Microsoft
description: Ograniczanie błędów, ponownych prób i wycofywania w usłudze Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: f5fbd80fc9a8e519cf8f49ab16d7e747c6a8171b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045361"
---
# <a name="troubleshooting-api-throttling-errors"></a>Rozwiązywanie problemów dotyczących błędów ograniczania przepływności interfejsu API 

Żądania obliczeń platformy Azure mogą być ograniczane w ramach subskrypcji i na podstawie dla regionu, aby pomóc w ogólnej wydajności usługi. Zapewniamy, że wszystkie wywołania dostawcy zasobów obliczeniowych platformy Azure (CRP), który zarządza zasobami w obszarze nazw Microsoft.Compute, nie przekraczają maksymalnej dopuszczalnej szybkości żądania interfejsu API. W tym dokumencie opisano ograniczanie przepustowości interfejsu API, szczegółowe informacje na temat rozwiązywania problemów z ograniczaniem przepustowości oraz najlepsze rozwiązania, aby uniknąć ograniczania przepustowości.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Ograniczanie przepustowości przez usługę Azure Resource Manager a dostawców zasobów  

Jako drzwi wejściowe do platformy Azure usługa Azure Resource Manager wykonuje sprawdzanie poprawności uwierzytelniania i pierwszego zamówienia oraz ograniczanie wszystkich przychodzących żądań interfejsu API. Limity szybkości wywołań usługi Azure Resource Manager i powiązane nagłówki HTTP odpowiedzi diagnostycznej są opisane [w tym miejscu](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling).
 
Gdy klient interfejsu API platformy Azure otrzymuje błąd ograniczania przepustowości, stan HTTP jest 429 zbyt wiele żądań. Aby zrozumieć, czy ograniczanie żądań jest wykonywane przez usługę Azure Resource `x-ms-ratelimit-remaining-subscription-reads` Manager lub `x-ms-ratelimit-remaining-subscription-writes` dostawcy zasobów źródłowych, takich jak CRP, sprawdź dla żądań GET i nagłówki odpowiedzi dla żądań innych niż GET. Jeśli liczba pozostałych wywołań zbliża się do 0, osiągnięto ogólny limit wywołań subskrypcji zdefiniowany przez usługę Azure Resource Manager. Działania wszystkich klientów subskrypcji są liczone razem. W przeciwnym razie ograniczanie przepustowości pochodzi od dostawcy zasobów `/providers/<RP>` docelowych (adresowany przez segment adresu URL żądania). 

## <a name="call-rate-informational-response-headers"></a>Nagłówki odpowiedzi informacyjnej o szybkości wywołania 

| Nagłówek                            | Format wartości                           | Przykład                               | Opis                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-pozostałe-zasób |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Liczba pozostałych wywołań interfejsu API dla zasad ograniczania przepustowości obejmujących zasobomierza lub grupy operacji, w tym miejsce docelowe tego żądania                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Liczba połączeń jest "naliczona" dla tego żądania HTTP w ramach limitu obowiązujących zasad. Jest to najczęściej 1. Żądania wsadowe, takie jak skalowanie zestawu skalowania maszyny wirtualnej, mogą pobierać wiele opłat. |


Należy zauważyć, że żądanie interfejsu API może podlegać wielu zasadach ograniczania przepustowości. Dla każdej zasady `x-ms-ratelimit-remaining-resource` będzie znajdować się osobny nagłówek. 

Oto przykładowa odpowiedź na usunięcie żądania zestawu skalowania maszyny wirtualnej.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Szczegóły błędu ograniczania przepustowości

Stan HTTP 429 jest często używany do odrzucania żądania, ponieważ osiągnięto limit szybkości połączeń. Typowa odpowiedź na błąd ograniczania przepustowości od dostawcy zasobów obliczeniowych będzie wyglądać jak w poniższym przykładzie (wyświetlane są tylko odpowiednie nagłówki):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Zasady z pozostałą liczbą wywołań 0 jest tym, z powodu którego zwracany jest błąd ograniczania przepustowości. W tym przypadku `HighCostGet30Min`jest to . Ogólny format treści odpowiedzi jest ogólny format błędu interfejsu API usługi Azure Resource Manager (zgodne z OData). Główny kod błędu `OperationNotAllowed`, jest jedynym dostawcą zasobów obliczeniowych używa do raportowania błędów ograniczania przepustowości (wśród innych typów błędów klienta). Właściwość `message` wewnętrznych error(s) zawiera serializowane JSON struktury ze szczegółami naruszenia ograniczania przepustowości.

Jak pokazano powyżej, każdy błąd ograniczania obejmuje `Retry-After` nagłówek, który zapewnia minimalną liczbę sekund, przez którą klient powinien poczekać przed ponowieniem próby żądania. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Analizator błędów szybkości wywołania interfejsu API i ograniczania przepustowości
Wersja zapoznawcza funkcji rozwiązywania problemów jest dostępna dla interfejsu API dostawcy zasobów obliczeniowych. Te polecenia cmdlet programu PowerShell zawierają statystyki dotyczące szybkości żądań interfejsu API według interwału czasu na operację i ograniczania naruszeń na grupę operacji (zasady):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Statystyki wywołania interfejsu API może zapewnić doskonały wgląd w zachowanie klienta subskrypcji i umożliwić łatwą identyfikację wzorców wywołań, które powodują ograniczanie przepustowości.

Ograniczenie analizatora na razie jest to, że nie liczy żądań dla typów zasobów dysku i migawki (w obsłudze dysków zarządzanych). Ponieważ zbiera dane z telemetrii CRP, nie może również pomóc w identyfikowaniu błędów ograniczania przepustowości z arm. Ale te można łatwo zidentyfikować na podstawie charakterystycznych nagłówków odpowiedzi ARM, jak wspomniano wcześniej.

Polecenia cmdlet programu PowerShell są przy użyciu interfejsu API usługi REST, który można łatwo wywołać bezpośrednio przez klientów (choć bez formalnej pomocy technicznej jeszcze). Aby wyświetlić format żądania HTTP, uruchom polecenia cmdlet z przełącznikiem -Debug lub podsłuchiwanie ich wykonywania za pomocą fiddler.


## <a name="best-practices"></a>Najlepsze rozwiązania 

- Nie należy ponowiać błędów interfejsu API usługi Platformy Azure bezwarunkowo i/lub natychmiast. Częstym zjawiskiem jest dla kodu klienta, aby dostać się do pętli szybkiego ponawiania próby po napotkaniu błędu, który nie jest ponawiany. Ponownych prób ostatecznie wyczerpie dozwolony limit wywołań dla grupy operacji docelowej i wpłynie na innych klientów subskrypcji. 
- W przypadku automatyzacji interfejsu API dużych woluminów należy rozważyć wdrożenie proaktywnego samoobserwowania po stronie klienta, gdy liczba dostępnych wywołań dla docelowej grupy operacyjnej spadnie poniżej pewnego niskiego progu. 
- Podczas śledzenia operacji asynchroniiowych należy przestrzegać wskazówek nagłówka Ponawianie ponawiania. 
- Jeśli kod klienta potrzebuje informacji o określonej maszynie wirtualnej, kwerenda, że maszyna wirtualna bezpośrednio zamiast wyświetlania wszystkich maszyn wirtualnych w grupie zasobów zawierających lub całej subskrypcji, a następnie wybierając wymaganą maszynę wirtualną po stronie klienta. 
- Jeśli kod klienta wymaga maszyn wirtualnych, dysków i migawek z określonej lokalizacji platformy Azure, użyj formularza opartego na lokalizacji kwerendy `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` zamiast wykonywania zapytań o wszystkie maszyny wirtualne subskrypcji, a następnie filtrowania według lokalizacji po stronie klienta: zapytanie do obliczeń dostawcy zasobów regionalnych punktów końcowych. 
-   Podczas tworzenia lub aktualizowania zasobów interfejsu API w szczególności maszyn wirtualnych i zestawów skalowania maszyn wirtualnych znacznie bardziej efektywne jest `provisioningState`śledzenie zwracanych operacji asynchronicznego do zakończenia niż sondowanie samego adresu URL zasobu (na podstawie ).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ponawiania prób dla innych usług na platformie Azure, zobacz [Wskazówki dotyczące ponawiania prób dla określonych usług](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
