---
title: Schemat zdarzenia usługi Azure Event Grid konfiguracji aplikacji platformy Azure
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń konfiguracji aplikacji platformy Azure za pomocą usługi Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735784"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schemat zdarzenia usługi Azure Event Grid dla konfiguracji aplikacji platformy Azure

Ten artykuł zawiera właściwości i schemat zdarzeń konfiguracji aplikacji platformy Azure. Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [Źródło zdarzeń konfiguracji aplikacji platformy Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Azure App Configuration emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Wywoływane po utworzeniu lub zastąpieniu wartości klucza. |
| Microsoft.AppConfiguration.KeyValueDeleted | Wywoływane po usunięciu wartości klucza. |

## <a name="example-event"></a>Przykładowe zdarzenie

Poniższy przykład przedstawia schemat zdarzenia zmodyfikowanego wartości klucza: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schemat usuniętego zdarzenia wartości klucza jest podobny: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia konfiguracji aplikacji. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| key | ciąg | Klucz klucza wartość, który został zmodyfikowany lub usunięty. |
| label | ciąg | Etykieta, jeśli istnieje, wartości klucza, który został zmodyfikowany lub usunięty. |
| Etag | ciąg | Dla `KeyValueModified` etag nowej wartości klucza. Dla `KeyValueDeleted` etag wartości klucza, który został usunięty. |
 
## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby zapoznać się ze zmianami dotyczących pracy z konfiguracją aplikacji platformy Azure, zobacz [Kierowanie zdarzeń konfiguracji aplikacji platformy Azure — Interfejsu wiersza polecenia platformy Azure.](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) 