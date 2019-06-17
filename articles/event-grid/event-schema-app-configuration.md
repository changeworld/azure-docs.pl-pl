---
title: Schemat zdarzeń w usłudze Azure konfiguracji aplikacji usługi Azure siatki zdarzeń
description: Opisuje właściwości, które są dostarczane dla zdarzeń konfiguracji aplikacji platformy Azure za pomocą usługi Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735784"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schemat zdarzeń Azure Event Grid dla konfiguracji aplikacji usługi Azure

Ten artykuł zawiera właściwości i schematu dla zdarzeń konfiguracji aplikacji platformy Azure. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń konfiguracji aplikacji usługi Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Konfiguracja aplikacji platformy Azure generuje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Wywoływane po utworzeniu lub zastąpić pary klucz wartość. |
| Microsoft.AppConfiguration.KeyValueDeleted | Wywoływane, gdy klucz wartość zostanie usunięta. |

## <a name="example-event"></a>Przykład zdarzenia

Poniższy przykład przedstawia schematu to zdarzenie zmodyfikowane pary klucz wartość: 

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

Schemat zdarzenia usuniętego pary klucz wartość są podobne: 

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

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| topic | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| subject | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Dane zdarzenia konfiguracji aplikacji. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| key | string | Klucz klucz wartość, która została zmieniona lub usunięta. |
| label | string | Etykieta, jeśli istnieje klucz wartość, która została zmieniona lub usunięta. |
| etag | string | Aby uzyskać `KeyValueModified` tag etag nowy klucz wartość. Aby uzyskać `KeyValueDeleted` tag etag klucz wartość, która została usunięta. |
 
## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Wprowadzenie do pracy ze zdarzeniami konfiguracji aplikacji platformy Azure, zobacz [zdarzenia konfiguracji aplikacji usługi Azure Route — interfejs wiersza polecenia Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 