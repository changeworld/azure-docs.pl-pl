---
title: Reagowanie na zdarzenia magazynu obiektów Blob platformy Azure | Dokumenty firmy Microsoft
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: e4dd6bab6198546dc5acab78ec59d92387328dbb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754999"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia usługi Blob Storage

Zdarzenia usługi Azure Storage umożliwiają aplikacjom reagowanie na zdarzenia, takie jak tworzenie i usuwanie obiektów blob. Robi to bez konieczności skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Najlepsze jest to, że płacisz tylko za to, czego używasz.

Zdarzenia magazynu obiektów Blob są wypychane przy użyciu [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak usługi Azure Functions, Usługi Azure Logic Apps, a nawet do własnego odbiornika http. Usługa Event Grid zapewnia niezawodne dostarczanie zdarzeń do aplikacji za pośrednictwem rozszerzonych zasad ponawiania prób i dead-lettering.

Zobacz artykuł [schemat zdarzeń magazynu obiektów blob,](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) aby wyświetlić pełną listę zdarzeń, które obsługuje magazyn obiektów Blob.

Typowe scenariusze zdarzeń magazynu obiektów blob obejmują przetwarzanie obrazu lub wideo, indeksowanie wyszukiwania lub dowolny przepływ pracy zorientowany na plik. Przesyłanie plików asynchronicznych doskonale nadają się do zdarzeń. Gdy zmiany są rzadkie, ale scenariusz wymaga natychmiastowej reakcji, architektura oparta na zdarzeniach może być szczególnie wydajna.

Jeśli chcesz wypróbować zdarzenia magazynu obiektów blob, zobacz dowolne z następujących artykułów szybkiego startu:

|Jeśli chcesz użyć tego narzędzia:    |Zobacz ten artykuł: |
|--|-|
|Azure Portal    |[Szybki start: kierowanie zdarzeń magazynu obiektów Blob do punktu końcowego sieci Web za pomocą witryny Azure portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Szybki start: kierowanie zdarzeń magazynu do punktu końcowego sieci Web za pomocą programu PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki start: kierowanie zdarzeń magazynu do punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Aby wyświetlić szczegółowe przykłady reagowania na zdarzenia magazynu obiektów Blob przy użyciu funkcji platformy Azure, zobacz następujące artykuły:

- [Samouczek: Aby zaktualizować tabelę Databricks Delta, użyj zdarzeń usługi Azure Data Lake Storage Gen2.](data-lake-storage-events.md)
- [Samouczek: automatyzacja zmiany rozmiaru przesłanych obrazów przy użyciu siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia v2),** **BlockBlobStorage**i **BlobStorage** obsługują integrację zdarzeń. **Magazyn (genral purpose v1)** *nie* obsługuje integracji z siatką zdarzeń.

## <a name="the-event-model"></a>Model zdarzenia

Usługa Event Grid używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania wiadomości o zdarzeniach do subskrybentów. Ten obraz ilustruje relację między wydawcami zdarzeń, subskrypcjami zdarzeń i programami obsługi zdarzeń.

![Model siatki zdarzeń](./media/storage-blob-event-overview/event-grid-functional-model.png)

Najpierw zasubskrybuj punkt końcowy zdarzenia. Następnie po wyzwoleniu zdarzenia usługa Event Grid wyśle dane o tym zdarzeniu do punktu końcowego.

Zobacz artykuł [schematu zdarzeń magazynu obiektów blob,](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) aby wyświetlić:

> [!div class="checklist"]
> * Pełna lista zdarzeń magazynu obiektów Blob i sposób wyzwalania każdego zdarzenia.
> * Przykład danych, które siatka zdarzeń będzie wysyłać dla każdego z tych zdarzeń.
> * Cel każdej pary wartości klucza, który pojawia się w danych.

## <a name="filtering-events"></a>Filtrowanie zdarzeń

Zdarzenia obiektów blob [mogą być filtrowane](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) według typu zdarzenia, nazwy kontenera lub nazwy obiektu, który został utworzony/usunięty. Filtry w siatce zdarzeń są zgodne z początkiem lub końcem tematu, więc zdarzenia z pasującym obiektem przechodzą do subskrybenta.

Aby dowiedzieć się więcej o stosowaniu filtrów, zobacz [Filtrowanie zdarzeń dla siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Temat zdarzeń magazynu obiektów Blob używa formatu:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby dopasować wszystkie zdarzenia dla konta magazynu, można pozostawić filtry tematu puste.

Aby dopasować zdarzenia z obiektów blob utworzonych w `subjectBeginsWith` zestawie kontenerów udostępniających prefiks, należy użyć filtru, takiego jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia z obiektów blob `subjectBeginsWith` utworzonych w określonym kontenerze, należy użyć filtru, takiego jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów blob utworzonych w określonym `subjectBeginsWith` kontenerze udostępniając prefiks nazwy obiektu blob, należy użyć filtru, takiego jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów blob utworzonych w określonym `subjectEndsWith` kontenerze udostępniając sufiks obiektu blob, należy użyć filtru, takiego jak ".log" lub ".jpg". Aby uzyskać więcej informacji, zobacz [Pojęcia siatki zdarzeń](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Praktyki dotyczące spożywania zdarzeń

Aplikacje obsługujące zdarzenia magazynu obiektów Blob powinny stosować się do kilku zalecanych rozwiązań:
> [!div class="checklist"]
> * Ponieważ wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat wiadomości, aby upewnić się, że pochodzi z oczekiwanego konta magazynu.
> * Podobnie sprawdź, czy eventType jest jednym jesteś przygotowany do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymasz będą typy, których oczekujesz.
> * Ponieważ wiadomości mogą docierać po pewnym opóźnieniu, użyj pól etag, aby dowiedzieć się, czy informacje o obiektach są nadal aktualne. Aby dowiedzieć się, jak korzystać z pola etag, zobacz [Zarządzanie współbieżnością w magazynie obiektów Blob](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Ponieważ wiadomości mogą być dostarczane poza kolejnością, użyj pól sekwencera, aby zrozumieć kolejność zdarzeń na dowolnym określonym obiekcie. Pole sekwencera jest wartością ciągu reprezentującą logiczną sekwencję zdarzeń dla określonej nazwy obiektu blob. Można użyć standardowego porównania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń o tej samej nazwie obiektu blob.
> Zdarzenia magazynu gwarantuje co najmniej raz dostarczanie do subskrybentów, co zapewnia, że wszystkie wiadomości są przesyłane. Jednak ze względu na ponownych prób lub dostępności subskrypcji, zduplikowane wiadomości mogą czasami wystąpić.
> * Użyj pola blobType, aby zrozumieć, jakiego typu operacje są dozwolone w obiekcie blob i typy biblioteki klienta, których należy użyć do uzyskania dostępu do obiektu blob. Prawidłowe wartości `BlockBlob` to `PageBlob`jeden lub . 
> * Użyj pola adresu `CloudBlockBlob` URL `CloudAppendBlob` z i konstruktorów, aby uzyskać dostęp do obiektu blob.
> * Ignoruj pola, których nie rozumiesz. Ta praktyka pomoże Ci zachować odporność na nowe funkcje, które mogą być dodawane w przyszłości.
> * Jeśli chcesz upewnić się, że **zdarzenie Microsoft.Storage.BlobCreated** jest wyzwalane tylko wtedy, gdy `CopyBlob`blok `PutBlob` `PutBlockList` blob jest całkowicie zatwierdzony, filtruj zdarzenie dla wywołań interfejsu API , lub `FlushWithClose` REST. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft.Storage.BlobCreated** tylko wtedy, gdy dane są w pełni zaangażowane w blokowy obiekt blob. Aby dowiedzieć się, jak utworzyć filtr, zobacz [Filtrowanie zdarzeń dla siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze Event Grid i wypróbuj zdarzenia magazynu obiektów Blob:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Schemat zdarzeń magazynu obiektów Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Rozsyłanie zdarzeń magazynu obiektów Blob do niestandardowego punktu końcowego sieci Web](storage-blob-event-quickstart.md)
