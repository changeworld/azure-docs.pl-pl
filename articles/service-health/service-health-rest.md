---
title: Pobierz zdarzenia kondycji zasobów platformy Azure przy użyciu interfejsu API REST | Dokumenty firmy Microsoft
description: Użyj interfejsów API rest platformy Azure, aby uzyskać zdarzenia kondycji dla zasobów platformy Azure.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654005"
---
# <a name="get-resource-health-using-the-rest-api"></a>Pobierz kondycję zasobów przy użyciu interfejsu API REST 

W tym przykładzie pokazano, jak pobrać listę zdarzeń kondycji dla zasobów platformy Azure w ramach subskrypcji przy użyciu [interfejsu API rest platformy Azure.](/rest/api/azure/)

Pełna dokumentacja referencyjna i dodatkowe przykłady interfejsu API REST są dostępne w [odwołaniu rest usługi Azure Monitor.](/rest/api/monitor) 

## <a name="build-the-request"></a>Tworzenie żądania

Użyj następującego `GET` żądania HTTP, aby wyświetlić listę zdarzeń `2018-05-16` kondycji `2018-06-20`subskrypcji dla zakresu czasu między i .

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw na prawidłowy  [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Nazwa | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, który identyfikuje subskrypcję platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | Wersja interfejsu API do użycia dla żądania.<br /><br /> Ten dokument obejmuje `2015-04-01`wersję api, zawartą w powyższym adresie URL.  |
| $filter | Opcja filtrowania w celu zmniejszenia zestawu zwróconych wyników. Dopuszczalne wzorce dla tego parametru są dostępne [w odwołaniu dla operacji Dzienniki aktywności](/rest/api/monitor/activitylogs/list#uri-parameters). Pokazany przykład przechwytuje wszystkie zdarzenia w zakresie czasu między 2018-05-16 i 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Dla tej operacji nie jest potrzebna żadna treść żądania.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Kod stanu 200 jest zwracany z listą wartości zdarzeń kondycji `nextlink` odpowiadających parametrowi filtru wraz z identyfikatorem URI w celu pobrania następnej strony wyników.

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
