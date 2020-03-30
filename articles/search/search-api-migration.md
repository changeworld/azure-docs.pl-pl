---
title: Uaktualnianie wersji interfejsu API REST
titleSuffix: Azure Cognitive Search
description: Przejrzyj różnice w wersjach interfejsu API i dowiedz się, które akcje są wymagane do migracji istniejącego kodu do najnowszej wersji interfejsu API usługi Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112169"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Uaktualnienie do najnowszej wersji interfejsu API usługi Usługi Azure Cognitive Search REST

Jeśli używasz poprzedniej wersji [interfejsu API REST wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/)ten artykuł pomoże ci uaktualnić aplikację do korzystania z najnowszej ogólnie dostępnej wersji interfejsu API, 2019-05-06.

Wersja 2019-05-06 interfejsu API REST zawiera pewne zmiany z wcześniejszych wersji. Są one w większości zgodne z poprzednimi wersjami, więc zmiana kodu powinna wymagać tylko minimalnego nakładu pracy, w zależności od wersji, której używano wcześniej. [Kroki uaktualnienia](#UpgradeSteps) konspektuje zmiany kodu wymagane do korzystania z nowych funkcji.

> [!NOTE]
> Wystąpienie usługi Azure Cognitive Search obsługuje szereg wersji interfejsu API REST, w tym wcześniejsze. Można nadal używać tych wersji interfejsu API, ale zaleca się migrację kodu do najnowszej wersji, dzięki czemu można uzyskać dostęp do nowych możliwości.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Co nowego w wersji 2019-05-06
Wersja 2019-05-06 jest najnowszą ogólnie dostępną wersją interfejsu API REST. Funkcje, które zostały przeniesione do stanu ogólnie dostępne w tej wersji interfejsu API obejmują:

* [Autouzupełnianie](index-add-suggesters.md) jest funkcją typu w nagłówku, która kończy częściowo określone dane wejściowe terminu.

* [Typy złożone](search-howto-complex-data-types.md) zapewnia natywną obsługę danych obiektów strukturalnych w indeksie wyszukiwania.

* [JsonLines analizowanie trybów](search-howto-index-json-blobs.md), część indeksowania obiektów Blob platformy Azure, tworzy jeden dokument wyszukiwania dla jednostki JSON, który jest oddzielony przez nowy obiekt.

* [Wzbogacanie aI](cognitive-search-concept-intro.md) zapewnia indeksowanie, które wykorzystuje aparaty wzbogacania AI usług Cognitive Services.

Kilka wersji funkcji w wersji zapoznawczej pokrywa się z tą ogólnie dostępną aktualizacją. Aby przejrzeć listę nowych funkcji w wersji zapoznawczej, zobacz [Search REST api-version 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Fundamentalne zmiany

Istniejący kod zawierający następujące funkcje zostanie przerwany na api-version=2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indeksator usługi Azure Cosmos DB — źródło danych jest teraz "type": "cosmosdb"

Jeśli używasz [indeksatora usługi Cosmos DB,](search-howto-index-cosmosdb.md )należy zmienić `"type": "documentdb"` na `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Błędy wyniku wykonania indeksatora nie mają już stanu

Struktura błędów dla wykonywania indeksatora `status` wcześniej miał element. Ten element został usunięty, ponieważ nie dostarcza przydatnych informacji.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Interfejs API źródła danych indeksatora nie zwraca już ciągów połączeń

Począwszy od wersji interfejsu API 2019-05-06 i 2019-05-06-Preview— interfejs API źródła danych nie zwraca już ciągów połączeń w odpowiedzi na żadną operację REST. W poprzednich wersjach interfejsu API dla źródeł danych utworzonych przy użyciu postu usługa Azure Cognitive Search zwróciła **201,** po której następuje odpowiedź OData, która zawierała ciąg połączenia w postaci zwykłego tekstu.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Umiejętności poznawcze rozpoznawania nazwanych jednostek są teraz przerywane

Jeśli wywołasz [umiejętności rozpoznawania nazw w](cognitive-search-skill-named-entity-recognition.md) kodzie, wywołanie zakończy się niepowodzeniem. Funkcją zastępowania jest [rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md). Powinno być możliwe zastąpienie odniesienia umiejętności bez innych zmian. Podpis interfejsu API jest taki sam dla obu wersji. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki uaktualnienia
Jeśli uaktualniasz z poprzedniej wersji GA, 2017-11-11 lub 2016-09-01, prawdopodobnie nie trzeba wprowadzać żadnych zmian w kodzie, z innym niż aby zmienić numer wersji. Jedyne sytuacje, w których może być konieczna zmiana kodu, to:

* Kod kończy się niepowodzeniem, gdy nierozpoznane właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja powinna ignorować właściwości, które nie rozumieją.

* Kod będzie się powtarzał żądania interfejsu API i próbuje ponownie wyśmiać je do nowej wersji interfejsu API. Na przykład może się to zdarzyć, jeśli aplikacja będzie się powtarzać tokeny `@search.nextPageParameters` kontynuacji zwrócone z interfejsu API wyszukiwania (aby uzyskać więcej informacji, poszukaj w [odwołaniu do interfejsu API wyszukiwania).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Jeśli którakolwiek z tych sytuacji dotyczy Ciebie, może być konieczna zmiana kodu. W przeciwnym razie żadne zmiany nie powinny być konieczne, chyba że chcesz rozpocząć korzystanie z [nowych funkcji](#WhatsNew) wersji 2019-05-06.

Jeśli uaktualniasz wersję interfejsu API w wersji zapoznawczej, powyższe również ma zastosowanie, ale należy również pamiętać, że niektóre funkcje w wersji zapoznawczej nie są dostępne w wersji 2019-05-06:

* [Zapytania "Więcej podobnych"](search-more-like-this.md)
* [Indeksowanie obiektów blob CSV](search-howto-index-csv-blobs.md)
* [Obsługa interfejsu API mongodb dla indeksatorów usługi Cosmos DB](search-howto-index-cosmosdb.md)

Jeśli kod korzysta z tych funkcji, nie będzie można uaktualnić do wersji interfejsu API 2019-05-06 bez usuwania użycia ich.

> [!IMPORTANT]
> Podgląd interfejsów API są przeznaczone do testowania i oceny i nie powinny być używane w środowiskach produkcyjnych.
> 

### <a name="upgrading-complex-types"></a>Uaktualnianie typów złożonych

Jeśli kod używa typów złożonych ze starszymi wersjami interfejsu API w wersji 2017-11-11-Preview lub 2016-09-01-Preview, istnieją pewne nowe i zmienione limity w wersji 2019-05-06, o których należy pamiętać:

+ Obniżono limity głębokości podaborników i liczby złożonych kolekcji na indeks. Jeśli utworzono indeksy, które przekraczają te limity przy użyciu wersji zapoznawczej wersji interfejsu API, każda próba ich aktualizacji lub ponownego utworzenia przy użyciu interfejsu API w wersji 2019-05-06 zakończy się niepowodzeniem. Jeśli dotyczy to ciebie, należy przeprojektować schemat, aby zmieścić się w nowych limitów, a następnie odbudować indeks.

+ Istnieje nowy limit w wersji api 2019-05-06 na liczbę elementów złożonych kolekcji na dokument. Jeśli utworzono indeksy z dokumentami, które przekraczają te limity przy użyciu wersji zapoznawcowej wersji interfejsu API, każda próba ponownego indeksowania tych danych przy użyciu wersji interfejsu API 2019-05-06 zakończy się niepowodzeniem. Jeśli dotyczy to użytkownika, należy zmniejszyć liczbę złożonych elementów kolekcji na dokument przed ponownym indeksowania danych.

Aby uzyskać więcej informacji, zobacz [Limity usług dla usługi Azure Cognitive Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Jak uaktualnić starą złożoną strukturę typów

Jeśli kod używa typów złożonych ze starszą wersją interfejsu API w wersji zapoznawczej, być może używasz formatu definicji indeksu, który wygląda następująco:

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

W wersji interfejsu API 2017-11-11-Preview wprowadzono nowszy format podobny do drzewa do definiowania pól indeksu. W nowym formacie każde złożone pole ma kolekcję pól, w której zdefiniowane są jego podpola. W wersji interfejsu API 2019-05-06 ten nowy format jest używany wyłącznie i próby utworzenia lub zaktualizowania indeksu przy użyciu starego formatu zakończy się niepowodzeniem. Jeśli masz indeksy utworzone przy użyciu starego formatu, musisz użyć wersji interfejsu API 2017-11-11-Preview, aby zaktualizować je do nowego formatu, zanim będą mogły być zarządzane przy użyciu interfejsu API w wersji 2019-05-06.

Indeksy "płaskie" można zaktualizować do nowego formatu, wykonując następujące kroki przy użyciu interfejsu API w wersji 2017-11-11-Preview:

1. Wykonaj żądanie GET, aby pobrać indeks. Jeśli jest już w nowym formacie, gotowe.

2. Przetłumacz indeks z formatu "płaskiego" na nowy format. Musisz napisać kod do tego, ponieważ nie ma żadnego przykładowego kodu dostępnego w momencie pisania tego tekstu.

3. Wykonaj żądanie PUT, aby zaktualizować indeks do nowego formatu. Upewnij się, że nie należy zmieniać żadnych innych szczegółów indeksu, takich jak możliwość wyszukiwania/filtrowania pól, ponieważ nie jest to dozwolone przez interfejs API indeksu aktualizacji.

> [!NOTE]
> Nie jest możliwe zarządzanie indeksami utworzonymi przy starym "płaskim" formacie z witryny Azure portal. Proszę uaktualnić indeksy z "płaskiej" reprezentacji do "drzewa" reprezentacji w najbliższym czasie.

## <a name="next-steps"></a>Następne kroki

Przejrzyj dokumentację referencyjną interfejsu API interfejsu API wyszukiwania. Jeśli napotkasz problemy, poproś nas o pomoc w [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API REST usługi wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/)

