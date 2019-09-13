---
title: Schemat subskrypcji usługi Azure Event Grid
description: Opisuje właściwości subskrybowanie zdarzenia za pomocą usługi Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 6129c7f498ce6c52fce4266f693c6a304642f8c3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845457"
---
# <a name="event-grid-subscription-schema"></a>Schemat subskrypcji siatki zdarzeń

Aby utworzyć subskrypcję usługi Event Grid, Wyślij żądanie do operacji tworzenia zdarzeń subskrypcji. Użyj następującego formatu:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Na przykład, aby utworzyć subskrypcję zdarzeń dla konta magazynu o nazwie `examplestorage` w grupie zasobów o nazwie `examplegroup`, użyj następującego formatu:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Nazwa subskrypcji zdarzeń musi mieć długość 3 – 64 znaków i może zawierać tylko a – z, A-Z, 0-9 i "-". W artykule opisano, właściwości i schematu dla treści żądania.
 
## <a name="event-subscription-properties"></a>Właściwości subskrypcji zdarzeń

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| destination | object | Obiekt, który definiuje punkt końcowy. |
| filter | object | Opcjonalne pole do filtrowania typów zdarzeń. |

### <a name="destination-object"></a>obiekt docelowy

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| endpointType | string | Typ punktu końcowego dla subskrypcji (webhook/HTTP, Centrum zdarzeń lub kolejki). | 
| endpointUrl | string | Docelowy adres URL zdarzenia w tej subskrypcji zdarzeń. | 

### <a name="filter-object"></a>obiekt filtru

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| includedEventTypes | tablica | Dopasowanie, gdy typ zdarzenia w komunikacie zdarzenia jest dokładne dopasowanie do jednego z tych nazw typu event. Zgłasza błąd, gdy nazwa zdarzenia jest niezgodny z nazwy typu zdarzenia zarejestrowane dla źródła zdarzenia. Domyślna jest zgodna wszystkich typów zdarzeń. |
| subjectBeginsWith | ciąg | Dopasowanie prefiksu filtr zdarzeń do pola temat wiadomości. Domyślne lub pusty ciąg pasuje do wszystkich. | 
| subjectEndsWith | ciąg | Sufiks match filtr zdarzeń do pola temat wiadomości. Domyślne lub pusty ciąg pasuje do wszystkich. |
| isSubjectCaseSensitive | ciąg | Kontrolki jest rozróżniana wielkość liter, pasujące do filtrów. |


## <a name="example-subscription-schema"></a>Przykład schemat subskrypcji

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
