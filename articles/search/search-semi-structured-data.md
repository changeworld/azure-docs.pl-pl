---
title: Samouczek dotyczący wyszukiwania danych JSON w usłudze Azure Blob Storage — Azure Search
description: W tym samouczku pokazano, jak przeszukiwać częściowo ustrukturyzowane dane obiektów blob platformy Azure przy użyciu usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201351"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Samouczek: Przeszukiwanie częściowo ustrukturyzowanych danych w magazynie w chmurze platformy Azure

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

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

[Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) zawiera przykładowe dane.

[Użyj narzędzia Postman](https://www.getpostman.com/) lub innego klienta REST do wysyłania żądań. W następnej sekcji znajdują się instrukcje dotyczące konfigurowania żądania HTTP w narzędziu Postman.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie jesteś zaznajomiony z tego narzędzia, zobacz [Eksplorowanie usługi Azure Search interfejsów API REST przy użyciu narzędzia Postman](search-fiddler.md).

Metodą żądania dla każdego wywołania w tym samouczku jest „POST”. Klucze nagłówka to „Content-type” i „api-key”. Wartości kluczy nagłówka to odpowiednio „application/json” i „admin key” (klucz administratora to symbol zastępczy podstawowego klucza wyszukiwania). Treść to miejsce, w którym umieszcza się właściwą zawartość wywołania. W zależności od używanego klienta mogą występować pewne różnice w sposobie konstruowania zapytania, ale to są jego podstawy.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/postmanoverview.png)

Wywołania REST omówione w tym samouczku wymagają użycia klucza wyszukiwania interfejsu API. Klucz interfejsu API można znaleźć w obszarze **Klucze** wewnątrz usługi wyszukiwania. Ten klucz interfejsu API musi znajdować się w nagłówku każdego wywołania interfejsu API tworzonego w ramach tego samouczka (zastąp nim wartość „admin key” na poprzednim zrzucie ekranu). Klucz należy zachować, ponieważ jest wymagany w przypadku każdego wywołania.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

1. **Pobierz plik [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** i rozpakuj go do własnego folderu. Dane pochodzą z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)konwersji do formatu JSON na potrzeby tego samouczka.

2. Zaloguj się do [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, otwórz **danych** kontenera i kliknij przycisk **przekazywanie**.

3. Kliknij pozycję **Zaawansowane**, wprowadź wyrażenie „clinical-trials-json”, a następnie przekaż wszystkie pobrane pliki w formacie JSON.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/clinicalupload.png)

Po zakończeniu przekazywania pliki powinny pojawić się w podfolderze wewnątrz kontenera danych.

## <a name="connect-your-search-service-to-your-container"></a>Łączenie usługi wyszukiwania z kontenerem

Używamy narzędzia Postman do wykonania trzech wywołań interfejsu API do usługi wyszukiwania w celu utworzenia źródła danych, indeksu i indeksatora. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.

Ciąg zapytania musi zawierać wersję zapoznawczą interfejsu API (takie jak **parametru api-version = 2017-11-11-Preview**) i każde wywołanie powinno zwrócić **201 utworzono**. Ogólnie dostępna wersja interfejsu API nie ma jeszcze możliwości obsługi formatu json jako jsonArray. Obecnie tylko wersja zapoznawcza interfejsu API została zaopatrzona w taką opcję.

Wykonaj trzy następujące wywołania interfejsu API z poziomu klienta REST.

## <a name="create-a-data-source"></a>Tworzenie źródła danych

Źródło danych jest obiektem usługi Azure Search, który określa, jakie dane mają być indeksowane.

Punkt końcowy tego wywołania to `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Zastąp element `[service name]` nazwą usługi wyszukiwania. W przypadku tego wywołania potrzebna jest nazwa konta magazynu oraz klucz konta magazynu. Klucz konta magazynu można znaleźć w witrynie Azure Portal w obszarze **Klucze dostępu** konta magazynu. Lokalizację pokazano na poniższej ilustracji:

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/storagekeys.png)

Przed wykonaniem wywołania należy zmienić nazwy elementów `[storage account name]` i `[storage account key]` w treści wywołania.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
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
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Tworzenie indeksu
    
Drugie wywołanie interfejsu API powoduje utworzenie indeksu usługi Azure Search. Indeks określa wszystkie parametry i ich atrybuty.

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

Indeksator łączy źródło danych, importuje dane do docelowym indeksem wyszukiwania i opcjonalnie zapewnia harmonogram w celu zautomatyzowania odświeżania danych.

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

Teraz można wykonywać zapytania względem indeksu. W tym celu należy użyć [ **Eksploratora wyszukiwania** ](search-explorer.md) w portalu.

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

Do potoku indeksatora możesz dołączyć algorytmy oparte na sztucznej inteligencji. Aby wykonać następny krok, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)