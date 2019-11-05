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
ms.openlocfilehash: dff6473fd01fc2d41c5bb100eefd583afe4175b0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496482"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure — Wersja 2019-05-06-Preview

W tym artykule opisano `api-version=2019-05-06-Preview` wersja interfejsu API REST usługi Search, oferując funkcje eksperymentalne, które nie są jeszcze ogólnie dostępne.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania z celem zebrania opinii i mogą ulec zmianie. Zdecydowanie odradzamy korzystanie z interfejsów API w wersji zapoznawczej w aplikacjach produkcyjnych.


## <a name="new-in-2019-05-06-preview"></a>Nowość w 2019-05-06 — wersja zapoznawcza

[* * Indeksowanie przyrostowe](cognitive-search-incremental-indexing-conceptual.md) jest nowym trybem do indeksowania, który dodaje stan i buforowanie, umożliwiając ponowne używanie istniejących danych wyjściowych, gdy definicje danych, indeksatorów i zestawu umiejętności nie są zmieniane. Ta funkcja ma zastosowanie tylko do wzbogacania za pomocą zestawu umiejętności poznawczych.

[**Magazyn wiedzy**](knowledge-store-concept-intro.md) to nowe miejsce docelowe potoku wzbogacania opartego na AI. Oprócz indeksu można teraz utrwalać wypełnione struktury danych utworzone podczas indeksowania w usłudze Azure Storage. Możesz kontrolować struktury fizyczne danych za pomocą elementów w zestawu umiejętności, w tym w jaki sposób dane są zapisywane, czy dane są przechowywane w magazynie tabel lub w magazynie obiektów blob, oraz czy istnieje wiele widoków.

[**Klucze szyfrowania zarządzane przez klienta**](search-security-manage-encryption-keys.md) dla szyfrowania po stronie usługi — w trybie spoczynku są również nową funkcją w wersji zapoznawczej. Oprócz wbudowanego szyfrowania, które jest zarządzane przez firmę Microsoft, można zastosować dodatkową warstwę szyfrowania, w której jesteś jedynym właścicielem kluczy.

## <a name="other-preview-features"></a>Inne funkcje w wersji zapoznawczej

Funkcje ogłoszone we wcześniejszych wersjach zapoznawczych nadal są w publicznej wersji zapoznawczej. Jeśli wywołujesz interfejs API z wcześniejszą wersją interfejsu API w wersji zapoznawczej, możesz nadal korzystać z tej wersji lub przełączać się do `2019-05-06-Preview` bez zmian w oczekiwanym zachowaniu.

+ [parametr zapytania moreLikeThis](search-more-like-this.md) wyszukuje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. 
* [Indeksowanie obiektów BLOB CSV](search-howto-index-csv-blobs.md) tworzy jeden dokument na wiersz, w przeciwieństwie do jednego dokumentu na obiekt BLOB tekstu.
* [Obsługa interfejsu API MongoDB dla indeksatorów Cosmos DB](search-howto-index-cosmosdb.md) jest w wersji zapoznawczej.


## <a name="how-to-call-a-preview-api"></a>Jak wywołać interfejs API w wersji zapoznawczej

Starsze wersje zapoznawcze nadal działają, ale stają się nieodświeżone w czasie. Jeśli kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview`, te wywołania są nadal ważne. Jednak tylko Najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API podglądu.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Wyszukiwanie poznawcze jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą usługi [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/)