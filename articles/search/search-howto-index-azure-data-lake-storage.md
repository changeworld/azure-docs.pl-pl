---
title: Wyszukiwanie za pomocą usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować zawartość i metadane w usłudze Azure Data Lake Storage Gen2. Ta funkcja jest obecnie w publicznej wersji zapoznawczej
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905663"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indeksowanie dokumentów w usłudze Azure Data Storage Gen2

> [!IMPORTANT] 
> Obsługa usługi Azure Data Lake Storage Gen2 jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Możesz poprosić o dostęp do podglądów, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). Ta funkcja zapewnia [interfejs API REST w wersji 2019-05-06-Preview.](search-api-preview.md) Obecnie nie ma obsługi portalu ani SDK .NET.


Podczas konfigurowania konta magazynu platformy Azure można włączyć [hierarchiczny obszar nazw](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Dzięki temu kolekcja zawartości na koncie ma być zorganizowana w hierarchii katalogów i zagnieżdżonych podkatalogów. Włączenie hierarchicznego obszaru nazw umożliwia obsługę [usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

W tym artykule opisano, jak rozpocząć pracę z indeksowaniem dokumentów, które znajdują się w usłudze Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Konfigurowanie indeksatora usługi Azure Data Lake Storage Gen2

Istnieje kilka kroków, które należy wykonać, aby indeksować zawartość z usługi Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Krok 1: Zarejestruj się w wersji zapoznawczej

Zarejestruj się w podglądzie indeksatora Data Lake Storage Gen2, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu do wersji zapoznawczej otrzymasz wiadomość e-mail z potwierdzeniem.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Krok 2: Postępuj zgodnie z instrukcjami konfiguracji indeksowania magazynu obiektów Blob platformy Azure

Po otrzymaniu potwierdzenia, że rejestracja w wersji zapoznawczej zakończyła się pomyślnie, możesz przystąpić do tworzenia potoku indeksowania.

Zawartość i metadane można indeksować z usługi Data Lake Storage Gen2 przy użyciu [interfejsu API REST w wersji 2019-05-06-Preview](search-api-preview.md). Obecnie nie ma obsługi portalu ani SDK .NET.

Indeksowanie zawartości w usłudze Data Lake Storage Gen2 jest identyczne z indeksowaniem zawartości w magazynie obiektów Blob platformy Azure. Aby zrozumieć, jak skonfigurować źródło danych, indeks i indeksator magazynu danych Lake Storage Gen2, zapoznaj się [z instrukcją indeksowania dokumentów w usłudze Azure Blob Storage za pomocą usługi Azure Cognitive Search.](search-howto-indexing-azure-blob-storage.md) Artykuł magazynu obiektów Blob zawiera również informacje o tym, jakie formaty dokumentów są obsługiwane, jakie właściwości metadanych obiektów blob są wyodrębniane, indeksowanie przyrostowe i inne. Te informacje będą takie same dla usługi Data Lake Storage Gen2.

## <a name="access-control"></a>Kontrola dostępu

Usługa Azure Data Lake Storage Gen2 implementuje [model kontroli dostępu,](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) który obsługuje zarówno azure kontroli dostępu opartej na rolach (RBAC) i POSIX-like list kontroli dostępu (Listy kontroli dostępu). Podczas indeksowania zawartości z usługi Data Lake Storage Gen2 usługa Azure Cognitive Search nie wyodrębni informacji RBAC i ACL z zawartości. W rezultacie te informacje nie zostaną uwzględnione w indeksie usługi Azure Cognitive Search.

Jeśli utrzymanie kontroli dostępu dla każdego dokumentu w indeksie jest ważne, to do dewelopera aplikacji do wdrożenia [przycinania zabezpieczeń](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Wykrywanie zmian

Indeksator Data Lake Storage Gen2 obsługuje wykrywanie zmian. Oznacza to, że po uruchomieniu indeksatora tylko ponownie indeksuje zmienionych obiektów `LastModified` blob, zgodnie z ustaleniami sygnatury czasowej obiektu blob.

> [!NOTE] 
> Data Lake Storage Gen2 umożliwia zmianę nazwy katalogów. Gdy katalog zostanie zmieniona, sygnatury czasowe obiektów blob w tym katalogu nie są aktualizowane. W rezultacie indeksator nie będzie ponownie indeksować te obiekty blob. Jeśli potrzebne są obiekty BLOB w katalogu, które mają zostać ponownie zindeksowane po zmianie nazwy `LastModified` katalogu, ponieważ mają one teraz nowe adresy URL, należy zaktualizować sygnaturę czasową dla wszystkich obiektów blob w katalogu, aby indeksator wiedział, aby ponownie je zeksualizować podczas przyszłego uruchomienia.
