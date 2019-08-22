---
title: Dodawanie wyszukiwania pełnotekstowego do usługi Azure Blob Storage — Azure Search
description: Przeszukiwanie zawartości tekstu w usłudze Azure Blob Storage w celu Azure Search indeksowania w kodzie przy użyciu interfejsu API REST protokołu HTTP.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: nitinme
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: f0801931b57302ae1d627dab783a40d2407c19ac
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650088"
---
# <a name="searching-blob-storage-with-azure-search"></a>Przeszukiwanie magazynu obiektów blob za pomocą usługi Azure Search

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

## <a name="quick-start"></a>Szybki start
Azure Search można dodać do obiektów BLOB bezpośrednio ze strony portalu usługi BLOB Storage.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknij przycisk **dodaj Azure Search** , aby uruchomić przepływ, w którym można wybrać istniejącą usługę Azure Search lub utworzyć nową usługę. Jeśli utworzysz nową usługę, wyjdziesz w środowisku portalu Twojego konta magazynu. Możesz przejść z powrotem do strony portalu magazynu i ponownie wybrać opcję **dodaj Azure Search** , w której można wybrać istniejącą usługę.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat Azure Search obiektu BLOB Indexer zawiera pełna [Dokumentacja](https://aka.ms/azsblobindexer).
