---
title: Azure schematu zdarzeń centra zdarzeń siatki zdarzeń
description: Opisuje właściwości, które są dostępne dla zdarzeń centra zdarzeń Azure zdarzeń siatki
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6fac70a8837245e6d60a5a61db8a22ab718d4b49
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34302665"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure schematu zdarzeń siatki zdarzeń usługi event hubs

Ten artykuł zawiera właściwości i schematu dla zdarzeń centrów zdarzeń. Aby obejrzeć wprowadzenie do schematów zdarzeń, zobacz [schematu zdarzeń siatki zdarzeń Azure](event-schema.md).

### <a name="available-event-types"></a>Typy dostępnych zdarzeń

Centra zdarzeń emituje **Microsoft.EventHub.CaptureFileCreated** typ zdarzenia podczas tworzenia pliku przechwytywania.

## <a name="example-event"></a>Przykład zdarzeń

To zdarzenie próbkowania zawiera schemat centra zdarzeń zdarzenia wywoływane, gdy funkcja przechwytywania są przechowywane w pliku: 

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
| Temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalny. Zdarzenie siatki udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana wydawcy podmiotem zdarzeń. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzenia. |
| eventTime | ciąg | Czas jest generowane zdarzenie oparte na czas UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia Centrum zdarzeń. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca definiuje wersji schematu. |
| Element metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Zdarzenie siatki definiuje schemat właściwości najwyższego poziomu. Zdarzenie siatki udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| fileUrl | ciąg | Ścieżka do pliku przechwytywania. |
| Typ pliku | ciąg | Typ pliku przechwytywania. |
| partitionId | ciąg | Identyfikator niezależnego fragmentu. |
| sizeInBytes | liczba całkowita | Rozmiar pliku. |
| eventCount | liczba całkowita | Liczba zdarzeń w pliku. |
| firstSequenceNumber | liczba całkowita | Najmniejsza liczba sekwencji z kolejki. |
| lastSequenceNumber | liczba całkowita | Ostatni numer sekwencji z kolejki. |
| firstEnqueueTime | ciąg | Przy pierwszym z kolejki. |
| lastEnqueueTime | ciąg | Czas ostatniego z kolejki. |

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do usługi Azure Event siatki, zobacz [co to jest zdarzenie siatki?](overview.md)
* Aby uzyskać więcej informacji o tworzeniu subskrypcji platformy Azure zdarzeń siatki, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).
* Aby uzyskać informacje na temat obsługi zdarzeń centra zdarzeń, zobacz [strumienia danych big data do magazynu danych](event-grid-event-hubs-integration.md).