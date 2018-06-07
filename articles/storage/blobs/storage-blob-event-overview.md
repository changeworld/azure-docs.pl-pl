---
title: Reagowanie na zdarzenia magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: db062fc36478d6ba2cf0f00544793f635ccdbb06
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650132"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia magazynu obiektów Blob

Zdarzenia magazynu Azure umożliwiają aplikacjom zareagować na tworzenie i usuwanie obiektów blob przy użyciu nowoczesnych architekturach niekorzystającą. Robi to bez konieczności skomplikowane, kodu lub mało wydajne i ekonomiczne sondowania usług.  Zamiast tego zdarzenia są usuwane [siatki zdarzeń Azure](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet własne odbiornik http niestandardowego, a tylko płać za można użyć. 

Typowe scenariusze zdarzenia magazynu obiektów Blob obejmują przetwarzania obrazu lub wideo, indeksowanie wyszukiwania lub przepływu pracy zorientowane na plik.  Przekazywania plików asynchroniczne są doskonałe dopasowanie dla zdarzeń.  Zmiany są rzadko wykonywane, kiedy dany scenariusz wymaga natychmiastowej reakcji, architektura oparta na zdarzenie może być szczególnie skuteczne.

Dostępność dla magazynu zdarzenia jest powiązany z siatki zdarzeń [dostępności](../../event-grid/overview.md) i będą dostępne w różnych regionach, tak jak w przypadku zdarzeń siatki. Spójrz na [zdarzenia magazynu obiektów Blob trasy do niestandardowego sieci web punktu końcowego - CLI](storage-blob-event-quickstart.md) lub [zdarzenia magazynu obiektów Blob trasy do niestandardowego sieci web punktu końcowego - PowerShell](storage-blob-event-quickstart-powershell.md) dla prosty przykład. 

![Model siatki zdarzeń](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Konta usługi Blob Storage
Zdarzenia magazynu obiektów blob są dostępne w [konta usługi Blob storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) i [kont magazynu ogólnego przeznaczenia v2](../common/storage-account-options.md#general-purpose-v2). **Ogólnego przeznaczenia v2 (GPv2)** są konta magazynu, które obsługują wszystkie funkcje dla wszystkich usług magazynu, w tym obiekty BLOB, plików, kolejek i tabel. A **kontem magazynu obiektów Blob** to specjalne konto magazynu do przechowywania danych bez struktury jako obiekty BLOB (obiekty) w usłudze Azure Storage. Konta magazynu obiektów blob są podobne do kont magazynu ogólnego przeznaczenia i udostępniać wszystkie trwałości, dostępności, skalowalności i wydajności zalety że używane obecnie, łącznie z 100% spójnością interfejsu API dla blokowych obiektów blob i uzupełnialnych obiektów blob. W przypadku aplikacji wymagających tylko magazynu obiektów blokowych lub uzupełnialnych obiektów blob zalecamy używanie kont usługi Blob Storage. 

## <a name="available-blob-storage-events"></a>Dostępne zdarzenia magazynu obiektów Blob
Siatka zdarzeń używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do wyznaczania tras wiadomościom zdarzeń do subskrybentów.  Subskrypcja zdarzeń magazynu obiektów blob może zawierać dwa typy zdarzeń:  

> |Nazwa zdarzenia|Opis|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Uruchamiany podczas tworzenia obiektu blob lub zastąpione przez `PutBlob`, `PutBlockList`, lub `CopyBlob` operacji|
> |`Microsoft.Storage.BlobDeleted`|Uruchamiany po usunięciu obiektu blob za pośrednictwem `DeleteBlob` operacji|

## <a name="event-schema"></a>Schematu zdarzeń
Zdarzenia magazynu obiektów blob zawiera wszystkich informacji potrzebnych do reagowania na zmiany w danych.  Można zidentyfikować zdarzenia magazynu obiektów Blob, ponieważ właściwość eventType rozpoczyna się od "Microsoft.Storage."  
Dodatkowe informacje dotyczące użycia właściwości zdarzenia zdarzeń siatki jest udokumentowany w [schematu zdarzeń siatki zdarzeń](../../event-grid/event-schema.md).  

> |Właściwość|Typ|Opis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |Temat|ciąg|Pełny identyfikator usługi Azure Resource Manager konta magazynu, który emituje zdarzenia.|
> |Temat|ciąg|Ścieżka względna zasobów do obiektu, który jest przedmiotem zdarzenia, przy użyciu tego samego formatu usługi Azure Resource Manager rozszerzonej używana do opisu konta magazynu, usług i kontenery dla Azure RBAC.  Ten format obejmuje nazwę zachowania w przypadku obiektów blob.|
> |eventTime|ciąg|Data/Godzina wygenerowania zdarzenia, w formacie ISO 8601|
> |Typ zdarzenia|ciąg|"Microsoft.Storage.BlobCreated" lub "Microsoft.Storage.BlobDeleted"|
> |Identyfikator|ciąg|Unikatowy identyfikator, jeśli to zdarzenie|
> |dataVersion|ciąg|Wersja schematu dla obiektu danych.|
> |Element metadataVersion|ciąg|Wersja schematu właściwości najwyższego poziomu.|
> |dane|obiekt|Zbieranie danych zdarzeń specyficznych dla magazynu obiektów blob|
> |data.contentType|ciąg|Typ zawartości obiektu blob, czy zwrócony w nagłówku Content-Type z obiektu blob|
> |data.contentLength|numer|Rozmiar obiektu blob, tak jak liczbę całkowitą reprezentującą liczbę bajtów, czy zwrócony w nagłówku Content-Length z obiektu blob.  Wysyłane z BlobCreated zdarzeń, ale nie z BlobDeleted.|
> |data.url|ciąg|Adres url obiektu, który jest przedmiotem zdarzenia|
> |data.eTag|ciąg|Element etag obiektu, gdy to zdarzenie jest wywoływane.  Nie jest dostępna dla zdarzenia BlobDeleted.|
> |data.api|ciąg|Nazwa operacji interfejsu api, która wyzwoliła tego zdarzenia.  W przypadku zdarzeń BlobCreated ta wartość jest "Metody PutBlob", "Metoda PutBlockList" lub "CopyBlob".  W przypadku zdarzeń BlobDeleted ta wartość jest "DeleteBlob".  Te wartości są takie same nazwy interfejsu api, które znajdują się w dziennikach diagnostycznych usługi Azure Storage.  Zobacz [rejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|ciąg|Ciąg z ogólnym opisem reprezentującej logicznej sekwencji zdarzeń dla dowolnej nazwy określonego obiektu blob.  Użytkownicy mogą używać standardowego porównywania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń na tej samej nazwie obiektu blob.|
> |data.requestId|ciąg|Identyfikator żądania generowanych przez usługi dla operacji interfejsu API magazynu.  Może służyć do skorelowania do magazynu Azure diagnostycznych dzienników, korzystając z pola "żądanie id-header" w dziennikach i zostanie zwrócona przez inicjowanie wywołań interfejsu API w nagłówku "x-ms-request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|ciąg|Identyfikator żądania dostarczonych przez klienta do przechowywania operacji interfejsu API.  Służy do skorelowania do dzienników diagnostycznych usługi Azure Storage, korzystając z pola "client-request-id" w dziennikach i może zostać dostarczona w żądaniach klienta przy użyciu nagłówka "x-ms-client żądania id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|obiekt|Dane diagnostyczne czasami uwzględniony przez usługę Azure Storage.  Jeśli jest obecny, powinny być ignorowane przez odbiorców zdarzeń.|
|data.blobType|ciąg|Typ obiektu blob. Prawidłowe wartości to "BlockBlob" lub "PageBlob".| 

Oto przykład zdarzenia BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Aby uzyskać więcej informacji, zobacz [schematu zdarzenia magazynu obiektów Blob](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrowanie zdarzeń
Subskrypcje zdarzeń obiektu blob można filtrować na podstawie typu zdarzenia oraz nazwa kontenera i nazwa obiektu blob obiektu, który został utworzony lub usunięty.  Filtry można zastosować do subskrypcji zdarzeń albo podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) subskrypcji zdarzeń lub [w późniejszym czasie](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Filtry tematu w pracach siatki zdarzenia na podstawie "rozpoczyna się od ciągu" i "kończy się wyrazem" dopasowań, dzięki czemu zdarzenia z podmiotu pasującego są dostarczane do subskrybenta. 

Podmiot zdarzenia magazynu obiektów Blob w formacie:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby uwzględnić wszystkie zdarzenia dla konta magazynu, można pozostawić filtry tematu puste.

Aby dopasować zdarzenia z utworzone w zestawie kontenery udostępnianie prefiks obiektów blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia z utworzone w określonym kontenerze obiektów blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów blob utworzone w określonym kontenerze udostępnianie prefiksu nazwy obiektu blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów blob utworzone w określonego kontenera udostępnianie sufiks obiektów blob, użyj `subjectEndsWith` filtru, takich jak "log" lub ".jpg"

Aby uzyskać więcej informacji, zobacz [pojęcia siatki zdarzeń](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzeń
Aplikacje obsługujące zdarzenia magazynu obiektów Blob, należy wykonać kilka zaleceń:
> [!div class="checklist"]
> * Jak wiele subskrypcji można skonfigurować do trasy zdarzenia do tej procedury obsługi zdarzeń, jest ważne, nie przyjęto założenie, że są zdarzenia z danego źródła, ale sprawdź temat wiadomości, aby upewnić się, że pochodzą z konta magazynu, które są oczekiwane.
> * Podobnie Sprawdź, czy zdarzenia jest jednym są przygotowane do procesu, a nie założono, że wszystkie zdarzenia, które otrzymujesz będzie oczekiwane typy.
> * Nadejścia nowych wiadomości mogą poza kolejnością i po pewne opóźnienie, użyj pól etag, aby zrozumieć, jeśli Twoje informacje o obiektach są nadal aktualne.  Należy także użyć pola sequencer zrozumienie kolejność zdarzeń w dowolnym określonego obiektu.
> * Użyj pola blobType, aby zrozumieć, jaki typ operacji są dozwolone w obiekcie blob i typów biblioteki klienckiej, która powinna być używana do dostępu obiektu blob. Prawidłowe wartości to albo `BlockBlob` lub `PageBlob`. 
> * Użyj pola adres url z `CloudBlockBlob` i `CloudAppendBlob` konstruktorów można uzyskać dostępu do obiektu blob.
> * Ignoruj pola, które nie zrozumieć.  Takie rozwiązanie będzie zapewnić Ci odporne na nowe funkcje, które mogą zostać dodane w przyszłości.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat zdarzeń siatki i wypróbuj zdarzenia magazynu obiektów Blob:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Trasy do punktu końcowego sieci web niestandardowego magazynu obiektów Blob zdarzenia](storage-blob-event-quickstart.md)
