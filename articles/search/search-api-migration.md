---
title: Uaktualnij do najnowszej wersji interfejsu API REST usługi Azure Search — usługa Azure Search
description: Różnice w wersjach interfejsu API i naukę akcje, które są wymagane, aby przeprowadzić migrację istniejącego kodu do najnowszej wersji interfejsu API REST usługi Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540690"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Uaktualnij do najnowszej wersji interfejsu API REST usługi Azure Search
Jeśli używasz poprzedniej wersji [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/), ten artykuł pomoże Ci uaktualnienia aplikacji w taki sposób, aby używać najnowszych jest ogólnie dostępna wersja interfejsu API 2019-05-06.

Wersja 2019-05-06 interfejsu API REST zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim zgodne z poprzednimi wersjami, więc zmiana kodu należy wymagać tylko minimalnym nakładzie pracy, w zależności od instalowanej wersji używanego wcześniej. [Kroki niezbędne do uaktualnienia](#UpgradeSteps) opisano zmiany kodu, wymagane do używania nowych funkcji.

> [!NOTE]
> Wystąpienie usługi Azure Search obsługuje wersje zakres interfejsu API REST, w tym wcześniejsze. Można nadal używać tych wersji interfejsu API, ale zalecamy przeprowadzenie migracji kodu do najnowszej wersji, aby umożliwić dostęp nowe możliwości.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Co nowego w wersji 2019-05-06
Wersja 2019-05-06 jest ogólnie dostępna najnowszą wersję interfejsu API REST usługi Azure Search Service. Funkcje, które zostały przeniesione do stanu jest ogólnie dostępna w tej wersji interfejsu API:

* [Autouzupełnianie](index-add-suggesters.md) jest funkcją typeahead kończące wprowadzania częściowo określonego okresu.

* [Typy złożone](search-howto-complex-data-types.md) zapewnia natywną obsługę danych ze strukturą obiektu do indeksu usługi Azure Search.

* [Podczas analizowania tryby JsonLines](search-howto-index-json-blobs.md)częścią usługi Azure Blob indeksowania, tworzy jeden wyszukiwania dokumentów na jednostkę JSON, oddzielonych znakami nowego wiersza.

* [Usługa cognitive search](cognitive-search-concept-intro.md) zapewnia indeksowania wykorzystującego aparatów wzbogacania sztucznej Inteligencji usług Cognitive Services.

Kilka wersji zapoznawczych funkcji pokrywają się z tą aktualizacją jest ogólnie dostępna. Aby przejrzeć listę nowych funkcji zapoznawczych, zobacz [wersji interfejsu api REST wyszukiwania 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

Istniejący kod, zawierające następujące funkcje zostaną przerwane w wersji api-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indeksator dla usługi Azure Cosmos DB — źródło danych jest teraz "type": "cosmosdb"

Jeśli używasz [indeksator usługi Cosmos DB](search-howto-index-cosmosdb.md ), należy zmienić `"type": "documentdb"` do `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Błędy wynik wykonania indeksator nie będzie już stanu

Struktura błąd wykonywanie indeksatora stracił `status` elementu. Ten element został usunięty, ponieważ nie został on udostępniają przydatne informacje.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Indeksator źródła danych interfejsu API nie zwraca już parametry połączenia

Z poziomu interfejsu API wersje 2019-05-06 i 2019-05-06-Preview lub nowszy, źródła danych, interfejs API nie zwraca już parametry połączenia w odpowiedzi żadnych operacji REST. W poprzednich wersjach interfejsu API dla źródła danych utworzone za pomocą wpisu, zwracany jest usługi Azure Search **201** następuje odpowiedzi OData, która zawiera parametry połączenia w postaci zwykłego tekstu.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>O nazwie rozpoznawania jednostek cognitive umiejętności jest już obsługiwany.

Jeśli wywołasz [nazwa jednostki rozpoznawania](cognitive-search-skill-named-entity-recognition.md) umiejętności w swoim kodzie, wywołanie zakończy się niepowodzeniem. Funkcja zastępczy jest [rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md). Można zastąpić odwołanie umiejętności żadne inne zmiany. Podpis interfejsu API jest takie same dla obu wersji. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Jeśli uaktualniasz z poprzedniej wersji ogólnie dostępnej wersji 2017-11-11 lub 2016-09-01, prawdopodobnie nie musisz dokonać zmian w kodzie, inny niż numer wersji. Tylko sytuacji, w których konieczne może być zmiana kodu są:

* Twój kod nie powiedzie się, nierozpoznany właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja powinien ignorować właściwości, których nie rozumie.

* Twój kod będzie się powtarzał żądań interfejsu API i próbuje ponownie wysłać je do nowej wersji interfejsu API. Na przykład, to może się zdarzyć, jeśli aplikacja będzie nadal występować, tokeny kontynuacji zwrócony z interfejsu API wyszukiwania (Aby uzyskać więcej informacji, poszukaj `@search.nextPageParameters` w [dokumentacja interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Jeśli jedno z tych sytuacji odnoszą się do Ciebie, następnie konieczne może być odpowiednio zmień swój kod. W przeciwnym razie żadne zmiany nie powinny być wymagane, jeśli nie chcesz rozpocząć korzystanie z [nowe funkcje](#WhatsNew) wersji 2019-05-06.

Uaktualniania z wersji interfejsu API w wersji zapoznawczej powyższych ma również zastosowanie, ale należy również pamiętać, że niektóre funkcje w wersji zapoznawczej nie są dostępne w wersji 2019-05-06:

* [Zapytania "Więcej następująco"](search-more-like-this.md)
* [Indeksowanie obiektów blob CSV](search-howto-index-csv-blobs.md)
* [Obsługa interfejsu API usługi MongoDB dla indeksatorów usługi Cosmos DB](search-howto-index-cosmosdb.md)

Jeśli Twój kod korzysta z tych funkcji, nie można uaktualnić do interfejsu API w wersji 2019-05-06 bez usuwania swojej użytkowania.

> [!IMPORTANT]
> Interfejsy API (wersja zapoznawcza) są przeznaczone do testowania i oceny, a nie powinny być używane w środowiskach produkcyjnych.
> 

### <a name="upgrading-complex-types"></a>Uaktualnianie typy złożone

Jeśli kod używa typów złożonych starszych wersji interfejsu API w wersji zapoznawczej 2017-11-11-Preview lub 2016-09-01-Preview, istnieją pewne ograniczenia nowe i zmienione funkcje w wersji 2019-05-06, z których należy wiedzieć:

+ Obniżeniu limity głębokość pól podrzędnych i liczby złożonych kolekcji na indeks. Jeśli utworzono indeksów, która przekracza te limity za pomocą wersji interfejsu api (wersja zapoznawcza), dowolne próba zaktualizuj lub utwórz je ponownie przy użyciu interfejsu API w wersji 2019-05-06 zakończy się niepowodzeniem. Dotyczy to Ciebie, należy ponownie zaprojektować schematu mieści się w granicach nowy i następnie odbudowanie indeksu.

+ Brak nowego ograniczenia w wersji api-version 2019-05-06 liczby elementów złożonych kolekcji dla dokumentu. Indeksy są tworzone z dokumentami, która przekracza te limity za pomocą wersji interfejsu api w wersji zapoznawczej, wszelkie próby ponownego poindeksowania danych tych danych przy użyciu interfejsu api-version 2019-05-06 zakończy się niepowodzeniem. Dotyczy to Ciebie, należy zmniejszyć liczbę elementów złożonych kolekcji na dokumencie przed indeksowanie danych.

Aby uzyskać więcej informacji, zobacz [limitów usług dla usługi Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Jak uaktualnić starej struktury typu złożonego

Jeśli Twój kod używa typów złożonych z jednym starsze wersje interfejsu API (wersja zapoznawcza), być może korzystasz formatem definicji indeksu, który wygląda w następujący sposób:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Nowszy format drzewa Definiowanie pól indeksu została wprowadzona w wersji interfejsu API 2017-11-11-Preview. W nowym formacie każde pole złożonych ma kolekcji pól, w którym jego podrzędne pola są definiowane. W interfejsie API w wersji 2019-05-06 ten nowy format jest używany wyłącznie i podjęto próbę utworzenia lub zaktualizowania indeksu w starym formacie zakończy się niepowodzeniem. W przypadku indeksów utworzona za pomocą tego starszego formatu należy zaktualizować je na nowy format, zanim można było zarządzać nimi za pomocą interfejsu API w wersji 2019-05-06 przy użyciu interfejsu API w wersji 2017-11-11-Preview.

Następujące czynności, za pomocą interfejsu API w wersji 2017-11-11-Preview, można zaktualizować "płaską" indeksów do nowego formatu:

1. Wykonaj żądanie GET w celu pobrania indeksu. Jeśli jest już w nowym formacie, wszystko będzie gotowe.

2. Wykonuje translację elementu indeksu z formatu "płaską" do nowego formatu. Musisz napisać kod dla tego, ponieważ w czasie pisania tego dokumentu jest dostępny żaden kod przykładowy.

3. Wykonaj żądanie PUT, aby zaktualizować indeks do nowego formatu. Upewnij się, że nie należy zmieniać wszelkie inne informacje szczegółowe dotyczące indeksu, takie jak możliwość wyszukiwania/filterability pola, ponieważ nie jest to dozwolone przez interfejs API aktualizacji indeksu.

> [!NOTE]
> Nie jest możliwe Zarządzaj indeksami utworzone przy użyciu starego formatu "płaską" w witrynie Azure portal. Uaktualnij indeksów z reprezentacji "płaską" na reprezentację w postaci "drzewa" przy najbliższej sposobności.

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj Dokumentacja referencyjna interfejsu API REST usługi Azure Search. Jeśli napotkasz problemy, poproś nas o pomoc na [StackOverflow](https://stackoverflow.com/) lub [się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/)

