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
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302897"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Dodawanie wyszukiwania pełnotekstowego do danych obiektów blob platformy Azure przy użyciu Azure Search

Wyszukiwanie w różnych typach zawartości przechowywanych w usłudze Azure Blob Storage może być trudnym problemem do rozwiązania. Można jednak indeksować i przeszukiwać zawartość obiektów BLOB za pomocą zaledwie kilku kliknięć przy użyciu Azure Search. Wyszukiwanie w usłudze BLOB Storage wymaga aprowizacji usługi Azure Search. Różne limity usług i warstwy cenowe Azure Search można znaleźć na [stronie cennika](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Co to jest usługa Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) to usługa wyszukiwania, która ułatwia deweloperom Dodawanie niezawodnych funkcji wyszukiwania pełnotekstowego do aplikacji internetowych i mobilnych. Jako usługa Azure Search eliminuje konieczność zarządzania dowolnymi infrastrukturami wyszukiwania, a jednocześnie oferuje umowę [SLA na 99,9%](https://aka.ms/azuresearchsla)czasu.

## <a name="index-and-search-enterprise-document-formats"></a>Indeksuj i Przeszukaj formaty dokumentów przedsiębiorstwa
Z obsługą [wyodrębniania dokumentów](https://aka.ms/azsblobindexer) w usłudze Azure Blob Storage można indeksować następującą zawartość:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Przez wyodrębnienie tekstu i metadanych z tych typów plików można przeszukiwać wiele formatów plików przy użyciu jednego zapytania. 

## <a name="search-through-your-blob-metadata"></a>Przeszukiwanie metadanych obiektów BLOB
Typowym scenariuszem, który ułatwia sortowanie za pomocą obiektów BLOB dowolnego typu zawartości, jest indeksowanie zarówno niestandardowych metadanych, jak i właściwości systemu dla każdego obiektu BLOB. W ten sposób informacje o wszystkich obiektach Blob są indeksowane niezależnie od typu dokumentu. Następnie można przystępować do sortowania, filtrowania i aspektów całej zawartości magazynu obiektów BLOB.

[Dowiedz się więcej na temat indeksowania metadanych obiektów BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Wyszukiwanie obrazów
Azure Search wyszukiwanie pełnotekstowe, Nawigacja aspektowa i możliwości sortowania można teraz zastosować do metadanych obrazów przechowywanych w obiektach Blob.

Wyszukiwanie poznawcze obejmuje umiejętności przetwarzania obrazów, takie jak [optyczne rozpoznawanie znaków (OCR)](cognitive-search-skill-ocr.md) i identyfikacja [funkcji wizualizacji](cognitive-search-skill-image-analysis.md) , które umożliwiają indeksowanie zawartości wizualnej znalezionej w każdym obrazie.

## <a name="index-and-search-through-json-blobs"></a>Indeksowanie obiektów BLOB JSON i ich wyszukiwanie
Azure Search można skonfigurować do wyodrębniania zawartości strukturalnej znalezionej w obiektach Blob, które zawierają kod JSON. Azure Search może odczytywać obiekty blob JSON i analizować zawartość strukturalną do odpowiednich pól Azure Search dokumentu. Azure Search może również przyjmować obiekty blob, które zawierają tablicę obiektów JSON i mapują każdy element do oddzielnego Azure Search dokumentu.

Analiza JSON nie jest obecnie konfigurowalna za pomocą portalu. [Dowiedz się więcej o analizie JSON w Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Szybki start
Azure Search można dodać do obiektów BLOB bezpośrednio ze strony portalu usługi BLOB Storage.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknij przycisk **dodaj Azure Search** , aby uruchomić przepływ, w którym można wybrać istniejącą usługę Azure Search lub utworzyć nową usługę. Jeśli utworzysz nową usługę, wyjdziesz w środowisku portalu Twojego konta magazynu. Możesz przejść z powrotem do strony portalu magazynu i ponownie wybrać opcję **dodaj Azure Search** , w której można wybrać istniejącą usługę.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat Azure Search obiektu BLOB Indexer zawiera pełna [Dokumentacja](https://aka.ms/azsblobindexer).
