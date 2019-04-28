---
title: Pobieranie zasobów platformy Azure przy użyciu interfejsu API REST zdarzenia dotyczące kondycji | Dokumentacja firmy Microsoft
description: Użyj interfejsów API REST platformy Azure, aby pobrać zdarzenia kondycji zasobów platformy Azure.
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790916"
---
# <a name="get-resource-health-using-the-rest-api"></a>Pobierz kondycję zasobów przy użyciu interfejsu API REST 

W tym artykule przykład pokazuje, jak pobrać listę zdarzenia dotyczące kondycji zasobów platformy Azure w subskrypcji przy użyciu [interfejsu API REST usługi Azure](/rest/api/azure/).

Pełna dokumentacja i więcej przykładów dla interfejsu API REST są dostępne w [dokumentacja usługi Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Tworzenie żądania

Należy użyć następującego `GET` żądania HTTP, aby wyświetlić listę zdarzenia kondycji dla Twojej subskrypcji dla zakresu czasu między `2018-05-16` i `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacja:*|Wymagany. Ustawić prawidłową `Bearer` [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Name (Nazwa) | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, która identyfikuje subskrypcji platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| wersja interfejsu API | Wersja interfejsu API do użycia dla żądania.<br /><br /> W tym dokumencie opisano parametru api-version `2015-04-01`zawarte w powyższym adresie URL.  |
| $filter | Opcję filtrowania, aby zmniejszyć zestaw wyników zwrócony. Dostępne są dozwolone wzorce dla tego parametru [w odwołaniu dla operacji dzienników aktywności](/rest/api/monitor/activitylogs/list#uri-parameters). Przykład pokazany rejestruje wszystkie zdarzenia w zakresie czasu między 2018-05-16 i 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Nie treści żądania jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Z listy wartości zdarzenia kondycji odpowiadający parametr filtru wraz z zwróciła kod stanu 200 `nextlink` identyfikator URI do pobrania następnej strony wyników.

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
