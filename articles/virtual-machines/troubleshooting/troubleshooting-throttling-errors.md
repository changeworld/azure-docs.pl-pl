---
title: Rozwiązywania problemów, ograniczanie błędy na platformie Azure | Dokumentacja firmy Microsoft
description: Ograniczanie przepustowości, błędy, ponownych prób i wycofywania w usługi Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: efa10f5beae64105857b00b186683d491edb00f5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233769"
---
# <a name="troubleshooting-api-throttling-errors"></a>Rozwiązywanie problemów z błędami ograniczania przepływności interfejsu API 

Azure Compute żądań może być ograniczone w subskrypcji i na podstawie regionu, aby pomóc w ogólnej wydajności usługi. Upewniamy się, że wszystkie wywołania do platformy Azure dostawcy zasobów obliczeniowych (CRP), który zarządza zasobami w przestrzeni nazw dostawcy Microsoft.Compute nie przekracza maksymalną dozwoloną liczba żądań interfejsu API. Ten dokument zawiera opis interfejsu API, ograniczanie przepustowości, szczegółowe informacje na temat rozwiązywania problemów ograniczania przepustowości i najlepsze rozwiązania w celu uniknięcia ograniczane.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Ograniczanie przepustowości przez usługi Azure Resource Manager, a dostawców zasobów  

Jako drzwi do platformy Azure usługi Azure Resource Manager wykonuje uwierzytelnianie i pierwszego rzędu sprawdzania poprawności i ograniczania przepustowości wszystkich przychodzących żądań interfejsu API. Limity szybkości wywołania w usłudze Azure Resource Manager i nagłówki powiązane diagnostyki odpowiedzi HTTP zostały opisane [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits).
 
Gdy klienta interfejsu API platformy Azure pobiera ograniczenie błędu, stan HTTP jest 429 zbyt wiele żądań. Aby dowiedzieć się, jeśli ograniczanie żądań, odbywa się przez usługi Azure Resource Manager lub podstawowego dostawcy zasobów, takich jak CRP, zbadaj `x-ms-ratelimit-remaining-subscription-reads` dla żądania GET i `x-ms-ratelimit-remaining-subscription-writes` nagłówki odpowiedzi dla żądań-GET. Jeśli pozostała liczba wywołań zbliża się do 0, został osiągnięty limit ogólne wywołania tej subskrypcji, zdefiniowane przez usługę Azure Resource Manager. Zliczane są ze sobą działań w oparciu o wszystkich subskrypcji klientów. W przeciwnym razie ograniczenie pochodzi z dostawcą zasobów docelowej (jeden odnoszącego `/providers/<RP>` segment adresu URL żądania). 

## <a name="call-rate-informational-response-headers"></a>Wywołaj nagłówki odpowiedzi informacyjny szybkości 

| Nagłówek                            | Format wartości                           | Przykład                               | Opis                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Pozostała liczba wywołań interfejsu API dla zasady ograniczania obejmujące przedział lub operacji grupę zasobów łącznie celem tego żądania                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Liczba wywołań zlicza "naliczane" dla tego żądania HTTP limitem odpowiednich zasad. Najczęściej jest to 1. Żądania wsadowe, takie jak skalowanie zestawu skalowania maszyn wirtualnych, mogą być obciążani wiele liczników. |


Należy zauważyć, że żądania interfejsu API można poddać wiele zasad ograniczania przepustowości. Będzie osobnym `x-ms-ratelimit-remaining-resource` nagłówka dla każdej zasady. 

Oto przykładowa odpowiedź, aby usunąć żądanie zestaw skali maszyny wirtualnej.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Ograniczanie szczegóły błędu

Stan HTTP 429 jest najczęściej używany do odrzucenia żądania, ponieważ osiągnięto limit szybkości wywołania. Typowe ograniczania błąd odpowiedzi od dostawcy zasobów obliczeniowych będzie wyglądać podobnie jak w poniższym przykładzie (wyświetlane są tylko odpowiednie nagłówki):

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

Pozostałe wywołania liczba 0 jest jednym z powodu której ograniczania błąd jest zwracany. W tym przypadku jest to `HighCostGet30Min`. Ogólny format treści odpowiedzi jest format ogólny błąd interfejsu API usługi Azure Resource Manager (zgodność z protokołem OData). Kod błędu głównego `OperationNotAllowed`, jeden używa dostawcę zasobów obliczeniowych, aby zgłaszać błędy (oraz inne rodzaje błędów klientów) ograniczania przepływności. `message` Właściwość wewnętrzne błędy zawiera strukturę serializacji JSON ze szczegółowymi informacjami o naruszenie ograniczenia przepustowości.

Jak pokazano powyżej, każdy błąd ograniczania obejmuje `Retry-After` nagłówka, który dostarcza klientowi minimalną liczbę sekund oczekiwania przed ponowieniem próby żądania. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Wywołanie interfejsu API szybkości i ograniczania przepustowości Błąd analizatora
Wersja zapoznawcza funkcji rozwiązywania problemów jest dostępna dla interfejsu API dostawcy zasobów obliczeniowych. Te polecenia cmdlet programu PowerShell, podaj statystyki dotyczące liczby żądań interfejsu API na przedział czasu dla operacji i ograniczania przepustowości naruszeń na grupy operacji (zasady):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Statystyki wywołania interfejsu API można zapewniają doskonałą wgląd w zachowania klientów w ramach subskrypcji i włączyć łatwą identyfikację wzorców wywołania, które powodują ograniczenia przepustowości.

To ograniczenie analizator przez pewien czas jest, czy nie powoduje liczenia żądania dla typów zasobów dysku i migawki (w odniesieniu do dysków zarządzanych). Ponieważ zbiera dane z telemetrii firmy CRP, jego również nie będzie mogła pomóc w identyfikacji błędów ograniczania dostępności z ARM. Jednak te mogą być identyfikowane łatwo zależnie od szczególne nagłówki odpowiedzi ARM, zgodnie z wcześniejszym opisem.

Polecenia cmdlet programu PowerShell używasz usługi interfejsu API REST, który można łatwo wywołać bezpośrednio przez klientów (chociaż posiadanie obsługi jeszcze). Aby wyświetlić format żądania HTTP, Uruchom polecenia cmdlet z - debugowania lub rozpoznanie, na ich wykonanie, przy użyciu programu Fiddler.


## <a name="best-practices"></a>Najlepsze rozwiązania 

- Nie należy wykonywać ponowień błędów interfejsu API usługi platformy Azure, bezwarunkowo i/lub od razu. Jest wystąpieniem typowe dla klienta kodu w pętli ponawiania szybkie po wystąpieniu błędu, który nie jest w stanie ponownych prób. Ponownych prób po pewnym czasie będzie wyczerpać limit dozwolony wywołania dla operacji docelowej grupy i mieć wpływ na innych klientów subskrypcji. 
- W przypadkach, mocno obciążające interfejsu API usługi automation należy wziąć pod uwagę Implementowanie aktywnego klienta własnym ograniczania, gdy liczba dostępnych wywołań dla grupy operacji docelowej nie spadnie poniżej niektóre progu dolnego. 
- Podczas śledzenia operacji asynchronicznych, należy przestrzegać nagłówka Retry-After wskazówek dotyczących serwerów. 
- Jeśli kod klienta wymaga informacji na temat konkretnej maszyny wirtualnej, Wyślij zapytanie do tej maszyny Wirtualnej bezpośrednio, zamiast wyświetlania listy wszystkich maszyn wirtualnych w grupie zasobów zawierającej lub całej subskrypcji, a następnie pobrania potrzebne maszyny Wirtualnej po stronie klienta. 
- Jeśli kod klienta wymaga maszyn wirtualnych, dysków i migawek z określonej lokalizacji platformy Azure, formularz oparte na lokalizacji kwerendy zamiast odpytywanie subskrypcji wszystkie maszyny wirtualne, a następnie filtrować według lokalizacji po stronie klienta: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` zapytanie, aby regionalne dostawcę zasobów obliczeniowych punkty końcowe. 
-   Podczas tworzenia lub aktualizowania zasobów interfejsu API, w szczególności, maszyny wirtualne i zestawy skalowania maszyn wirtualnych, jest znacznie bardziej efektywne do śledzenia operacji asynchronicznej zwrócone do zakończenia niż sondowanie na adres URL zasobu, sama (na podstawie `provisioningState`).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wytycznych ponawiania prób dla innych usług na platformie Azure, zobacz [ponów wskazówki dla określonych usług](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
