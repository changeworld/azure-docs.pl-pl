---
title: Dodawanie wyszukiwania pełnotekstowego do usługi Azure Blob Storage
titleSuffix: Azure Search
description: Wyodrębnij zawartość i Dodaj strukturę do obiektów blob platformy Azure podczas kompilowania indeksu wyszukiwania pełnotekstowego w Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312185"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Dodawanie wyszukiwania pełnotekstowego do danych obiektów blob platformy Azure przy użyciu Azure Search

Wyszukiwanie w różnych typach zawartości przechowywanych w usłudze Azure Blob Storage może być trudnym problemem do rozwiązania. Można jednak indeksować i przeszukiwać zawartość obiektów BLOB za pomocą zaledwie kilku kliknięć przy użyciu [Azure Search](search-what-is-azure-search.md). Azure Search ma wbudowaną integrację do indeksowania poza magazynem obiektów BLOB za pomocą [*indeksatora obiektów BLOB*](search-howto-indexing-azure-blob-storage.md) , który dodaje możliwości rozpoznawania źródła danych do indeksowania.

## <a name="supported-content-types"></a>Obsługiwane typy zawartości

Uruchamiając indeksator obiektów BLOB w kontenerze, można wyodrębnić tekst i metadane z następujących typów zawartości za pomocą pojedynczego zapytania:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Opcjonalnie można dołączyć [wzbogacanie AI](search-blob-ai-integration.md) w formie *zestawu umiejętności* , aby utworzyć nowe informacje i strukturę z obiektów blob, w tym tekstową reprezentację plików obrazów. Dodanie wzbogacania AI rozszerza listę typów zawartości w taki sposób, aby obejmowała JPEG i PNG. Dodaje umiejętności przetwarzania obrazów, takie jak [optyczne rozpoznawanie znaków (OCR)](cognitive-search-skill-ocr.md) i identyfikacja [funkcji wizualizacji](cognitive-search-skill-image-analysis.md) , które umożliwiają indeksowanie zawartości wizualnej znalezionej w każdym obrazie.

## <a name="search-through-your-blob-metadata"></a>Przeszukiwanie metadanych obiektów BLOB
Typowym scenariuszem, który ułatwia sortowanie za pomocą obiektów BLOB dowolnego typu zawartości, jest indeksowanie zarówno niestandardowych metadanych, jak i właściwości systemu dla każdego obiektu BLOB. W ten sposób informacje o wszystkich obiektach Blob są indeksowane niezależnie od typu dokumentu. Następnie można przystępować do sortowania, filtrowania i aspektów całej zawartości magazynu obiektów BLOB.

[Dowiedz się więcej na temat indeksowania metadanych obiektów BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Indeksowanie obiektów BLOB JSON i ich wyszukiwanie
Azure Search można skonfigurować do wyodrębniania zawartości strukturalnej znalezionej w obiektach Blob, które zawierają kod JSON. Azure Search może odczytywać obiekty blob JSON i analizować zawartość strukturalną do odpowiednich pól Azure Search dokumentu. Azure Search może również przyjmować obiekty blob, które zawierają tablicę obiektów JSON i mapują każdy element do oddzielnego Azure Search dokumentu.

Analiza JSON nie jest obecnie konfigurowalna za pomocą portalu. [Dowiedz się więcej o analizie JSON w Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Szybki start
Azure Search można dodać do obiektów BLOB bezpośrednio ze strony portalu usługi BLOB Storage.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknij przycisk **dodaj Azure Search** , aby uruchomić przepływ, w którym można wybrać istniejącą usługę Azure Search lub utworzyć nową usługę. Jeśli utworzysz nową usługę, wyjdziesz w środowisku portalu Twojego konta magazynu. Możesz przejść z powrotem do strony portalu magazynu i ponownie wybrać opcję **dodaj Azure Search** , w której można wybrać istniejącą usługę.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat Azure Search obiektu BLOB Indexer zawiera pełna [Dokumentacja](https://aka.ms/azsblobindexer).
