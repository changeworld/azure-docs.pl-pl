---
title: Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza
titleSuffix: Azure Cognitive Search
description: Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure w wersji 2019-05-06 — wersja zapoznawcza obejmuje funkcje eksperymentalne, takie jak magazyn wiedzy i klucze szyfrowania zarządzane przez klienta.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720029"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure — Wersja 2019-05-06-Preview

W tym artykule opisano `api-version=2019-05-06-Preview` wersja interfejsu API REST usługi Search, oferując funkcje eksperymentalne, które nie są jeszcze ogólnie dostępne.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania z celem zebrania opinii i mogą ulec zmianie. Zdecydowanie odradzamy korzystanie z interfejsów API w wersji zapoznawczej w aplikacjach produkcyjnych.


## <a name="new-in-2019-05-06-preview"></a>Nowość w 2019-05-06 — wersja zapoznawcza

+ [Indeksowanie przyrostowe](cognitive-search-incremental-indexing-conceptual.md) jest nowym trybem do indeksowania, który dodaje stan i buforowanie do zestawu umiejętności, umożliwiając ponowne używanie istniejących danych wyjściowych, gdy definicje danych źródłowych, indeksatora i zestawu umiejętności są niezmienione. Ta funkcja ma zastosowanie tylko do wzbogacania zdefiniowanych zestawu umiejętności poznawczych.

+ [Cosmos DB indeksator](search-howto-index-cosmosdb.md) obsługuje interfejs API MongoDB, interfejs API Gremlin i interfejs API Cassandra.

+ [Azure Data Lake Storage Gen2 indeksator](search-howto-index-azure-data-lake-storage.md) może indeksować zawartość i metadane z Data Lake Storage Gen2.

+ [Wyodrębnianie dokumentu (wersja zapoznawcza)](cognitive-search-skill-document-extraction.md) to umiejętność wykorzystywana podczas indeksowania, która pozwala wyodrębnić zawartość pliku z zestawu umiejętności. Wcześniej przed wykonaniem zestawu umiejętności tylko złamanie dokumentu. Po dodaniu tej umiejętności można także wykonać tę operację w ramach wykonywania zestawu umiejętności.

+ [Tłumaczenie tekstu (wersja zapoznawcza)](cognitive-search-skill-text-translation.md) to umiejętność wykorzystywana podczas indeksowania, która oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy.

+ [Magazyn wiedzy](knowledge-store-concept-intro.md) to nowe miejsce docelowe potoku wzbogacania opartego na AI. Struktura danych fizycznych istnieje w usłudze Azure Blob Storage i Azure Table Storage oraz jest tworzona i wypełniana podczas uruchamiania indeksatora, który ma dołączony zestawu umiejętności poznawcze. Definicja samego sklepu wiedzy została określona w definicji zestawu umiejętności. W ramach definicji sklepu merytorycznego można kontrolować struktury fizyczne danych za pomocą elementów *projekcji* , które określają, jak dane są w kształcie, czy dane są przechowywane w magazynie tabel lub w magazynie obiektów blob, oraz czy istnieje wiele widoków.

+ [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) dla szyfrowania po stronie usługi — w trybie spoczynku są również nową funkcją w wersji zapoznawczej. Oprócz wbudowanego szyfrowania, które jest zarządzane przez firmę Microsoft, można zastosować dodatkową warstwę szyfrowania, w której jesteś jedynym właścicielem kluczy.

## <a name="earlier-preview-features"></a>Wcześniejsze funkcje w wersji zapoznawczej

Funkcje ogłoszone we wcześniejszych wersjach zapoznawczych nadal są w publicznej wersji zapoznawczej. Jeśli wywołujesz interfejs API z wcześniejszą wersją interfejsu API w wersji zapoznawczej, możesz nadal korzystać z tej wersji lub przełączać się do `2019-05-06-Preview` bez zmian w oczekiwanym zachowaniu.

+ [parametr zapytania moreLikeThis](search-more-like-this.md) wyszukuje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. 

+ [Indeksowanie obiektów BLOB CSV](search-howto-index-csv-blobs.md) tworzy jeden dokument na wiersz, w przeciwieństwie do jednego dokumentu na obiekt BLOB tekstu.

## <a name="how-to-call-a-preview-api"></a>Jak wywołać interfejs API w wersji zapoznawczej

Starsze wersje zapoznawcze nadal działają, ale stają się nieodświeżone w czasie. Jeśli kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview`, te wywołania są nadal ważne. Jednak tylko Najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API podglądu.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Wyszukiwanie poznawcze jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą usługi [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/)