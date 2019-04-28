---
title: 'Samouczek: Indeksowanie danych o częściowej lub strutured w obiektach blob JSON — usługa Azure Search'
description: Dowiedz się, jak na indeksowanie i wyszukiwanie częściowo ustrukturyzowane obiektów blob JSON platformy Azure przy użyciu interfejsów API REST usługi Azure Search i narzędzia Postman.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 147f67f40a060f3e274fe1f3fa368ebfd01711b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282165"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Samouczek dotyczący architektury REST: Indeksowanie i wyszukiwanie częściowo ustrukturyzowanych danych (obiektów blob JSON) w usłudze Azure Search

Usługa Azure Search umożliwia indeksowanie dokumentów JSON i tablic w usłudze Azure blob storage przy użyciu [indeksatora](search-indexer-overview.md) który wie, jak odczytać danych z częściową strukturą. Częściowo ustrukturyzowane dane zawierają tagi lub oznaczenia, które dzielą zawartość w ramach danych. Dzieli różnicę między pozbawionych struktury danych, które muszą być w pełni indeksowane i formalnie struktury danych, która jest zgodna z modelem danych, takich jak schemat relacyjnej bazy danych, które mogą być indeksowane w poszczególnych pól.

W tym samouczku [interfejsów API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/) i klienta REST do wykonywania następujących zadań:

> [!div class="checklist"]
> * Skonfigurować źródło danych usługi Azure Search dla kontenera obiektów blob platformy Azure
> * Tworzenie indeksu usługi Azure Search, aby zawierała zawartość do przeszukiwania
> * Konfigurowanie i uruchamianie indeksatora do odczytu kontenera i Wyodrębnij zawartość do przeszukiwania z magazynu obiektów blob platformy Azure
> * Przeszukać utworzony indeks

> [!NOTE]
> Ten samouczek opiera się na obsłudze tablic JSON, które występują obecnie w usłudze Azure Search w wersji zapoznawczej. Funkcja nie jest dostępna w portalu. Z tego powodu korzystamy z interfejsu API REST w wersji zapoznawczej, który zawiera nie tylko tę funkcję, ale również narzędzie klienta REST przeznaczone do wywoływania interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Następujących usług, narzędzi i danych są używane w tym przewodniku Szybki Start. 

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku. 

[Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych.

[Aplikacja klasyczna narzędzia postman](https://www.getpostman.com/) wysyłania żądań do usługi Azure Search.

[Kliniczne trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) zawiera dane używane w ramach tego samouczka. Pobierz i Rozpakuj ten plik do własnego folderu. Dane pochodzą z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)konwersji do formatu JSON na potrzeby tego samouczka.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawiera przykładowe dane. Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "przekazywania na pasku poleceń")

1. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **przekazywanie**.

   ![Przekazywanie plików](media/search-semi-structured-data/clinicalupload.png "przekazywanie plików")

Po zakończeniu przekazywania pliki powinny pojawić się w podfolderze wewnątrz kontenera danych.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie jesteś zaznajomiony z tego narzędzia, zobacz [Eksplorowanie usługi Azure Search interfejsów API REST przy użyciu narzędzia Postman](search-fiddler.md).

Metoda żądania dla każdego wywołania w tym samouczku jest **WPIS**. Klucze nagłówka to „Content-type” i „api-key”. Wartości kluczy nagłówka to odpowiednio „application/json” i „admin key” (klucz administratora to symbol zastępczy podstawowego klucza wyszukiwania). Treść to miejsce, w którym umieszcza się właściwą zawartość wywołania. W zależności od używanego klienta mogą występować pewne różnice w sposobie konstruowania zapytania, ale to są jego podstawy.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/postmanoverview.png)

Używamy narzędzia Postman do wykonania trzech wywołań interfejsu API do usługi wyszukiwania w celu utworzenia źródła danych, indeksu i indeksatora. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.

Ciąg zapytania musi zawierać wersję zapoznawczą interfejsu API (takie jak **parametru api-version = 2017-11-11-Preview**) i każde wywołanie powinno zwrócić **201 utworzono**. Ogólnie dostępna wersja interfejsu API nie ma jeszcze możliwości obsługi formatu json jako jsonArray. Obecnie tylko wersja zapoznawcza interfejsu API została zaopatrzona w taką opcję.

Wykonaj trzy następujące wywołania interfejsu API z poziomu klienta REST.

## <a name="create-a-data-source"></a>Tworzenie źródła danych

[Danych źródłowych interfejsu API tworzenia](https://docs.microsoft.com/rest/api/searchservice/create-data-source)tworzy obiekt usługi Azure Search, który określa, jakie dane mają być indeksowane.

Punkt końcowy tego wywołania to `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Zastąp element `[service name]` nazwą usługi wyszukiwania. 

Dla tego wywołania treść żądania musi zawierać nazwę konta magazynu, klucz konta magazynu i nazwa kontenera obiektów blob. Klucz konta magazynu można znaleźć w witrynie Azure Portal w obszarze **Klucze dostępu** konta magazynu. Lokalizację pokazano na poniższej ilustracji:

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/storagekeys.png)

Upewnij się zastąpić `[storage account name]`, `[storage account key]`, i `[blob container name]` w treści wywołania przed wykonaniem wywołania.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

Odpowiedź powinna wyglądać następująco:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Tworzenie indeksu
    
Drugie wywołanie ma [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-data-source), tworzenie indeksu usługi Azure Search, który przechowuje wszystkie dane z możliwością wyszukiwania. Indeks określa wszystkie parametry i ich atrybuty.

Adres URL dla tego wywołania wygląda następująco: `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Zastąp element `[service name]` nazwą usługi wyszukiwania.

Najpierw zastąp adres URL. Następnie skopiuj i wklej następujący kod do treści wywołania i uruchom zapytanie.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

Odpowiedź powinna wyglądać następująco:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Tworzenie i uruchamianie indeksatora

Indeksator łączy źródło danych, importuje dane do docelowym indeksem wyszukiwania i opcjonalnie zapewnia harmonogram w celu zautomatyzowania odświeżania danych. Interfejs API REST jest [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Adres URL dla tego wywołania wygląda następująco: `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Zastąp element `[service name]` nazwą usługi wyszukiwania.

Najpierw zastąp adres URL. Następnie skopiuj i wklej następujący kod do treści wywołania i wysłać żądanie. Żądanie jest przetwarzane od razu. Gdy odpowiedź wróci będziesz mieć indeksu pełnotekstowego wyszukiwania.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Odpowiedź powinna wyglądać następująco:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Przeszukiwanie plików JSON

Możesz rozpocząć wyszukiwanie zaraz po załadowaniu pierwszy dokument. W tym celu należy użyć [ **Eksploratora wyszukiwania** ](search-explorer.md) w portalu.

W witrynie Azure portal Otwórz usługę wyszukiwania **Przegląd** strony, Znajdź indeks został utworzony w **indeksy** listy.

Pamiętaj wybrać indeks, który został utworzony. Wersja interfejsu API może być w wersji zapoznawczej lub ogólnie dostępnej wersji. Jedynym wymaganiem (wersja zapoznawcza) był dla indeksowania tablic JSON.

  ![Wyszukiwanie bez struktury](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Wyszukiwanie metadanych zdefiniowanych przez użytkownika

Tak jak wcześniej, zapytania o dane można wykonywać na kilka sposobów: za pomocą wyszukiwania pełnotekstowego, właściwości systemowych lub metadanych zdefiniowanych przez użytkownika. Zarówno właściwości systemowe, jak i metadane zdefiniowane przez użytkownika mogą być przeszukiwane wyłącznie za pomocą parametru `$select`, jeśli podczas tworzenia indeksu docelowego zostały oznaczone jako **możliwe do pobierania**. Po utworzeniu parametrów w indeksie nie można ich zmienić. Można jednak dodać dodatkowe parametry.

Przykładem zapytania podstawowego jest `$select=Gender,metadata_storage_size`, które zwraca wyłącznie te dwa parametry.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/lastquery.png)

Przykład bardziej złożonego zapytania wyglądałby następująco: `$filter=MinimumAge ge 30 and MaximumAge lt 75`. Jego wykonanie zwraca tylko te wyniki, gdzie parametr MinimumAge jest większy niż lub równy wartości 30, a parametr MaximumAge jest mniejszy niż 75.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/metadatashort.png)

Jeśli chcesz poeksperymentować, możesz samodzielnie wypróbować kilka zapytań. Pamiętaj, że możesz użyć operatorów logicznych (and, or, not) i operatorów porównania (eq, ne, gt, lt, ge, le). W porównaniach ciągów jest rozróżniana wielkość liter.

Parametr `$filter` działa wyłącznie z metadanymi, które podczas tworzenia indeksu zostały oznaczone jako możliwe do filtrowania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd usługi Azure Search.

## <a name="next-steps"></a>Kolejne kroki

Istnieje kilka metod i wiele opcji indeksowanie obiektów blob JSON. Kolejnym krokiem Przejrzyj i przetestować różne opcje, aby zobaczyć, co sprawdza się najlepiej w przypadku danego scenariusza.

> [!div class="nextstepaction"]
> [Jak indeksować obiektów blob JSON za pomocą indeksatora obiektów Blob platformy Azure Search](search-howto-index-json-blobs.md)