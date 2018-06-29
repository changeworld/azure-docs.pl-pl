---
title: Interfejs API użycia zasobów dzierżawy | Dokumentacja firmy Microsoft
description: Odwołania do użycia zasobów interfejsu API, który pobrać informacji o użyciu stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 8472d8ce733c07641a7fa6d53aeb6909cd709990
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048394"
---
# <a name="tenant-resource-usage-api"></a>Interfejs API użycia zasobów dzierżawcy

Dzierżawcy służy interfejs API dzierżawcy do wyświetlania danych o użyciu zasobów własne dzierżawy. Ten interfejs API jest zgodna z użycia interfejsu API Azure (obecnie w podglądzie prywatnym).

Można użyć polecenia cmdlet programu Windows PowerShell **Get UsageAggregates** można pobrać danych użycia, na przykład na platformie Azure.

## <a name="api-call"></a>Wywołanie interfejsu API
### <a name="request"></a>Żądanie
Żądanie pobiera szczegóły zużycia dla żądanego subskrypcje i żądanej przedziale czasu. Nie ma żadnych treści żądania.

| **— Metoda** | **Identyfikator URI żądania** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumenty
| **Argument** | **Opis** |
| --- | --- |
| *armendpoint* |Menedżer zasobów punktu końcowego platformy Azure środowiska Azure stosu. Konwencja stosu Azure jest, że nazwa punktu końcowego usługi Azure Resource Manager jest w formacie `https://management.{domain-name}`. Na przykład dla zestawu SDK, nazwa domeny jest local.azurestack.external, a następnie punkt końcowy Menedżera zasobów jest `https://management.local.azurestack.external`. |
| *subId* |Identyfikator subskrypcji użytkownika, który jest wywołania. Ten interfejs API tylko dla zapytania można użyć do użycia z jedną subskrypcją. Dostawców można użyć interfejsu API użycia zasobów dostawcy do użycia zapytania dla wszystkich dzierżawców. |
| *reportedStartTime* |Uruchomienie zapytania. Wartość *DateTime* powinna być w formacie UTC i na początku godziny, na przykład 13:00. Codzienne agregacji ta wartość północy czasu UTC. Format jest *wpisywany* ISO 8601, na przykład 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, gdzie dwukropek została zmieniona na % 3a i plus została zmieniona, % 2b tak, aby przyjazną identyfikatora URI. |
| *reportedEndTime* |Godzina zakończenia zapytania. Ograniczenia, które dotyczą *reportedStartTime* mają też zastosowanie do tego argumentu. Wartość *reportedEndTime* nie może być w przyszłości. |
| *aggregationGranularity* |Opcjonalny parametr, który ma dwa odrębne wartości potencjalnych: dni i godzin. Jako wartości sugerują, jeden zwraca w dzienne dane, a drugi to rozpoznawanie co godzinę. Opcja codzienne jest ustawieniem domyślnym. |
| *wersja interfejsu API* |Wersja protokołu, który służy do zgłoszenia tego żądania. Należy użyć 2015-06-01-preview. |
| *continuationToken* |Token jest pobierana z ostatnim wywołaniem dostawcy interfejsu API użycia. Token ten jest potrzebna, gdy odpowiedź jest większa niż 1000 wierszy i działa jako zakładki dla postępu. Jeśli nie istnieje, dane są pobierane z początku dnia lub przekazano godzinę, w oparciu o stopień szczegółowości. |

### <a name="response"></a>Odpowiedź
Pobierz /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = codziennie & api-version = 1.0

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
| *Nazwa* |Nazwa wartości zagregowanej użycia |
| *type* |Definicja zasobu |
| *Identyfikator subskrypcji* |Identyfikator subskrypcji platformy Azure użytkownika |
| *usageStartTime* |Czas UTC uruchomienie zasobnika użycia, do którego należy ta wartość zagregowana użycia |
| *usageEndTime* |Godzina zakończenia UTC łańcucha użycia, do którego należy ta wartość zagregowana użycia |
| *instanceData* |Pary klucz wartość Szczegóły wystąpienia (w formacie nowych):<br>  *resourceUri*: pełni kwalifikowany identyfikator zasobu, łącznie z grupami zasobów i nazwa wystąpienia <br>  *Lokalizacja*: regionu, w którym uruchomienia tej usługi <br>  *tagi*: tagi zasobów, które określa użytkownika <br>  *części informacje dodatkowe aby*: więcej szczegółowych informacji o zasobu, który został wykorzystany, na przykład typ wersji lub obrazu systemu operacyjnego |
| *ilość* |Ilość zużycia zasobów, które wystąpiły w tym przedziale czasu |
| *meterId* |Unikatowy identyfikator zasobu, który został wykorzystany (nazywane również *ResourceID*) |


## <a name="next-steps"></a>Kolejne kroki
[Interfejs API użycia zasobów dostawcy](azure-stack-provider-resource-api.md)

[Często zadawane pytania dotyczące wykorzystania](azure-stack-usage-related-faq.md)

