---
title: Schemat zdarzenia magazynu obiektów blob Azure Event Grid
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi blob storage za pomocą usługi Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 11524f8868a0102e30b06f3385a26b1bd06aae6e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054401"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schemat zdarzeń Azure Event Grid dla magazynu obiektów Blob

Ten artykuł zawiera właściwości i schematu dla zdarzeń usługi blob storage. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródło zdarzenia magazynu](event-sources.md#storage).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Magazyn obiektów blob emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Wywoływane, gdy zostanie utworzony obiekt blob. |
| Microsoft.Storage.BlobDeleted | Wywoływane, gdy obiekt blob zostanie usunięty. |

## <a name="example-event"></a>Przykład zdarzenia

Poniższy przykład przedstawia schematu obiektu blob utworzone zdarzenie: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia usunięto obiekt blob jest podobne: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
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
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| api | ciąg | Operacja, która wyzwoliła zdarzenie. |
| clientRequestId | ciąg | Generowane przez klientów, nieprzezroczysta wartość Limit znaków 1 KB. Po włączeniu usługa storage analytics, rejestrowanie, zostało zapisane w dziennikach analizy. |
| requestId | ciąg | Unikatowy identyfikator dla żądania. Używać go do rozwiązywania problemów z żądania. |
| Element eTag | ciąg | Wartość, która służy do wykonywania operacji warunkowo. |
| Typ zawartości | ciąg | Typ zawartości, określony dla obiektu blob. |
| właściwości contentLength | liczba całkowita | Rozmiar obiektu blob w bajtach. |
| blobType | ciąg | Typ obiektu blob. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| url | ciąg | Ścieżka do obiektu blob. |
| Program Sequencer | ciąg | Wartość kontrolowanej przez użytkownika, która służy do śledzenia żądań. |
| storageDiagnostics | obiekt | Informacje o diagnostyce magazynu. |
 
## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Wprowadzenie do pracy z zdarzeń usługi blob storage, zobacz [zdarzeń usługi Blob trasy storage — interfejs wiersza polecenia Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
