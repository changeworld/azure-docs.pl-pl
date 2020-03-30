---
title: Schemat zdarzenia usługi Azure Event Grid
description: Opisuje właściwości i schemat, które są obecne dla wszystkich zdarzeń.Zdarzenia składają się z zestawu pięciu wymaganych właściwości ciągu i wymaganego obiektu danych.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244838"
---
# <a name="azure-event-grid-event-schema"></a>Schemat zdarzenia usługi Azure Event Grid

W tym artykule opisano właściwości i schemat, które są obecne dla wszystkich zdarzeń.Zdarzenia składają się z zestawu pięciu wymaganych właściwości ciągu i wymaganego obiektu danych. Właściwości są wspólne dla wszystkich zdarzeń z dowolnego wydawcy. Obiekt danych ma właściwości, które są specyficzne dla każdego wydawcy. W przypadku tematów systemowych te właściwości są specyficzne dla dostawcy zasobów, takie jak Usługa Azure Storage lub usługi Azure Event Hubs.

Źródła zdarzeń wysyłają zdarzenia do usługi Azure Event Grid w tablicy, która może mieć kilka obiektów zdarzeń. Podczas księgowania zdarzeń w temacie siatki zdarzeń tablica może mieć całkowity rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczone do 64 KB (ogólna dostępność) lub 1 MB (wersja zapoznawcza). Jeśli zdarzenie lub tablica jest większa niż limity rozmiaru, otrzymasz odpowiedź **413 Ładunek zbyt duży**.

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęte umową dotyczącą poziomu usług ogólnej dostępności (GA). Obsługa zdarzenia o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej. Zdarzenia o masie powyżej 64 KB są naliczane w przyrostach o 64 KB. 

Usługa Event Grid wysyła zdarzenia do subskrybentów w tablicy, która ma jedno zdarzenie. To zachowanie może się zmienić w przyszłości.

Schemat JSON dla zdarzenia w siatce zdarzeń i ładunek danych każdego wydawcy platformy Azure można znaleźć w [magazynie schematu zdarzeń.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)

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

Na przykład schemat opublikowany dla zdarzenia magazynu obiektów Blob platformy Azure jest:

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

Wszystkie zdarzenia mają takie same następujące dane najwyższego poziomu:

| Właściwość | Typ | Wymagany | Opis |
| -------- | ---- | -------- | ----------- |
| temat | ciąg | Nie, ale jeśli zostanie uwzględniony, musi dokładnie odpowiadać tematowi usługi Azure Resource Manager w obszarze Siatce zdarzeń. Jeśli nie zostanie uwzględniona, siatka zdarzeń zostanie oznaczona jako zdarzenie. | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Tak | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Tak | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Tak | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Tak | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Nie | Dane zdarzeń specyficzne dla dostawcy zasobów. |
| dataVersion | ciąg | Nie, ale będą opieczętowane pustą wartością. | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Nie jest wymagane, ale jeśli jest dołączona, `metadataVersion` musi dokładnie dopasować `1`schemat siatki zdarzeń (obecnie tylko). Jeśli nie zostanie uwzględniona, siatka zdarzeń zostanie oznaczona jako zdarzenie. | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Aby dowiedzieć się więcej o właściwościach obiektu danych, zobacz źródło zdarzenia:

* [Subskrypcje platformy Azure (operacje zarządzania)](event-schema-subscriptions.md)
* [Rejestr kontenerów](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Centra zdarzeń](event-schema-event-hubs.md)
* [Iot](event-schema-iot-hub.md)
* [Usługi multimedialne](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupy zasobów (operacje zarządzania)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Uczenie maszynowe platformy Azure](event-schema-machine-learning.md)

W przypadku tematów niestandardowych wydawca zdarzeń określa obiekt danych. Dane najwyższego poziomu powinny mieć te same pola co standardowe zdarzenia zdefiniowane za zasoby.

Publikując zdarzenia w tematach niestandardowych, utwórz tematy dla wydarzeń, które ułatwiają subskrybentom dowiedzieć się, czy są zainteresowani wydarzeniem. Subskrybenci używają tematu do filtrowania i rozsyłania zdarzeń. Należy rozważyć zapewnienie ścieżki dla miejsca zdarzenia, dzięki czemu subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom wąsko lub szeroko filtrować zdarzenia. Jeśli na przykład podasz ścieżkę `/A/B/C` trzech segmentów, jak w `/A` temacie, subskrybenci mogą filtrować według pierwszego segmentu, aby uzyskać szeroki zestaw zdarzeń. Ci subskrybenci otrzymują `/A/B/C` `/A/D/E`wydarzenia z tematami takimi jak lub . Inni subskrybenci mogą filtrować, `/A/B` aby uzyskać węższy zestaw zdarzeń.

Czasami twój temat potrzebuje więcej szczegółów na temat tego, co się stało. Na przykład wydawca **kont** magazynu `/blobServices/default/containers/<container-name>/blobs/<file>` udostępnia temat po dodaniu pliku do kontenera. Subskrybent może filtrować według `/blobServices/default/containers/testcontainer` ścieżki, aby uzyskać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu. Subskrybent może również filtrować lub kierować `.txt` przez sufiks, aby pracować tylko z plikami tekstowymi.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
