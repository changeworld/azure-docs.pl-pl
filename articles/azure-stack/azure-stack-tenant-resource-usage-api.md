---
title: Interfejs API użycia zasobów dzierżawy | Dokumentacja firmy Microsoft
description: Odwołanie do użycia zasobów interfejsu API, który pobrać informacje o użyciu usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ab5dad550e590cd70f54ad5c8d4727d0f6370190
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379716"
---
# <a name="tenant-resource-usage-api"></a>Interfejs API użycia zasobów dzierżawy

Dzierżawca użyć interfejs API dzierżawcy do wyświetlania danych użycia zasobów przez dzierżawy. Ten interfejs API jest zgodne z interfejsem API użycia platformy Azure (obecnie dostępna w prywatnej wersji zapoznawczej).

Można użyć polecenia cmdlet programu Windows PowerShell **Get UsageAggregates** można pobrać danych użycia, takich jak na platformie Azure.

## <a name="api-call"></a>Wywołanie interfejsu API
### <a name="request"></a>Żądanie
Żądanie pobiera szczegóły użycia dla żądanego subskrypcje i żądany przedział czasu. Nie treści żądania nie istnieje.

| **— Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumenty
| **Argument** | **Opis** |
| --- | --- |
| *armendpoint* |Usługa Azure Resource Manager punktu końcowego środowiskiem usługi Azure Stack. Konwencja usługi Azure Stack to, że nazwa punktu końcowego usługi Azure Resource Manager jest w formacie `https://management.{domain-name}`. Na przykład dla zestawu SDK, nazwa domeny jest local.azurestack.external, a następnie punkt końcowy usługi Resource Manager jest `https://management.local.azurestack.external`. |
| *subId* |Identyfikator subskrypcji użytkownika, który wykonuje wywołanie. Można użyć tego interfejsu API tylko do zapytania do użycia w ramach jednej subskrypcji. Dostawcy mogą wykorzystywać interfejs API użycia zasobów dostawcy do użycia zapytania dla wszystkich dzierżaw. |
| *reportedStartTime* |Uruchomienie zapytania. Wartość *daty/godziny* powinna być w formacie UTC i na początku godziny, na przykład, 13:00. Codzienne agregacji należy ustawić tę wartość na północy czasu UTC. Format jest *poprzedzone znakiem zmiany znaczenia* ISO 8601, na przykład 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, gdzie dwukropek jest zmieniany na % 3a i plus jest zmieniany na % 2b, tak, aby identyfikator URI przyjazna. |
| *reportedEndTime* |Godzina zakończenia kwerendy. Ograniczenia, które są stosowane do *reportedStartTime* mają zastosowanie również do tego argumentu. Wartość *reportedEndTime* nie może przypadać w przyszłości. |
| *aggregationGranularity* |Opcjonalnym parametrem, który ma dwie osobne wartości potencjalnych: dni i godzin. Jako wartości wskazują, jeden zwraca dane w codziennych stopień szczegółowości, a drugi to rozpoznawanie co godzinę. Opcja codzienne jest ustawieniem domyślnym. |
| *wersja interfejsu API* |Wersja protokołu, który jest używany do wykonania tego żądania. Należy użyć 2015-06-01-preview. |
| *continuationToken* |Token jest pobierany z ostatniego wywołania, interfejs API użycia dostawcy. Token ten jest wymagane, gdy odpowiedź jest większa niż 1000 wierszy i działa jako zakładki uzyskać informacje o postępie. Jeśli nie występuje, dane są pobierane z początku dnia lub godziny, w oparciu o stopień szczegółowości przekazana. |

### <a name="response"></a>Odpowiedź
Pobierz /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = codziennie & wersja api-version = 1,0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Szczegóły odpowiedzi
| **Argument** | **Opis** |
| --- | --- |
| *id* |Unikatowy identyfikator użycia agregacji |
| *Nazwa* |Nazwa użycia agregacji |
| *type* |Definicja zasobu |
| *Identyfikator subskrypcji* |Identyfikator subskrypcji platformy Azure użytkownika |
| *usageStartTime* |Godzina przedział użycia, do której należy ta wartość zagregowana użycia rozpoczęcia UTC |
| *usageEndTime* |Godzina zakończenia UTC zasobnika użycia, do której należy ta wartość zagregowana użycia |
| *instanceData* |Pary klucz wartość Szczegóły wystąpienia (w nowym formacie):<br>  *Identyfikator URI zasobu*: w pełni kwalifikowany identyfikator zasobu, łącznie z grupami zasobów i nazwę wystąpienia <br>  *Lokalizacja*: Region, w którym ta usługa została uruchomiona <br>  *tagi*: tagi zasobów, które użytkownik określa <br>  *additionalInfo*: bardziej szczegółowe informacje dotyczące zasobów, który został wykorzystany, na przykład typ wersji lub obrazu systemu operacyjnego |
| *Ilość* |Ilość użycia zasobów, które wystąpiły w tym przedziale czasu |
| *meterId* |Unikatowy identyfikator zasobu, który został wykorzystany (nazywane również *ResourceID*) |


## <a name="next-steps"></a>Kolejne kroki
[Interfejs API użycia zasobów dostawcy](azure-stack-provider-resource-api.md)

[— Często zadawane pytania dotyczące wykorzystania](azure-stack-usage-related-faq.md)

