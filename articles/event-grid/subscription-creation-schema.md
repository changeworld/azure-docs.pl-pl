---
title: Schemat subskrypcji usługi Azure Event Grid
description: W tym artykule opisano właściwości subskrybowania zdarzenia za pomocą usługi Azure Event Grid. Schemat subskrypcji usługi Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720762"
---
# <a name="event-grid-subscription-schema"></a>Schemat subskrypcji usługi Event Grid

Aby utworzyć subskrypcję usługi Event Grid, należy wysłać żądanie do operacji tworzenie subskrypcji zdarzenia. Użyj następującego formatu:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Na przykład, aby utworzyć subskrypcję zdarzeń `examplestorage` dla konta `examplegroup`magazynu o nazwie w grupie zasobów o nazwie , użyj następującego formatu:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Nazwa subskrypcji zdarzeń musi mieć długość od 3 do 64 znaków i może zawierać tylko a-z, A-Z, 0-9 i "-". W tym artykule opisano właściwości i schemat dla treści żądania.
 
## <a name="event-subscription-properties"></a>Właściwości subskrypcji zdarzeń

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| destination | obiekt | Obiekt, który definiuje punkt końcowy. |
| filtr | obiekt | Opcjonalne pole do filtrowania typów zdarzeń. |

### <a name="destination-object"></a>obiekt docelowy

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Endpointtype | ciąg | Typ punktu końcowego dla subskrypcji (element webhook/HTTP, Event Hub lub kolejki). | 
| punkt końcowyUrl | ciąg | Docelowy adres URL zdarzeń w tej subskrypcji wydarzenia. | 

### <a name="filter-object"></a>obiekt filtracyjny

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| uwzględnionotyty | tablica | Dopasuj, gdy typ zdarzenia w komunikacie o zdarzeniu jest dokładnym dopasowaniem do jednej z tych nazw typów zdarzeń. Wywołuje błąd, gdy nazwa zdarzenia nie jest zgodna z nazwami zarejestrowanych typów zdarzeń dla źródła zdarzeń. Domyślnie dopasowuje wszystkie typy zdarzeń. |
| tematZaczynajZ | ciąg | Filtr dopasowania prefiksu do pola tematu w komunikacie o zdarzeniu. Domyślny lub pusty ciąg pasuje do wszystkich. | 
| tematWniesz | ciąg | Filtr dopasowania sufiksu do pola tematu w komunikacie o zdarzeniu. Domyślny lub pusty ciąg pasuje do wszystkich. |
| isSubjectCaseSensitive | ciąg | Steruje dopasowaniem rozróżniania wielkości liter dla filtrów. |


## <a name="example-subscription-schema"></a>Przykładowy schemat subskrypcji

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

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do siatki zdarzeń, zobacz [Co to jest siatka zdarzeń?](overview.md)
