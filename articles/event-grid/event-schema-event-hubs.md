---
title: Azure schematu zdarzeń centra zdarzeń usługi Event Grid
description: Opisuje właściwości, które są dostarczane dla centrów zdarzeń za pomocą usługi Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: e301f3895126ed52b8d4c1f046f69dfcedb3563c
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055209"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schemat zdarzeń Azure Event Grid dla usługi event hubs

Ten artykuł zawiera właściwości i schematu dla centrów zdarzeń. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń usługi Event Hubs](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Zdarzenie dostępne typy

Usługa Event Hubs emituje **Microsoft.EventHub.CaptureFileCreated** typ zdarzenia, gdy tworzony jest plik przechwytywania.

## <a name="example-event"></a>Przykład zdarzenia

To zdarzenie próbki przedstawia schematu event hubs Zdarzenie wywoływane, gdy funkcja przechwytywania są przechowywane w pliku: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia Centrum zdarzeń. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| fileUrl | ciąg | Ścieżka do pliku przechwytywania. |
| Typ pliku | ciąg | Typ pliku pliku przechwytywania. |
| Identyfikator partycji | ciąg | Identyfikator fragmentu. |
| sizeInBytes | liczba całkowita | Rozmiar pliku. |
| eventCount | liczba całkowita | Liczba zdarzeń w pliku. |
| firstSequenceNumber | liczba całkowita | Najmniejsza liczba sekwencji z kolejki. |
| lastSequenceNumber | liczba całkowita | Ostatni numer sekwencji z kolejki. |
| firstEnqueueTime | ciąg | Po raz pierwszy z kolejki. |
| lastEnqueueTime | ciąg | Czas ostatniego z kolejki. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby uzyskać informacje na temat obsługi centrów zdarzeń, zobacz [Stream danych big data do magazynu danych](event-grid-event-hubs-integration.md).