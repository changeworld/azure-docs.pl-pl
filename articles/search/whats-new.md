---
title: Powiadomienia o nowych funkcjach
titleSuffix: Azure Cognitive Search
description: Anonse nowych i ulepszonych funkcji, w tym zmiana nazwy usługi Azure Search na platformę Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f35fcc2604866c2ff5a330b65ae844be0eed9476
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422398"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co nowego w usłudze Azure Wyszukiwanie poznawcze

Dowiedz się, co nowego w usłudze. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nowa nazwa usługi dla Azure Search

Azure Search teraz zmieniono nazwę na **platformę Azure wyszukiwanie poznawcze** w celu odzwierciedlenia rozszerzonego użycia umiejętności poznawczych i przetwarzania AI w operacjach podstawowych. Chociaż umiejętności poznawcze dodają nowe możliwości, korzystanie z AI jest absolutnie opcjonalne. Możesz nadal używać platformy Azure Wyszukiwanie poznawcze bez systemu AI do kompilowania rozbudowanych rozwiązań wyszukiwania pełnotekstowego za pośrednictwem prywatnej, heterogenicznychowej zawartości opartej na tekście w indeksie tworzonym i zarządzanym w chmurze. 

Wersje interfejsu API, pakiety NuGet, przestrzenie nazw i punkty końcowe nie są zmieniane. Zmiany nazwy usługi nie wpływają na istniejące rozwiązania wyszukiwania.

## <a name="feature-announcements"></a>Anonse funkcji

4 listopada 2019 — Konferencja zapłonowa

+ [Indeksowanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) pozwala na wybór i wybór kroków, które mają zostać przetworzone podczas wprowadzania modyfikacji do potoku wzbogacania. Indeksowanie przyrostowe jest przydatne, jeśli masz zawartość obrazu, która została wcześniej przeanalizowana. Dane wyjściowe analizy kosztów są przechowywane, a następnie używane jako podstawa do dodatkowego indeksowania lub wzbogacania.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Wyodrębnianie dokumentu (wersja zapoznawcza)](cognitive-search-skill-document-extraction.md) to umiejętność wykorzystywana podczas indeksowania, która pozwala wyodrębnić zawartość pliku z zestawu umiejętności. Wcześniej przed wykonaniem zestawu umiejętności tylko złamanie dokumentu. Po dodaniu tej umiejętności można także wykonać tę operację w ramach wykonywania zestawu umiejętności.

+ [Tłumaczenie tekstu (wersja zapoznawcza)](cognitive-search-skill-text-translation.md) to umiejętność wykorzystywana podczas indeksowania, która oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy.

+ [Szablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) mogą szybko Rozpocznij pracę wizualizacje i analizę wzbogaconej zawartości w sklepie z Power BI Desktop. Ten szablon jest przeznaczony dla projekcji tabel platformy Azure utworzonych za pomocą [Kreatora importu danych](knowledge-store-create-portal.md).

+ W indeksatorach są teraz obsługiwane [Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) [Cosmos DB, interfejs API Gremlin (wersja](search-howto-index-cosmosdb.md)zapoznawcza) i [Cosmos DB interfejs API Cassandra (wersja zapoznawcza)](search-howto-index-cosmosdb.md) . Możesz zarejestrować się, korzystając z [tego formularza](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu w programie w wersji zapoznawczej otrzymasz wiadomość e-mail z potwierdzeniem.

Lipiec 24 2019

+ Ogólnie dostępna w [chmurze Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji usługi](https://azure.microsoft.com/updates/?product=search&status=all) dla systemu Azure wyszukiwanie poznawcze można znaleźć w witrynie sieci Web systemu Azure.