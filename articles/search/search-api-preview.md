---
title: Wersja zapoznawcza interfejsu API REST dla usługi Azure Search 2019-05-06-Preview — usługa Azure Search
description: Platformy Azure REST usługi Search wersja interfejsu API 2019-05-06-Preview zawiera eksperymentalne funkcje, takie jak magazyn wiedzy i klucze szyfrowania zarządzanego przez klienta.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5374ff896613dd8f8563a2054be8a92103e63fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523905"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Usługa Azure Search REST api-version 2019-05-06-Preview
W tym artykule opisano `api-version=2019-05-06-Preview` wersję usługi Azure Search REST API, oferty funkcji eksperymentalnych, które nie są jeszcze ogólnie dostępne.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania z celem zbierania opinii i mogą ulec zmianie. Zdecydowanie odradzamy przy użyciu wersji zapoznawczej interfejsów API w aplikacjach produkcyjnych.


## <a name="new-in-2019-05-06-preview"></a>Nowość w wersji 2019-05-06-Preview

[**Magazyn wiedzy** ](knowledge-store-concept-intro.md) jest nowe miejsce docelowe jest oparte na sztucznej Inteligencji wzbogacony potok. Oprócz indeksu można teraz utrwalić struktur danych wypełnione utworzone podczas indeksowania w usłudze Azure storage. Struktury fizyczne swoich danych za pośrednictwem elementy zestawu umiejętności, takich jak ukształtowane dane, czy dane są przechowywane w magazynie tabel lub usługi Blob storage i czy istnieje wiele widoków możesz kontrolować.

[**Klucze szyfrowania zarządzanego przez klienta** ](search-security-manage-encryption-keys.md) po stronie usługi szyfrowania podczas spoczynku również jest nową funkcją w wersji zapoznawczej. Oprócz wbudowanych szyfrowanie w spoczynku zarządzany przez firmę Microsoft można zastosować dodatkową warstwę, gdzie jesteś jedynym właścicielem kluczy szyfrowania.

## <a name="other-preview-features"></a>Inne funkcje w wersji zapoznawczej

Funkcje z ogłoszeniem z wcześniejszych wersji zapoznawczych są nadal w publicznej wersji zapoznawczej. Jeśli w przypadku wywoływania interfejsu API za pomocą wcześniejszych wersji zapoznawczej wersja interfejsu api, możesz nadal korzystać z tej wersji programu lub przełączanie `2019-05-06-Preview` bez wprowadzania zmian w oczekiwane zachowanie.

+ [parametr zapytania moreLikeThis](search-more-like-this.md) znajduje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została w starszych wersjach. 
* [Indeksowanie obiektów blob CSV](search-howto-index-csv-blobs.md) tworzy jeden dokument dla każdego wiersza, w przeciwieństwie do jednego dokumentu na obiekt blob tekstu.
* [Obsługa interfejsu API usługi MongoDB dla indeksatorów usługi Cosmos DB](search-howto-index-cosmosdb.md) jest w wersji zapoznawczej.


## <a name="how-to-call-a-preview-api"></a>Wywoływanie interfejsu API w wersji zapoznawczej

Starsze wersje zapoznawcze są nadal działa, ale stają się nieaktualne wraz z upływem czasu. Jeśli Twój kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview`, te wywołania są nadal ważne. Jednak tylko najnowsza wersja (wersja zapoznawcza) zostanie odświeżona i ulepszenia. 

Następująca składnia przykładzie pokazano wywołanie do wersji zapoznawczej interfejsu API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Search jest dostępna w wielu wersji. Aby uzyskać więcej informacji, zobacz [wersji interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj Dokumentacja referencyjna interfejsu API REST usługi Azure Search. Jeśli napotkasz problemy, poproś nas o pomoc na [StackOverflow](https://stackoverflow.com/) lub [się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/)