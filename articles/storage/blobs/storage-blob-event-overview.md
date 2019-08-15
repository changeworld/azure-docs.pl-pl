---
title: Reagowanie na zdarzenia usługi Azure Blob Storage | Microsoft Docs
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 13eef9beb6c86683c56efc744dc42b4614b84fe9
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946480"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia magazynu obiektów BLOB

Zdarzenia usługi Azure Storage umożliwiają aplikacjom reagowanie na zdarzenia, takie jak tworzenie i usuwanie obiektów blob, przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania.

Zamiast tego zdarzenia są wypychane za pośrednictwem [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak Azure Functions, Azure Logic Apps lub nawet do własnego niestandardowego odbiornika HTTP i płacisz tylko za to, czego używasz.

Zdarzenia magazynu obiektów BLOB są niezawodnie wysyłane do usługi Event Grid, która zapewnia niezawodne usługi dostarczania dla aplikacji za pomocą rozbudowanych zasad ponowień i dostarczania wiadomości utraconych.

Typowe scenariusze zdarzeń magazynu obiektów BLOB obejmują przetwarzanie obrazów lub wideo, indeksowanie wyszukiwania lub wszelkie przepływy pracy zorientowane na pliki. Asynchroniczne przekazywanie plików to doskonałe dopasowanie do zdarzeń. Gdy zmiany są rzadko wykonywane, ale scenariusz wymaga natychmiastowej reakcji, Architektura oparta na zdarzeniach może być szczególnie wydajna.

Jeśli chcesz wypróbować tę usługę teraz, zobacz dowolny z tych artykułów szybkiego startu:

|Jeśli chcesz użyć tego narzędzia:    |Zobacz ten artykuł: |
|--|-|
|Azure Portal    |[Szybki start: Kierowanie zdarzeń magazynu obiektów BLOB do punktu końcowego sieci Web za pomocą Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Szybki start: Kierowanie zdarzeń magazynu do punktu końcowego w sieci Web przy użyciu programu PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki start: Kierowanie zdarzeń magazynu do punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>Model zdarzenia

Event Grid używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Ten obraz przedstawia relację między wydawcami zdarzeń, subskrypcjami zdarzeń i programami obsługi zdarzeń.

![Model Event Grid](./media/storage-blob-event-overview/event-grid-functional-model.png)

Najpierw Zasubskrybuj punkt końcowy do zdarzenia. Następnie po wyzwoleniu zdarzenia usługa Event Grid wyśle dane dotyczące tego zdarzenia do punktu końcowego.

Zobacz artykuł [schemat zdarzeń magazynu obiektów BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , aby wyświetlić:

> [!div class="checklist"]
> * Pełna lista zdarzeń magazynu obiektów blob oraz sposób wyzwalania poszczególnych zdarzeń.
> * Przykład danych wysyłanych przez Event Grid dla każdego z tych zdarzeń.
> * Przeznaczenie poszczególnych par klucz-wartość, które pojawiają się w danych.

## <a name="filtering-events"></a>Filtrowanie zdarzeń

Subskrypcje zdarzeń obiektów BLOB można filtrować na podstawie typu zdarzenia i nazwy kontenera i nazwy obiektu BLOB, który został utworzony lub usunięty.  Filtry mogą być stosowane do subskrypcji zdarzeń podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) subskrypcji zdarzeń lub [w późniejszym czasie](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Filtry podmiotu w Event Grid pracy oparte na "zaczyna się od" i "kończą się na" dopasowań, więc zdarzenia ze zgodnym podmiotem są dostarczane do subskrybenta.

Aby dowiedzieć się więcej na temat stosowania filtrów, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

W temacie zdarzeń magazynu obiektów BLOB jest stosowany format:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby dopasować wszystkie zdarzenia dla konta magazynu, można pozostawić puste filtry podmiotu.

Aby dopasować zdarzenia z obiektów BLOB utworzonych w zestawie kontenerów, które współużytkują prefiks `subjectBeginsWith` , należy użyć filtru, takiego jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, `subjectBeginsWith` Użyj filtru, takiego jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, udostępniając prefiks nazwy obiektu BLOB `subjectBeginsWith` , Użyj filtru, takiego jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, udostępniając sufiks obiektu BLOB `subjectEndsWith` , Użyj filtru, takiego jak ". log" lub ". jpg". Aby uzyskać więcej informacji, zobacz temat [Event Grid pojęcia](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń

Aplikacje, które obsługują zdarzenia magazynu obiektów blob, powinny spełniać kilka zalecanych praktyk:
> [!div class="checklist"]
> * Ponieważ można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie przyjmować zdarzeń z określonego źródła, ale w celu sprawdzenia, czy pochodzi ona z konta magazynu, którego oczekujesz.
> * Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
> * Ponieważ komunikaty mogą się pojawiać poza kolejnością i po pewnym opóźnieniu, należy użyć pól ETag, aby zrozumieć, czy informacje o obiektach są nadal aktualne.  Ponadto należy użyć pól programu Sequencer do zrozumienia kolejności zdarzeń dla każdego określonego obiektu.
> * Użyj pola blobtype, aby zrozumieć, jaki typ operacji jest dozwolony dla obiektu BLOB, a także typy bibliotek klientów, które powinny być używane w celu uzyskania dostępu do obiektu BLOB. Prawidłowe wartości to `BlockBlob` lub `PageBlob`. 
> * Użyj pola adresu URL z `CloudBlockBlob` konstruktorami i `CloudAppendBlob` , aby uzyskać dostęp do obiektu BLOB.
> * Ignoruj pola, które nie są zrozumiałe. Ta metoda pomaga w zachowaniu odporności na nowe funkcje, które mogą zostać dodane w przyszłości.
> * Jeśli chcesz się upewnić, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, przefiltruj `PutBlockList` zdarzenie dla wywołań interfejsu API `CopyBlob`, `PutBlob`lub `FlushWithClose` protokołu REST. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i nadaj zdarzeniom magazynu obiektów blob:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego internetowego punktu końcowego](storage-blob-event-quickstart.md)
