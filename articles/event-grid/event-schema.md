---
title: Schemat zdarzeń w usłudze Azure Event Grid
description: Opisuje właściwości, które są dostarczane dla zdarzeń za pomocą usługi Azure Event Grid
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 8a8193d21bbc1d0af933657705e605ce31589cbf
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785849"
---
# <a name="azure-event-grid-event-schema"></a>Schemat zdarzeń w usłudze Azure Event Grid

W tym artykule opisano, właściwości i schematu, które znajdują się dla wszystkich zdarzeń. Zdarzenia składają się z zbiór właściwości pięć wymaganych parametrów i obiekt wymaganych danych. Właściwości są wspólne dla wszystkich zdarzeń z dowolnego wydawcę. Obiekt danych posiada właściwości, które są specyficzne dla każdego wydawcy. Tematy systemu te właściwości są specyficzne dla dostawcy zasobów, takich jak usługa Azure Storage lub Azure Event Hubs.

Źródła zdarzeń wysyła zdarzenia do usługi Azure Event Grid w tablicy, który może mieć kilka obiektów zdarzeń. Ogłaszając zdarzenia do tematu usługi event grid tablicy może mieć całkowity rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczony do 64 KB (ogólnie) lub 1 MB (wersja zapoznawcza). Jeśli zdarzenia lub tablicy jest większy niż limity rozmiaru, pojawi się odpowiedź **413 ładunku duży**.

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęty przez umowy poziomu usług (SLA) ogólna dostępność (GA). Pomoc techniczna dotycząca zdarzenie o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej. Zdarzenia przekracza 64 KB są naliczane w przyrostach 64 KB. 

Usługa Event Grid wysyła zdarzenia do subskrybentów w tablicy, która ma pojedyncze zdarzenie. To zachowanie, mogą ulec zmianie w przyszłości.

Dla zdarzeń usługi Event Grid i ładunek danych każdego wydawcy platformy Azure można znaleźć schematu JSON [magazynie schematów zdarzeń](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schemat zdarzeń

Poniższy przykład pokazuje właściwości, które są używane przez wszystkich wydawców zdarzeń:

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

Na przykład jest schematu opublikowane zdarzenia magazynu obiektów Blob platformy Azure:

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

Wszystkie zdarzenia mają te same dane następujących najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| topic | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| subject | ciąg | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| data | object | Dane zdarzenia specyficzne dla dostawcy zasobów. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Aby dowiedzieć się więcej na temat właściwości w obiekcie danych, zobacz źródło zdarzenia:

* [Subskrypcje platformy Azure (operacje zarządzania)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupy zasobów (operacje zarządzania)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)

Niestandardowe tematy wydawca zdarzeń określa obiekt danych. Najwyższego poziomu dane powinny mieć te same pola jako standardowych zdarzeń zdefiniowanych przez zasób.

Podczas publikowania zdarzeń w tematy niestandardowe, należy utworzyć tematy dla zdarzeń, które ułatwiają subskrybentów dowiedzieć się, czy są one zainteresowani zdarzenia. Subskrybenci użyć do filtrowania i trasy zdarzenia tematu. Rozważ podanie ścieżki dla której zaszło zdarzenie, dzięki czemu subskrybenci można filtrować według segmentami tej ścieżki. Ścieżka pozwala subskrybentom wąskiego lub szeroko filtrowanie zdarzeń. Na przykład, jeśli należy podać ścieżkę trzy segmentu, takie jak `/A/B/C` w tym temacie, subskrybenci można filtrować według pierwszy segment `/A` uzyskać szeroką gamę zdarzenia. Te zdarzenia jest subskrybenci z tematów, takich jak `/A/B/C` lub `/A/D/E`. Innymi subskrybentami, można filtrować według `/A/B` Aby uzyskać bardziej szczegółowy zestaw zdarzeń.

Czasami tematu potrzebuje więcej szczegółów na temat co się stało. Na przykład **kont magazynu** wydawcy zawiera temat `/blobServices/default/containers/<container-name>/blobs/<file>` po dodaniu pliku do kontenera. Subskrybent można filtrować według ścieżki `/blobServices/default/containers/testcontainer` można pobrać wszystkie zdarzenia dla tego kontenera, ale nie innych kontenerów na koncie magazynu. Subskrybent można również filtrować lub tras po przyrostku `.txt` aby działał tylko w przypadku plików tekstowych.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
