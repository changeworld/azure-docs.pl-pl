---
title: Reagowanie na zdarzenia usługi Azure Blob storage | Dokumentacja firmy Microsoft
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444680"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia usługi Blob storage

Zdarzenia usługi Azure Storage umożliwiają aplikacjom reagowania na zdarzenia, takie jak tworzenie i usuwanie obiektów blob za pomocą nowoczesnych architektur bez użycia serwera. Robi to bez konieczności skomplikowanego kodu lub sondowania kosztowne i nieefektywna usług.

Zamiast tego zdarzenia są przekazywane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) subskrybentów, takich jak Azure Functions, Azure Logic Apps, a nawet Twoje własne, niestandardowe odbiornika http i płać tylko za rzeczywiste użycie.

Zdarzenia usługi blob storage niezawodnie są wysyłane do usługi Event Grid, która zapewnia niezawodne dostarczanie usług do aplikacji za pośrednictwem zasad ponawiania rozbudowane i dostarczania wiadomości utraconych.

Typowe scenariusze zdarzenia magazynu obiektów Blob obejmują przetwarzanie obrazu lub filmu wideo, indeksowanie wyszukiwania lub przepływ pracy korzystający z pliku. Przekazywanie plików asynchroniczne są doskonałe rozwiązanie dla zdarzenia. Gdy zmiany są rzadkie, ale dany scenariusz wymaga natychmiastowej reakcji, oparte na zdarzeniach architektury może być szczególnie wydajne.

Jeśli chcesz teraz Wypróbuj tę funkcję, zobacz dowolnego z poniższych artykułów szybkiego startu:

|Jeśli chcesz użyć tego narzędzia:    |Znajduje się w artykule: |
|--|-|
|Azure Portal    |[Szybki start: Kierowanie zdarzeń usługi Blob storage do punktu końcowego sieci web za pomocą witryny Azure portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki start: Kierowanie zdarzeń usługi storage w punkcie końcowym sieci web przy użyciu programu PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Szybki start: Kierowanie zdarzeń usługi storage w punkcie końcowym sieci web przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>Model zdarzeń

Korzysta z usługi Event Grid [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Ten obraz przedstawiono relację między wydawcami zdarzeń, subskrypcje zdarzeń i programów obsługi zdarzeń.

![Model siatki zdarzeń](./media/storage-blob-event-overview/event-grid-functional-model.png)

Po pierwsze Subskrybuj punkt końcowy do zdarzenia. Następnie po wyzwoleniu zdarzenie usługi Event Grid będzie wysyłać dane dotyczące tego zdarzenia do punktu końcowego.

Zobacz [schemat zdarzenia magazynu obiektów Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) artykuł, aby wyświetlić:

> [!div class="checklist"]
> * Pełna lista zdarzeń usługi Blob storage i jak każde zdarzenie jest wyzwalane.
> * Przykład danych usługi Event Grid prześle dla każdego z tych zdarzeń.
> * Cel każdej pary wartości klucza, który pojawia się w danych.

## <a name="filtering-events"></a>Filtrowanie zdarzeń

Subskrypcje zdarzeń obiektów blob można filtrować na podstawie typ zdarzenia i według nazwy kontenera i nazwa obiektu blob obiektu, który został utworzony lub usunąć.  Filtry można zastosować do subskrypcji zdarzeń albo podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) subskrypcji zdarzeń lub [w późniejszym czasie](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Filtry tematu usługi Event Grid pracę, sądząc po "rozpoczyna się od" a "kończy się na" dopasowania, tak aby zdarzeń za pomocą dopasowywania podmiotu są dostarczane do subskrybenta.

Aby dowiedzieć się więcej na temat sposobu stosowania filtrów, zobacz [filtrowanie zdarzeń usługi Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Temat zdarzenia usługi Blob storage używany format:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby dopasować wszystkich zdarzeń dla konta magazynu, można pozostawić filtry podmiotu puste.

Aby dopasować zdarzenia z obiektów blob utworzonych w zestawie kontenerów udostępnianie prefiksu, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia utworzone w określonym kontenerze obiektów blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów blob utworzonych w określonym kontenerze udostępnianie prefiks nazwy obiektu blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów blob utworzonych w kontenerze określonym udostępnianie sufiks obiektów blob, użyj `subjectEndsWith` filtru, takie jak "log" lub ".jpg". Aby uzyskać więcej informacji, zobacz [Event Grid — pojęcia](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia

Aplikacje, które obsługują zdarzenia usługi Blob storage, należy wykonać kilka zalecanych rozwiązań:
> [!div class="checklist"]
> * Zgodnie z wieloma subskrypcjami można skonfigurować w celu kierowanie zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, nie przyjęto założenie, że są zdarzenia z konkretnego źródła, ale Sprawdź tematu wiadomości, aby upewnić się, że pochodzi on z konta magazynu, które są oczekiwane.
> * Podobnie upewnij się, że zdarzenia jest przygotowany do procesów, a nie należy zakładać, że wszystkie zdarzenia, które otrzymujesz będzie typów, których oczekujesz.
> * Nadejścia nowych wiadomości mogą poza kolejnością i po pewne opóźnienie, należy użyć pól element etag, aby zrozumieć, jeśli Twoje informacje, informacje o obiektach są nadal aktualne.  Ponadto użyć pola program sequencer, aby zrozumieć kolejności zdarzeń dla dowolnego określonego obiektu.
> * Użyj pola blobType, aby zrozumieć, jakiego typu operacje są dozwolone w obiekcie blob, a typy biblioteki klienckiej, która powinna być używana do dostępu do obiektu blob. Prawidłowe wartości to albo `BlockBlob` lub `PageBlob`. 
> * Pole adresu url za pomocą `CloudBlockBlob` i `CloudAppendBlob` z konstruktorów mają dostęp do obiektu blob.
> * Ignoruj pola, których nie rozumie. Praktyka ta będzie zapewnić Ci odporne na błędy do nowych funkcji, które mogłyby zostać dodane w przyszłości.
> * Jeśli chcesz upewnić się, że **Microsoft.Storage.BlobCreated** zdarzenie jest wyzwalane tylko wtedy, gdy jest blokowy obiekt Blob jest całkowicie zadeklarowanej, filtrować zdarzenia dla `CopyBlob`, `PutBlob`, `PutBlockList` lub `FlushWithClose` REST Wywołania interfejsu API. Te wyzwalania wywołań interfejsu API **Microsoft.Storage.BlobCreated** zdarzenia tylko wtedy, gdy dane są w pełni zobowiązane do blokowego obiektu Blob. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń usługi Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Event Grid i wypróbuj zdarzeń usługi Blob storage:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Kierowanie magazynu obiektów Blob zdarzeń do niestandardowego internetowego punktu końcowego](storage-blob-event-quickstart.md)
