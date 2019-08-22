---
title: Wersja zapoznawcza interfejsu API REST dla Azure Search 2019-05-06 — wersja zapoznawcza — Azure Search
description: Interfejs API REST usługi Azure Search w wersji 2019-05-06 — wersja zapoznawcza obejmuje funkcje eksperymentalne, takie jak magazyn wiedzy i klucze szyfrowania zarządzane przez klienta.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649984"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Interfejs API REST usługi Azure Search — wersja 2019-05-06-Preview
W `api-version=2019-05-06-Preview` tym artykule opisano wersję interfejsu API REST usługi Azure Search, oferując funkcje eksperymentalne, które nie są jeszcze ogólnie dostępne.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania z celem zebrania opinii i mogą ulec zmianie. Zdecydowanie odradzamy korzystanie z interfejsów API w wersji zapoznawczej w aplikacjach produkcyjnych.


## <a name="new-in-2019-05-06-preview"></a>Nowość w 2019-05-06 — wersja zapoznawcza

[**Magazyn wiedzy**](knowledge-store-concept-intro.md) to nowe miejsce docelowe potoku wzbogacania opartego na AI. Oprócz indeksu można teraz utrwalać wypełnione struktury danych utworzone podczas indeksowania w usłudze Azure Storage. Możesz kontrolować struktury fizyczne danych za pomocą elementów w zestawu umiejętności, w tym w jaki sposób dane są zapisywane, czy dane są przechowywane w magazynie tabel lub w magazynie obiektów blob, oraz czy istnieje wiele widoków.

[**Klucze szyfrowania zarządzane przez klienta**](search-security-manage-encryption-keys.md) dla szyfrowania po stronie usługi — w trybie spoczynku są również nową funkcją w wersji zapoznawczej. Oprócz wbudowanego szyfrowania, które jest zarządzane przez firmę Microsoft, można zastosować dodatkową warstwę szyfrowania, w której jesteś jedynym właścicielem kluczy.

## <a name="other-preview-features"></a>Inne funkcje w wersji zapoznawczej

Funkcje ogłoszone we wcześniejszych wersjach zapoznawczych nadal są w publicznej wersji zapoznawczej. Jeśli wywołujesz interfejs API z wcześniejszą wersją interfejsu API w wersji zapoznawczej, możesz nadal korzystać z tej wersji lub `2019-05-06-Preview` przełączać się do programu bez zmian w oczekiwanym zachowaniu.

+ [parametr zapytania moreLikeThis](search-more-like-this.md) wyszukuje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. 
* [Indeksowanie obiektów BLOB CSV](search-howto-index-csv-blobs.md) tworzy jeden dokument na wiersz, w przeciwieństwie do jednego dokumentu na obiekt BLOB tekstu.
* [Obsługa interfejsu API MongoDB dla indeksatorów Cosmos DB](search-howto-index-cosmosdb.md) jest w wersji zapoznawczej.


## <a name="how-to-call-a-preview-api"></a>Jak wywołać interfejs API w wersji zapoznawczej

Starsze wersje zapoznawcze nadal działają, ale stają się nieodświeżone w czasie. Jeśli kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview`, te wywołania są nadal ważne. Jednak tylko Najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API podglądu.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Search jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Azure Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą usługi [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/)