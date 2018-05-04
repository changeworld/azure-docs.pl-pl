---
title: Azure schematu zdarzeń siatki zdarzeń
description: Opisuje właściwości, które są dostępne dla zdarzeń o Azure zdarzeń siatki
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/17/2018
ms.author: babanisa
ms.openlocfilehash: cb7797f5322b9288faf96be2ede164f156fd66cc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="azure-event-grid-event-schema"></a>Azure schematu zdarzeń siatki zdarzeń

W tym artykule opisano właściwości i schematu, które znajdują się dla wszystkich zdarzeń. Zdarzenia składają się z zbiór właściwości pięć wymaganych parametrów obiektu wymaganych danych. Właściwości są wspólne dla wszystkich zdarzeń z dowolnego wydawcę. Obiekt danych zawiera właściwości, które są specyficzne dla każdego wydawcy. Tematy systemu te właściwości są specyficzne dla dostawcy zasobu, takiego jak magazyn Azure lub usługi Azure Event Hubs.

Źródła zdarzeń wysyłania zdarzeń do siatki zdarzeń Azure w tablicy, która może zawierać wiele obiektów zdarzeń. Zaksięgowania zdarzenia do zdarzenia tematu siatki, Tablica może mieć całkowity rozmiar maksymalnie 1 MB. Każde zdarzenie w tablicy jest ograniczony do 64 KB. Jeśli zdarzenie lub tablicy przekracza ograniczenia rozmiaru, pojawi się odpowiedź **413 ładunku zbyt duży**.

Siatka zdarzeń wysyła zdarzenia do subskrybentów w tablicy, która zawiera pojedyncze zdarzenie. To zachowanie mogą ulec zmianie w przyszłości.

Dla zdarzeń siatki zdarzeń i ładunek danych każdy wydawca Azure można znaleźć schematu JSON [magazynie schematów zdarzeń](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schemat zdarzeń

W poniższym przykładzie przedstawiono właściwości, które są używane przez wszystkich wydawców zdarzeń:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Na przykład schemat opublikowana na potrzeby zdarzenia magazynu obiektów Blob platformy Azure jest:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia zawierają tych samych danych najwyższego poziomu następujące:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalny. Zdarzenie siatki udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana wydawcy podmiotem zdarzeń. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzenia. |
| eventTime | ciąg | Czas jest generowane zdarzenie oparte na czas UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia specyficzne dla dostawcy zasobów. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersji schematu. |
| Element metadataVersion | ciąg | Wersja schematu metadanych zdarzeń. Zdarzenie siatki definiuje schemat właściwości najwyższego poziomu. Zdarzenie siatki udostępnia tę wartość. |

Aby dowiedzieć się więcej na temat właściwości w obiekcie danych, zobacz źródło zdarzenia:

* [Subskrypcje platformy Azure (operacje zarządzania)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [Service Bus](event-schema-service-bus.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Grupy zasobów (operacje zarządzania)](event-schema-resource-groups.md)

W przypadku niestandardowych tematów wydawca zdarzeń określa obiekt danych. Danych najwyższego poziomu powinien zawierać te same pola jako standardowych zdarzeń zdefiniowanych zasobów.

Podczas publikowania zdarzeń w niestandardowych tematów, utworzyć tematy dla zdarzeń, które ułatwiają subskrybentów dowiedzieć się, czy są zainteresowane w zdarzeniu. Subskrybenci używać podmiotu na zdarzenia filtru i trasy. Rozważ podanie ścieżki, na którym wystąpiło zdarzenie, aby subskrybenci można filtrować według segmentów tej ścieżki. Ścieżka pozwala subskrybentom ściśle lub szeroko filtrować zdarzenia. Na przykład podaj ścieżkę trzy segmentu jak `/A/B/C` w tym temacie, subskrybentów można filtrować według pierwszy segment `/A` uzyskać szeroką gamę zdarzenia. Tych subskrybentów pobrać zdarzenia z tematów, takich jak `/A/B/C` lub `/A/D/E`. Inne subskrybentów można filtrować według `/A/B` można pobrać mniejszą niż zestaw zdarzeń.

Czasami temat potrzebuje więcej szczegółów na temat co się stało. Na przykład **kont magazynu** wydawcy zawiera temat `/blobServices/default/containers/<container-name>/blobs/<file>` po dodaniu pliku do kontenera. Subskrybent można filtrować według ścieżki `/blobServices/default/containers/testcontainer` uzyskać wszystkie zdarzenia dla tego kontenera, ale nie innych kontenerów na koncie magazynu. Subskrybent można również filtrować lub trasy sufiksem `.txt` aby działał tylko z plików tekstowych.

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do usługi Azure Event siatki, zobacz [co to jest zdarzenie siatki?](overview.md)
* Aby uzyskać więcej informacji o tworzeniu subskrypcji platformy Azure zdarzeń siatki, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).
