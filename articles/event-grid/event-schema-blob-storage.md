---
title: Schemat zdarzenia magazynu obiektów blob Azure Event Grid
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi blob storage za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445766"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schemat zdarzeń Azure Event Grid dla magazynu obiektów Blob

Ten artykuł zawiera właściwości i schematu dla zdarzeń usługi blob storage. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródło zdarzenia magazynu](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Lista zdarzeń dla interfejsów API REST usługi Blob

Te zdarzenia są wyzwalane, gdy klient tworzy, zastępuje lub usuwa obiekt blob przez wywołanie interfejsów API REST usługi Blob.

 |Nazwa zdarzenia |Opis|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Wyzwalane, gdy obiekt blob zostanie utworzony lub zastąpiony. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci korzystają z `PutBlob`, `PutBlockList`, lub `CopyBlob` operacje, które są dostępne w interfejsie API REST obiektu Blob.   |
 |**Microsoft.Storage.BlobDeleted** |Wyzwalane, gdy obiekt blob zostanie usunięty. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci wywołują `DeleteBlob` operacji, które są dostępne w interfejsie API REST obiektu Blob. |

> [!NOTE]
> Jeśli chcesz upewnić się, że **Microsoft.Storage.BlobCreated** zdarzenie jest wyzwalane tylko wtedy, gdy jest blokowy obiekt Blob jest całkowicie zadeklarowanej, filtrować zdarzenia dla `CopyBlob`, `PutBlob`, i `PutBlockList` wywołań interfejsów API REST. Te wyzwalania wywołań interfejsu API **Microsoft.Storage.BlobCreated** zdarzenia tylko wtedy, gdy dane są w pełni zobowiązane do blokowego obiektu Blob. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń usługi Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista zdarzeń usługi Azure Data Lake magazynu Gen 2 interfejsy API REST

Te zdarzenia są wyzwalane, jeśli zostanie włączone hierarchicznej przestrzeni nazw na koncie magazynu, a klienci wywołań interfejsów API usługi Azure Data Lake Storage Gen2 REST.

> [!NOTE]
> Te zdarzenia są w publicznej wersji zapoznawczej i są dostępne tylko **zachodnie stany USA 2** i **zachodnio-środkowe stany USA** regionów.

 |Nazwa zdarzenia|Opis|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Wyzwalane, gdy obiekt blob zostanie utworzony lub zastąpiony. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci korzystają z `CreateFile` i `FlushWithClose` operacje, które są dostępne w usłudze Azure Data Lake magazynu Gen2 interfejsu API REST. |
 |**Microsoft.Storage.BlobDeleted** |Wyzwalane, gdy obiekt blob zostanie usunięty. <br>W szczególności, to zdarzenie jest również wyzwalany, gdy klienci wywołują `DeleteFile` operacji, które są dostępne w usłudze Azure Data Lake magazynu Gen2 interfejsu API REST. |
 |**Microsoft.Storage.BlobRenamed**|Wyzwalane, gdy zmieniono jego nazwę obiektu blob. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci korzystają z `RenameFile` operacji, które są dostępne w usłudze Azure Data Lake magazynu Gen2 interfejsu API REST.|
 |**Microsoft.Storage.DirectoryCreated**|Wyzwalane, gdy jest tworzony w katalogu. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci korzystają z `CreateDirectory` operacji, które są dostępne w usłudze Azure Data Lake magazynu Gen2 interfejsu API REST.|
 |**Microsoft.Storage.DirectoryRenamed**|Wyzwalane, gdy zmieniono jego nazwę katalogu. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci korzystają z `RenameDirectory` operacji, które są dostępne w usłudze Azure Data Lake magazynu Gen2 interfejsu API REST.|
 |**Microsoft.Storage.DirectoryDeleted**|Wyzwalane, gdy katalog zostanie usunięty. <br>W szczególności, to zdarzenie jest wyzwalane, gdy klienci korzystają z `DeleteDirectory` operacji, które są dostępne w usłudze Azure Data Lake magazynu Gen2 interfejsu API REST.|

> [!NOTE]
> Jeśli chcesz upewnić się, że **Microsoft.Storage.BlobCreated** zdarzenie jest wyzwalane tylko wtedy, gdy jest blokowy obiekt Blob jest całkowicie zadeklarowanej, filtrować zdarzenia dla `FlushWithClose` wywołania interfejsu API REST. Wyzwalacze wywołanie tego interfejsu API **Microsoft.Storage.BlobCreated** zdarzenia tylko wtedy, gdy dane są w pełni zobowiązane do blokowego obiektu Blob. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń usługi Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Zawartość reagowania na zdarzenia

Po wyzwoleniu zdarzenie usługi Event Grid wysyła dane dotyczące tego zdarzenia do punktu końcowego subskrypcji.

Ta sekcja zawiera przykład jak tych danych będzie wyglądać dla każdego zdarzenia magazynu obiektów blob.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
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
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated event (Data Lake Storage Gen2)

Jeśli konto usługi blob storage ma hierarchicznej przestrzeni nazw, danych podobny do poprzedniego przykładu, z wyjątkiem tych zmian:

* `dataVersion` Jest ustawiona na wartość `2`.

* `data.api` Klucz jest ustawiony na ciąg `CreateFile` lub `FlushWithClose`.

* `contentOffset` Klucz znajduje się w zestawie danych.

> [!NOTE]
> Jeśli używasz aplikacji `PutBlockList` operację, aby przekazać nowy obiekt blob do konta, dane nie zawierają tych zmian.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted event (Data Lake Storage Gen2)

Jeśli konto usługi blob storage ma hierarchicznej przestrzeni nazw, danych podobny do poprzedniego przykładu, z wyjątkiem tych zmian:

* `dataVersion` Jest ustawiona na wartość `2`.

* `data.api` Klucz jest ustawiony na ciąg `DeleteFile`.

* `url` Klucza zawiera ścieżkę `dfs.core.windows.net`.

> [!NOTE]
> Jeśli używasz aplikacji `DeleteBlob` operacja usuwania obiektu blob z konta, a dane nie zawierają tych zmian.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Zdarzenie Microsoft.Storage.DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Zdarzenie Microsoft.Storage.DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Zdarzenie Microsoft.Storage.DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
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
| data | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| api | string | Operacja, która wyzwoliła zdarzenie. |
| clientRequestId | string | Identyfikator żądania dostarczonych przez klienta magazynu operacji interfejsu API. Ten identyfikator może służyć do odnoszą się do dzienników diagnostycznych usługi Azure Storage, korzystając z pola "client-request-id" w dziennikach i mogą otrzymywać żądania klientów przy użyciu nagłówek "x-ms klient request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Identyfikator generowanych przez usługi żądania dla operacji interfejsu API usługi storage. Może służyć do odnoszą się do usługi Azure Storage diagnostyczne dzienniki, korzystając z pola "żądanie id-header" w dziennikach i zwracany jest zainicjowanie wywołanie interfejsu API w nagłówku "x-ms-request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | Wartość, która służy do wykonywania operacji warunkowo. |
| contentType | string | Typ zawartości, określony dla obiektu blob. |
| contentLength contentLength | liczba całkowita | Rozmiar obiektu blob w bajtach. |
| blobType | string | Typ obiektu blob. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| contentOffset | numer | Przesunięcie w bajtach operacji zapisu wykonane w punkcie, gdzie aplikacja wyzwolenie zdarzenia ukończone zapisywanie do pliku. <br>Pojawia się tylko w przypadku zdarzeń wyzwalanych dla kont usługi blob storage, które mają hierarchicznej przestrzeni nazw.|
| destinationUrl |string | Adres url pliku który będzie istnieć po zakończeniu operacji. Na przykład po zmianie nazwy pliku `destinationUrl` właściwość zawiera adres url nową nazwę pliku. <br>Pojawia się tylko w przypadku zdarzeń wyzwalanych dla kont usługi blob storage, które mają hierarchicznej przestrzeni nazw.|
| sourceUrl |string | Adres url pliku, który istnieje przed wykonać operację. Na przykład po zmianie nazwy pliku `sourceUrl` zawiera adres url oryginalna nazwa pliku przed operacji zmiany nazwy. <br>Pojawia się tylko w przypadku zdarzeń wyzwalanych dla kont usługi blob storage, które mają hierarchicznej przestrzeni nazw. |
| url | string | Ścieżka do obiektu blob. <br>Jeśli klient korzysta z interfejsu API REST obiektów Blob, a następnie adres url zawiera tę strukturę:  *\<nazwa konta magazynu\>.blob.core.windows.net/\<nazwa kontenera\>/\<nazwa pliku \>* . <br>Jeśli klient korzysta z interfejsu API REST programu Data Lake Storage, a następnie adres url zawiera tę strukturę:  *\<nazwa konta magazynu\>.dfs.core.windows.net/\<nazw systemu plików\> / \<nazwa pliku\>* .
|
| recursive| string| `True` do wykonania tej operacji we wszystkich katalogach podrzędnych; w przeciwnym razie `False`. <br>Pojawia się tylko w przypadku zdarzeń wyzwalanych dla kont usługi blob storage, które mają hierarchicznej przestrzeni nazw. |
| sequencer | string | Nieprzezroczysty Ciąg reprezentującą logiczne sekwencję zdarzeń o dowolną nazwę określonego obiektu blob.  Użytkownicy mogą używać standardowego porównywania ciągów zrozumienie względne sekwencji dwa zdarzenia w tej samej nazwy obiektu blob. |
| storageDiagnostics | object | Dane diagnostyczne od czasu do czasu dołączane przez usługę Azure Storage. Jeśli jest obecny, mają być ignorowane przez odbiorców zdarzeń. |

|Właściwość|Typ|Opis|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Wprowadzenie do pracy z zdarzeń usługi blob storage, zobacz [zdarzeń usługi Blob trasy storage — interfejs wiersza polecenia Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
