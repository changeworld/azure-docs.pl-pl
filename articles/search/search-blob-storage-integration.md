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
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331276"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Dodawanie wyszukiwania pełnotekstowego do danych obiektów blob platformy Azure przy użyciu Azure Search

Wyszukiwanie w różnych typach zawartości przechowywanych w usłudze Azure Blob Storage może być trudnym problemem do rozwiązania. Można jednak indeksować i przeszukiwać zawartość obiektów BLOB za pomocą zaledwie kilku kliknięć przy użyciu [Azure Search](search-what-is-azure-search.md). Azure Search ma wbudowaną integrację do indeksowania poza magazynem obiektów BLOB za pomocą [*indeksatora obiektów BLOB*](search-howto-indexing-azure-blob-storage.md) , który dodaje możliwości rozpoznawania źródła danych do indeksowania.

## <a name="supported-content-types"></a>Obsługiwane typy zawartości

Uruchamiając indeksator obiektów BLOB w kontenerze, można wyodrębnić tekst i metadane z następujących typów zawartości za pomocą pojedynczego zapytania:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Opcjonalnie można dołączyć [wzbogacanie AI](search-blob-ai-integration.md) w formie *zestawu umiejętności* , aby utworzyć nowe informacje i strukturę z obiektów blob, w tym tekstową reprezentację plików obrazów. Dodanie wzbogacania AI rozszerza listę typów zawartości w taki sposób, aby obejmowała JPEG i PNG. Dodaje umiejętności przetwarzania obrazów, takie jak [optyczne rozpoznawanie znaków (OCR)](cognitive-search-skill-ocr.md) i identyfikacja [funkcji wizualizacji](cognitive-search-skill-image-analysis.md) , które umożliwiają indeksowanie zawartości wizualnej znalezionej w każdym obrazie.

## <a name="search-through-your-blob-metadata"></a>Przeszukiwanie metadanych obiektów BLOB
Typowym scenariuszem, który ułatwia sortowanie za pomocą obiektów BLOB dowolnego typu zawartości, jest indeksowanie zarówno niestandardowych metadanych, jak i właściwości systemu dla każdego obiektu BLOB. W ten sposób informacje o wszystkich obiektach Blob są indeksowane niezależnie od typu dokumentu, przechowywane w indeksie na Azure Search. Korzystając z nowego indeksu, można następnie posortować, filtrować i aspektować zawartość całej zawartości magazynu obiektów BLOB.

### <a name="indexing-json-blobs"></a>Indeksowanie obiektów BLOB JSON
Azure Search można skonfigurować do wyodrębniania zawartości strukturalnej znalezionej w obiektach Blob, które zawierają kod JSON. Azure Search może odczytywać obiekty blob JSON i analizować zawartość strukturalną do odpowiednich pól Azure Search dokumentu. Azure Search może również przyjmować obiekty blob, które zawierają tablicę obiektów JSON i mapują każdy element do oddzielnego Azure Search dokumentu. Można ustawić tryb analizowania, który ma wpływ na typ obiektu JSON utworzonego przez indeksator.

## <a name="how-to-get-started"></a>Jak zacząć

Możesz rozpocząć bezpośrednio na stronie portalu konta magazynu. Kliknij przycisk **dodaj Azure Search** , aby uruchomić przepływ, w którym można wybrać istniejącą usługę Azure Search lub utworzyć nową usługę. Jeśli utworzysz nową usługę, wyjdziesz w środowisku portalu Twojego konta magazynu. Możesz przejść z powrotem do strony portalu magazynu i ponownie wybrać opcję **dodaj Azure Search** , w której można wybrać istniejącą usługę. 

![](./media/search-blob-storage-integration/blob-blade.png)

Jeśli masz już istniejącą usługę wyszukiwania w ramach tej samej subskrypcji, kliknij przycisk **dodaj Azure Search** otwiera Kreatora importu danych, dzięki czemu możesz natychmiast przejść od indeksowania, wzbogacania i definicji indeksu.

Możesz również [utworzyć usługę Azure Search](search-create-index-portal.md) i zdefiniować indeksatory obiektów blob, które pobierają zawartość z kontenerów obiektów BLOB.

Poniższe przewodniki Szybki Start i samouczki korzystają z danych obiektów blob:

+ [Indeksowanie obiektów BLOB ze strukturą za pomocą interfejsów API REST](search-semi-structured-data.md)
+ [Tworzenie potoku wzbogacania AI w portalu](cognitive-search-quickstart-blob.md)
+ [Utwórz potok wzbogacania AI wC#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie indeksatora obiektów BLOB](search-howto-indexing-azure-blob-storage.md) 
