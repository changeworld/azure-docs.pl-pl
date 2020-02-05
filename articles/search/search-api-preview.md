---
title: Funkcje w wersji zapoznawczej interfejsu API REST
titleSuffix: Azure Cognitive Search
description: Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure w wersji 2019-05-06 — wersja zapoznawcza obejmuje funkcje eksperymentalne, takie jak magazyn wiedzy i buforowanie indeksatora w celu wzbogacania przyrostowego.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fd21a4b821e1911e94d542a0922e5269786c365d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991069"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkcje w wersji zapoznawczej na platformie Azure Wyszukiwanie poznawcze

W tym artykule wymieniono funkcje obecnie dostępne w wersji zapoznawczej. Funkcje, które przechodzą z wersji zapoznawczej do ogólnej dostępności, są usuwane z tej listy. Możesz sprawdzić [Aktualizacje usług](https://azure.microsoft.com/updates/?product=search) lub [co nowego](whats-new.md) w anonsach dotyczących ogólnej dostępności.

W przypadku niektórych funkcji w wersji zapoznawczej w portalu i zestawie .NET SDK interfejs API REST zawsze ma funkcje w wersji zapoznawczej. 

+ W przypadku operacji wyszukiwania bieżąca wersja interfejsu API w wersji zapoznawczej jest [`2019-05-06-Preview`](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)
+ W przypadku operacji zarządzania bieżąca wersja zapoznawcza jest [`2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)

> [!IMPORTANT]
> Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funkcje wzbogacania AI

Zapoznaj się z najnowszymi ulepszeniami wzbogacenia AI za pomocą [interfejsu API wyszukiwania w wersji zapoznawczej](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

+ [Umiejętność wyszukiwania jednostek niestandardowych (wersja zapoznawcza)](cognitive-search-skill-custom-entity-lookup.md ) jest umiejętnością poznawczej, która szuka tekstu na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykieta wszystkie dokumenty z dowolnymi zgodnymi jednostkami. Umiejętność obsługuje również stopień dopasowywania rozmytego, który można zastosować, aby znaleźć dopasowania, które są podobne, ale nie są bardzo dokładne. 

+ [Umiejętność wykrywania danych osobowych (wersja zapoznawcza)](cognitive-search-skill-pii-detection.md) to umiejętność wykorzystywana podczas indeksowania, która wyodrębnia dane osobowe z tekstu wejściowego i umożliwia zamaskowanie tego tekstu na różne sposoby.

+ [Wzbogacanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) dodaje buforowanie do potoku wzbogacania, umożliwiając ponowne użycie istniejących danych wyjściowych, jeśli dopuszczająca modyfikacja, taka jak aktualizacja zestawu umiejętności lub innego obiektu, nie spowoduje zmiany zawartości. Buforowanie dotyczy tylko ulepszonych dokumentów utworzonych przez zestawu umiejętności.

+ [Magazyn merytoryczny (wersja zapoznawcza)](knowledge-store-concept-intro.md) to nowe miejsce docelowe potoku wzbogacania opartego na AI. Struktura danych fizycznych istnieje w usłudze Azure Blob Storage i Azure Table Storage oraz jest tworzona i wypełniana podczas uruchamiania indeksatora, który ma dołączony zestawu umiejętności poznawcze. Definicja samego sklepu wiedzy została określona w definicji zestawu umiejętności. W ramach definicji sklepu merytorycznego można kontrolować struktury fizyczne danych za pomocą elementów *projekcji* , które określają, jak dane są w kształcie, czy dane są przechowywane w magazynie tabel lub w magazynie obiektów blob, oraz czy istnieje wiele widoków.

## <a name="indexing-and-query-features"></a>Funkcje indeksowania i zapytania

Funkcje programu Indexer Preview są dostępne w interfejsie API wyszukiwania w wersji zapoznawczej. 

+ [Cosmos DB indeksator](search-howto-index-cosmosdb.md) obsługuje interfejs API MongoDB (wersja zapoznawcza), interfejs Gremlin API (wersja zapoznawcza) i interfejs API Cassandra (wersja zapoznawcza).

+ [Azure Data Lake Storage Gen2 indeksator (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) może indeksować zawartość i metadane z Data Lake Storage Gen2.

+ [parametr zapytania moreLikeThis (wersja zapoznawcza)](search-more-like-this.md) umożliwia znalezienie dokumentów, które są istotne dla określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. 

## <a name="management-features"></a>Funkcje zarządzania

+ [Obsługa prywatnego punktu końcowego](service-create-private-endpoint.md) za pomocą [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) interfejsu API REST zarządzania. Można utworzyć usługę, która ma ograniczenia dotyczące dostępu do punktu końcowego.

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