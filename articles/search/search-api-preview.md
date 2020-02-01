---
title: Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza
titleSuffix: Azure Cognitive Search
description: Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure w wersji 2019-05-06 — wersja zapoznawcza obejmuje funkcje eksperymentalne, takie jak magazyn wiedzy i buforowanie indeksatora w celu wzbogacania przyrostowego.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 71c6879f467823ab01f4c60ac4d9f26cffcd4eea
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896124"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure — Wersja 2019-05-06-Preview

W tym artykule opisano `api-version=2019-05-06-Preview` wersja interfejsu API REST usługi Search, oferując funkcje eksperymentalne, które nie są jeszcze ogólnie dostępne.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania z celem zebrania opinii i mogą ulec zmianie. Zdecydowanie odradzamy korzystanie z interfejsów API w wersji zapoznawczej w aplikacjach produkcyjnych.

## <a name="features-in-2019-05-06-preview"></a>Funkcje w 2019-05-06 — wersja zapoznawcza

Ta sekcja zawiera listę funkcji mających stan wersji zapoznawczej. Większość została dodana w bieżącym interfejsie API 2019-05-06-Preview, ale niektóre takie jak `moreLikeThis` pochodzą z wcześniejszych wersji zapoznawczych, które zostały dołączone do najnowszego interfejsu API w wersji zapoznawczej. 

Gdy funkcja wersji zapoznawczej będzie ogólnie dostępna, zostanie usunięta z tej listy. Możesz sprawdzić [Aktualizacje usług](https://azure.microsoft.com/updates/?product=search) lub [co nowego](whats-new.md) w anonsach dotyczących ogólnej dostępności.

+ [Wzbogacanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) dodaje buforowanie do potoku wzbogacania, umożliwiając ponowne użycie istniejących danych wyjściowych, jeśli dopuszczająca modyfikacja, taka jak aktualizacja zestawu umiejętności lub innego obiektu, nie spowoduje zmiany zawartości. Buforowanie dotyczy tylko ulepszonych dokumentów utworzonych przez zestawu umiejętności.

+ [Cosmos DB indeksator](search-howto-index-cosmosdb.md) obsługuje interfejs API MongoDB (wersja zapoznawcza), interfejs Gremlin API (wersja zapoznawcza) i interfejs API Cassandra (wersja zapoznawcza).

+ [Azure Data Lake Storage Gen2 indeksator (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) może indeksować zawartość i metadane z Data Lake Storage Gen2.

+ [Magazyn wiedzy](knowledge-store-concept-intro.md) to nowe miejsce docelowe potoku wzbogacania opartego na AI. Struktura danych fizycznych istnieje w usłudze Azure Blob Storage i Azure Table Storage oraz jest tworzona i wypełniana podczas uruchamiania indeksatora, który ma dołączony zestawu umiejętności poznawcze. Definicja samego sklepu wiedzy została określona w definicji zestawu umiejętności. W ramach definicji sklepu merytorycznego można kontrolować struktury fizyczne danych za pomocą elementów *projekcji* , które określają, jak dane są w kształcie, czy dane są przechowywane w magazynie tabel lub w magazynie obiektów blob, oraz czy istnieje wiele widoków.

+ [parametr zapytania moreLikeThis](search-more-like-this.md) wyszukuje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. 

## <a name="earlier-preview-features"></a>Wcześniejsze funkcje w wersji zapoznawczej

Funkcje ogłoszone we wcześniejszych wersjach zapoznawczych, jeśli nie przechodzą do ogólnej dostępności, nadal są w publicznej wersji zapoznawczej. Jeśli wywołujesz interfejs API z wcześniejszą wersją interfejsu API w wersji zapoznawczej, możesz nadal korzystać z tej wersji lub przełączać się do `2019-05-06-Preview` bez zmian w oczekiwanym zachowaniu.

## <a name="how-to-call-a-preview-api"></a>Jak wywołać interfejs API w wersji zapoznawczej

Starsze wersje zapoznawcze nadal działają, ale stają się nieodświeżone w czasie. Jeśli kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview`, te wywołania są nadal ważne. Jednak tylko Najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API podglądu.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Wyszukiwanie poznawcze jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą usługi [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/)