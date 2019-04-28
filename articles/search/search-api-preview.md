---
title: Wersja zapoznawcza interfejsu API REST dla usługi Azure Search 2017-11-11-Preview — usługa Azure Search
description: Usługa Azure REST usługi Search interfejsu API w wersji 2017-11-11-Preview zawiera eksperymentalne funkcje, takie jak synonimów i moreLikeThis wyszukiwania.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 524c1a6d083db02349c7dae9a0131228613dc170
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127097"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Usługa Azure Search REST api-version 2017-11-11-Preview
W tym artykule opisano `api-version=2017-11-11-Preview` wersję usługi Azure Search REST API, oferty funkcji eksperymentalnych, które nie są jeszcze ogólnie dostępne.

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania z celem zbierania opinii i mogą ulec zmianie. Zdecydowanie odradzamy przy użyciu wersji zapoznawczej interfejsów API w aplikacjach produkcyjnych.


## <a name="new-in-2017-11-11-preview"></a>Nowość w wersji 2017-11-11-Preview

[**Autouzupełnianie** ](search-autocomplete-tutorial.md) łączy istniejące [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) do dodania, uzupełniające wpisywania z wyprzedzeniem środowisk na pasku wyszukiwania. Autouzupełnianie zwraca Release candidate terminów zapytania, które użytkownik może wybrać jako ciąg zapytania dla następnego przeszukiwania. Sugestie zwraca rzeczywiste dokumenty zawarte w odpowiedzi częściowe dane wejściowe: wyniki wyszukiwania są natychmiastowe i zmieniać dynamicznie, wraz ze wzrostem natężenia wprowadzanie terminu wyszukiwania, długości i szczegółowością.

[**Usługa cognitive search**](cognitive-search-concept-intro.md), nowa funkcja wzbogacania w usłudze Azure Search znajdzie informacje ukryte w źródłach innych niż tekst i tekstu takimi samymi, przekształcania go w zawartość do przeszukiwania pełnotekstowego w usłudze Azure Search. Następujące zasoby są wprowadzane lub modyfikowane w wersji zapoznawczej interfejsu API REST. Wszystkie inne interfejsy API REST są takie same, czy wywołanie jest ogólnie dostępna, czy wersji w wersji zapoznawczej.

+ [Operations(api-version=2017-11-11-Preview) zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [Tworzenie indeksatora (wersja api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)

Wszystkie inne interfejsy API REST są takie same, niezależnie od tego, jak ustawić wartość api-version. Na przykład `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` i `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (bez `Preview`) są funkcjonalnie równoważne.

## <a name="other-preview-features"></a>Inne funkcje w wersji zapoznawczej

Funkcje z ogłoszeniem z wcześniejszych wersji zapoznawczych są nadal w publicznej wersji zapoznawczej. Jeśli w przypadku wywoływania interfejsu API za pomocą wcześniejszych wersji zapoznawczej wersja interfejsu api, możesz nadal korzystać z tej wersji programu lub przełączanie `2017-11-11-Preview` bez wprowadzania zmian w oczekiwane zachowanie.

+ [Pliki CSV w usłudze Azure Blob indeksowaniu](search-howto-index-csv-blobs.md), wprowadzonych w programie `api-version=2015-02-28-Preview`, pozostaje w wersji zapoznawczej. Ta funkcja jest częścią indeksowanie obiektów Blob platformy Azure i jest wywoływany przez ustawienie parametru. Każdy wiersz w pliku CSV są indeksowane jako oddzielny dokument.

+ [Tablice notacji JSON w usłudze Azure Blob indeksowaniu](search-howto-index-json-blobs.md), wprowadzonych w programie `api-version=2015-02-28-Preview`, pozostaje w wersji zapoznawczej. Ta funkcja jest częścią indeksowanie obiektów Blob platformy Azure i jest wywoływany przez ustawienie parametru. gdzie każdy element w tablicy jest indeksowana jako oddzielny dokument.

+ [parametr zapytania moreLikeThis](search-more-like-this.md) znajduje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została w starszych wersjach. 


## <a name="how-to-call-a-preview-api"></a>Wywoływanie interfejsu API w wersji zapoznawczej

Starsze wersje zapoznawcze są nadal działa, ale stają się nieaktualne wraz z upływem czasu. Jeśli Twój kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2015-02-28-Preview`, te wywołania są nadal ważne. Jednak tylko najnowsza wersja (wersja zapoznawcza) zostanie odświeżona i ulepszenia. 

Następująca składnia przykładzie pokazano wywołanie do wersji zapoznawczej interfejsu API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Usługa Azure Search jest dostępna w wielu wersji. Aby uzyskać więcej informacji, zobacz [wersji interfejsu API](search-api-versions.md).
