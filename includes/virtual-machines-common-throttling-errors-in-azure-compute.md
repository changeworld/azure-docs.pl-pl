---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: changov
ms.service: virtual-machines
ms.topic: include
ms.date: 09/10/2018
ms.author: vashan, rajraj, changov
ms.custom: include file
ms.openlocfilehash: 5a9e0c86273895ccb26fab2a24239ce7aefd7eac
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724124"
---
Azure Compute żądań może być ograniczone w subskrypcji i na podstawie regionu, aby pomóc w ogólnej wydajności usługi. Upewniamy się, że wszystkie wywołania do usługi Azure obliczenia zasobów dostawcy (CRP), który zarządza zasobami w przestrzeni nazw dostawcy Microsoft.Compute nie przekracza maksymalną dozwoloną liczba żądań interfejsu API. Ten dokument zawiera opis interfejsu API, ograniczanie przepustowości, szczegółowe informacje na temat rozwiązywania problemów ograniczania przepustowości i najlepsze rozwiązania w celu uniknięcia ograniczane.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Ograniczanie przepustowości przez usługi Azure Resource Manager, a dostawców zasobów  

Jako drzwi do platformy Azure usługi Azure Resource Manager wykonuje uwierzytelnianie i pierwszego rzędu sprawdzania poprawności i ograniczania przepustowości wszystkich przychodzących żądań interfejsu API. Limity szybkości wywołania w usłudze Azure Resource Manager i nagłówki powiązane diagnostyki odpowiedzi HTTP zostały opisane [tutaj](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-request-limits).
 
Gdy klienta interfejsu API platformy Azure pobiera ograniczenie błędu, stan HTTP jest 429 zbyt wiele żądań. Aby dowiedzieć się, jeśli ograniczanie żądań, odbywa się przez usługi Azure Resource Manager lub podstawowego dostawcy zasobów, takich jak CRP, zbadaj `x-ms-ratelimit-remaining-subscription-reads` dla żądania GET i `x-ms-ratelimit-remaining-subscription-writes` nagłówki odpowiedzi dla żądań-GET. Jeśli pozostała liczba wywołań zbliża się do 0, został osiągnięty limit ogólne wywołania tej subskrypcji, zdefiniowane przez usługę Azure Resource Manager. Zliczane są ze sobą działań w oparciu o wszystkich subskrypcji klientów. W przeciwnym razie ograniczenie pochodzi z dostawcą zasobów docelowej (jeden odnoszącego `/providers/<RP>` segment adresu URL żądania). 

## <a name="call-rate-informational-response-headers"></a>Wywołaj nagłówki odpowiedzi informacyjny szybkości 

| Nagłówek                            | Format wartości                           | Przykład                               | Opis                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit — pozostałe — zasób |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Pozostała liczba wywołań interfejsu API dla zasady ograniczania obejmujące przedział lub operacji grupę zasobów łącznie celem tego żądania                                                                   |
| x-ms żądanie — opłata               | ```<count>   ```                             | 1                                     | Liczba wywołań zlicza "naliczane" dla tego żądania HTTP limitem odpowiednich zasad. Najczęściej jest to 1. Żądania wsadowe, takie jak skalowanie zestawu skalowania maszyn wirtualnych, mogą być obciążani wiele liczników. |


Należy zauważyć, że żądania interfejsu API można poddać wiele zasad ograniczania przepustowości. Będzie osobnym `x-ms-ratelimit-remaining-resource` nagłówka dla każdej zasady. 

Oto przykładowa odpowiedź, aby usunąć Maszynę wirtualną w żądaniu zestaw skali maszyny wirtualnej.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

##<a name="throttling-error-details"></a>Ograniczanie szczegóły błędu

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

Pozostałe wywołania liczba 0 jest jednym z powodu której ograniczania błąd jest zwracany. W tym przypadku jest to `HighCostGet30Min`. Ogólny format treści odpowiedzi jest format ogólny błąd interfejsu API usługi Azure Resource Manager (zgodność z protokołem OData). Kod błędu głównego `OperationNotAllowed`, jeden używa dostawcę zasobów obliczeniowych, aby zgłaszać błędy (oraz inne rodzaje błędów klientów) ograniczania przepływności. 

Jak pokazano powyżej, każdy błąd ograniczania obejmuje `Retry-After` nagłówka, który dostarcza klientowi minimalną liczbę sekund oczekiwania przed ponowieniem próby żądania. 

## <a name="best-practices"></a>Najlepsze praktyki 

- Nie należy wykonywać ponowień błędy usługi Azure API bezwarunkowo. Jest wystąpieniem typowe dla klienta kodu w pętli ponawiania szybkie po wystąpieniu błędu, który nie jest w stanie ponownych prób. Ponownych prób po pewnym czasie będzie wyczerpać limit dozwolony wywołania dla operacji docelowej grupy i mieć wpływ na innych klientów subskrypcji. 
- W przypadkach, mocno obciążające interfejsu API usługi automation należy wziąć pod uwagę Implementowanie aktywnego klienta własnym ograniczania, gdy liczba dostępnych wywołań dla grupy operacji docelowej nie spadnie poniżej niektóre progu dolnego. 
- Podczas śledzenia operacji asynchronicznych, należy przestrzegać nagłówka Retry-After wskazówek dotyczących serwerów. 
- Jeśli kod klienta potrzebuje informacji na temat konkretnej maszyny wirtualnej, zapytanie tej maszyny Wirtualnej bezpośrednio, zamiast wymieniać wszystkich maszyn wirtualnych w zawierające grupa zasobów lub całej subskrypcji i następnie pobrania potrzebne maszyny Wirtualnej po stronie klienta. 
- Jeśli kod klienta wymaga maszyn wirtualnych, dysków i migawek z określonej lokalizacji platformy Azure, formularz oparte na lokalizacji kwerendy zamiast odpytywanie subskrypcji wszystkie maszyny wirtualne, a następnie filtrować według lokalizacji po stronie klienta: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` i `/subscriptions/<subId>/providers/Microsoft.Compute/virtualMachines` zapytanie do obliczeń Dostawca regionalnych punktów końcowych zasobów. • Podczas tworzenia lub aktualizowania zasobów interfejsu API, w szczególności, maszyn wirtualnych i maszyn wirtualnych zestawów skalowania, jest znacznie bardziej efektywne śledzenie operacji asynchronicznej zwrócone do zakończenia niż sondowanie na adres URL zasobu, sama (na podstawie `provisioningState`).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wytycznych ponawiania prób dla innych usług na platformie Azure, zobacz [ponów wskazówki dla określonych usług](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific)