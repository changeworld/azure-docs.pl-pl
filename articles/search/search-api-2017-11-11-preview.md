---
title: 2017 wersja interfejsu API REST usługi Azure Search-11-11-Preview | Dokumentacja firmy Microsoft
description: Azure Search usługi REST 2017 wersja interfejsu API-11-11-Preview obejmuje eksperymentalne funkcje, takie jak synonimy i moreLikeThis wyszukiwania.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: afcc8ac31c45c1a43d3d759ef6f5a1cee8166c0a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Interfejsu API REST usługi Azure Search: 2017 wersji-11-11-Preview
W tym artykule opisano `api-version=2017-11-11-Preview` wersji usługi Azure Search interfejsu API REST, oferty eksperymentalną nie jeszcze ogólnie dostępna.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i doświadczenia z celem zbierania opinii i mogą ulec zmianie. Zdecydowanie odradzamy przy użyciu podglądu interfejsów API w aplikacji produkcyjnych.


## <a name="new-in-this-preview"></a>Nowe w tej wersji zapoznawczej

+ [Wyszukiwanie kognitywnych](cognitive-search-concept-intro.md), nowa funkcja wzbogacenia w usłudze Azure Search znajduje ukryty informacje w źródłach nietekstowych i niesortowalne tekstu, przekształcania go w zawartości wyszukiwanie pełnotekstowe w usłudze Azure Search.

Następujące dwie operacje są wprowadzone lub modyfikowane w wersji zapoznawczej interfejsu API REST. Wszystkie inne interfejsy API REST są takie same, czy wywołać ogólnie dostępna lub Podgląd (np.

+ [Utwórz Skillset (interfejs api-version = 2017-11-11-Preview)](ref-create-skillset.md)

+ [Utwórz indeksator (interfejs api-version = 2017-11-11-Preview)](ref-create-indexer.md)

Wszystkie inne interfejsy API REST są takie same, czy wywołać ogólnie dostępna lub podejrzeć wersji. Na przykład `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` i `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (bez `Preview`) działają tak samo.

## <a name="other-preview-features"></a>Inne funkcje w wersji zapoznawczej

Funkcje z wcześniejszych wersji zapoznawczych są nadal w publicznej wersji zapoznawczej.

+ [Pliki CSV w indeksowanie obiektów Blob Azure](search-howto-index-csv-blobs.md), gdzie każdy wiersz w pliku CSV jest indeksowana jako osobny dokument.

+ [Tablice notacji JSON w indeksowanie obiektów Blob Azure](search-howto-index-json-blobs.md), gdzie każdy element tablicy jest indeksowana jako osobny dokument.

+ [`moreLikeThis` parametr zapytania](search-more-like-this.md) można znaleźć dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została w wcześniejszych wersji zapoznawczych. W przypadku wywołujący ten interfejs API przy użyciu starszej wersji interfejsu api, można użyć tej wersji.


## <a name="how-to-call-a-preview-api"></a>Wywoływanie interfejsu API w wersji zapoznawczej

Starszej wersji zapoznawczych są nadal działa, ale nieodświeżone wraz z upływem czasu. Jeśli kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2015-02-25-Preview`, wywołań są nadal ważne. Jednak tylko najnowsza wersja preview jest odświeżona ulepszenia. 

Następująca składnia przykład ilustruje wywołanie wersja interfejsu API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Usługa Azure Search jest dostępna w różnych wersjach. Aby uzyskać więcej informacji, zobacz [wersji interfejsu API](search-api-versions.md).
