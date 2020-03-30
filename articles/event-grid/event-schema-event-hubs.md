---
title: Schemat zdarzenia centrum zdarzeń usługi Azure Event Grid
description: Zawiera opis właściwości, które są dostarczane dla zdarzeń centrum zdarzeń za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561832"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schemat zdarzeń usługi Azure Event Grid dla centrów zdarzeń

Ten artykuł zawiera właściwości i schemat zdarzeń centrów zdarzeń.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [Źródło zdarzeń Centrum zdarzeń](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Centrum zdarzeń emituje typ zdarzenia **Microsoft.EventHub.CaptureFileCreated** podczas tworzenia pliku przechwytywania.

## <a name="example-event"></a>Przykładowe zdarzenie

To przykładowe zdarzenie pokazuje schemat zdarzenia centrum zdarzeń wywoływane, gdy funkcja przechwytywania przechowuje plik: 

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

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzeń centrum zdarzeń. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| plikUrl | ciąg | Ścieżka do pliku przechwytywania. |
| Filetype | ciąg | Typ pliku przechwytywania. |
| Partitionid | ciąg | Identyfikator niezależnego fragmentu. |
| rozmiarWzdjęty | liczba całkowita | Rozmiar pliku. |
| eventCount | liczba całkowita | Liczba zdarzeń w pliku. |
| pierwszaSequenceNumber | liczba całkowita | Najmniejszy numer sekwencyjny z kolejki. |
| lastSequenceNumber | liczba całkowita | Ostatni numer sekwencyjny z kolejki. |
| firstEnqueueTime | ciąg | Pierwszy raz z kolejki. |
| lastEnqueueTime | ciąg | Ostatni raz z kolejki. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby uzyskać informacje dotyczące obsługi zdarzeń w centrach zdarzeń, zobacz [Przesyłanie strumieniowe dużych zbiorów danych do magazynu danych](event-grid-event-hubs-integration.md).