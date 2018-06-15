---
title: Przeszukiwanie częściowo ustrukturyzowanych danych w magazynie w chmurze platformy Azure
description: Przeszukiwanie częściowo ustrukturyzowanych danych typu blob przy użyciu usługi Azure Search.
author: roygara
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.openlocfilehash: 7579862e132724d101e4267023afd9e3336bc3b1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795045"
---
# <a name="part-2-search-semi-structured-data-in-cloud-storage"></a>Część 2. Przeszukiwanie częściowo ustrukturyzowanych danych w magazynie w chmurze

Z tego dwuczęściowego samouczka dowiesz się, jak wyszukiwać częściowo ustrukturyzowane dane oraz dane bez struktury za pomocą usługi Azure Search. W [części 1](../storage/blobs/storage-unstructured-search.md) opisano proces wyszukiwania względem danych bez struktury, a także ważne wymagania wstępne dotyczące tego samouczka, takie jak utworzenie konta magazynu. 

Część 2 koncentruje się na danych częściowo ustrukturyzowanych, takich jak JSON, przechowywanych w obiektach blob platformy Azure. Częściowo ustrukturyzowane dane zawierają tagi lub oznaczenia, które dzielą zawartość w ramach danych. Różnią się od danych bez struktury, które muszą być indeksowane w całości, oraz formalnie ustrukturyzowanych danych, które są zgodne z modelem danych, takim jak schemat relacyjnej bazy danych z możliwością przeszukiwania pod kątem poszczególnych pól.

W części 2 dowiesz się, jak:

> [!div class="checklist"]
> * Skonfigurować źródło danych usługi Azure Search dla kontenera obiektów blob platformy Azure
> * Utworzyć i wypełnić indeks oraz indeksator usługi Azure Search na potrzeby przeszukiwania kontenera i wyodrębniania zawartości, którą można przeszukiwać
> * Przeszukać utworzony indeks

> [!NOTE]
> Ten samouczek opiera się na obsłudze tablic JSON, które występują obecnie w usłudze Azure Search w wersji zapoznawczej. Funkcja nie jest dostępna w portalu. Z tego powodu korzystamy z interfejsu API REST w wersji zapoznawczej, który zawiera nie tylko tę funkcję, ale również narzędzie klienta REST przeznaczone do wywoływania interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [poprzedniego samouczka](../storage/blobs/storage-unstructured-search.md), w którym utworzono konto magazynu oraz usługę wyszukiwania.

* Zainstalowany klient REST i wiedza na temat tworzenia żądań HTTP. Na potrzeby tego samouczka użyto narzędzia [Postman](https://www.getpostman.com/). Możesz także użyć innego klienta REST, jeśli jest Ci lepiej znany.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie znasz tego narzędzia, zobacz [Odkrywaj interfejsy API REST usługi Azure Search przy użyciu narzędzia Fiddler lub Postman](search-fiddler.md), aby uzyskać więcej informacji.

Metodą żądania dla każdego wywołania w tym samouczku jest „POST”. Klucze nagłówka to „Content-type” i „api-key”. Wartości kluczy nagłówka to odpowiednio „application/json” i „admin key” (klucz administratora to symbol zastępczy podstawowego klucza wyszukiwania). Treść to miejsce, w którym umieszcza się właściwą zawartość wywołania. W zależności od używanego klienta mogą występować pewne różnice w sposobie konstruowania zapytania, ale to są jego podstawy.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/postmanoverview.png)

Wywołania REST omówione w tym samouczku wymagają użycia klucza wyszukiwania interfejsu API. Klucz interfejsu API można znaleźć w obszarze **Klucze** wewnątrz usługi wyszukiwania. Ten klucz interfejsu API musi znajdować się w nagłówku każdego wywołania interfejsu API tworzonego w ramach tego samouczka (zastąp nim wartość „admin key” na poprzednim zrzucie ekranu). Klucz należy zachować, ponieważ jest wymagany w przypadku każdego wywołania.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Pobieranie przykładowych danych

Przygotowaliśmy dla Ciebie przykładowy zestaw danych. **Pobierz plik [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** i rozpakuj go do własnego folderu.

Przykładowe pliki JSON były pierwotnie plikami tekstowymi uzyskanymi z witryny [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Dla Twojej wygody zostały przekonwertowane na format JSON.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny [Azure Portal](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Przekazywanie przykładowych danych

W witrynie Azure Portal przejdź z powrotem do konta magazynu utworzonego w [poprzednim samouczku](../storage/blobs/storage-unstructured-search.md). Następnie otwórz kontener **danych** i kliknij polecenie **Przekaż**.

Kliknij pozycję **Zaawansowane**, wprowadź wyrażenie „clinical-trials-json”, a następnie przekaż wszystkie pobrane pliki w formacie JSON.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/clinicalupload.png)

Po zakończeniu przekazywania pliki powinny pojawić się w podfolderze wewnątrz kontenera danych.

## <a name="connect-your-search-service-to-your-container"></a>Łączenie usługi wyszukiwania z kontenerem

Używamy narzędzia Postman do wykonania trzech wywołań interfejsu API do usługi wyszukiwania w celu utworzenia źródła danych, indeksu i indeksatora. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.

Ciąg zapytania musi zawierać element **api-version=2016-09-01-Preview**, a każde wywołanie powinno zwrócić wartość **201 Utworzono**. Ogólnie dostępna wersja interfejsu API nie ma jeszcze możliwości obsługi formatu json jako jsonArray. Obecnie tylko wersja zapoznawcza interfejsu API została zaopatrzona w taką opcję.

Wykonaj trzy następujące wywołania interfejsu API z poziomu klienta REST.

### <a name="create-a-datasource"></a>Tworzenie źródła danych

Źródło danych określa, które dane mają być indeksowane.

Punkt końcowy tego wywołania to `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Zastąp element `[service name]` nazwą usługi wyszukiwania.

W przypadku tego wywołania potrzebna jest nazwa konta magazynu oraz klucz konta magazynu. Klucz konta magazynu można znaleźć w witrynie Azure Portal w obszarze **Klucze dostępu** konta magazynu. Lokalizację pokazano na poniższej ilustracji:

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

### <a name="create-an-index"></a>Tworzenie indeksu
    
Drugie wywołanie interfejsu API powoduje utworzenie indeksu. Indeks określa wszystkie parametry i ich atrybuty.

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

### <a name="create-an-indexer"></a>Tworzenie indeksatora

Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i opcjonalnie zapewnia harmonogram w celu zautomatyzowania odświeżania danych.

Adres URL dla tego wywołania wygląda następująco: `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Zastąp element `[service name]` nazwą usługi wyszukiwania.

Najpierw zastąp adres URL. Następnie skopiuj i wklej następujący kod do treści wywołania i uruchom zapytanie.

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

Teraz, gdy usługa wyszukiwania została już połączona z kontenerem danych, można rozpocząć wyszukiwanie plików.

Otwórz witrynę Azure Portal i przejdź z powrotem do usługi wyszukiwania. Podobnie jak w poprzednim samouczku.

  ![Wyszukiwanie bez struktury](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Wyszukiwanie metadanych zdefiniowanych przez użytkownika

Tak jak wcześniej, zapytania o dane można wykonywać na kilka sposobów: za pomocą wyszukiwania pełnotekstowego, właściwości systemowych lub metadanych zdefiniowanych przez użytkownika. Zarówno właściwości systemowe, jak i metadane zdefiniowane przez użytkownika mogą być przeszukiwane wyłącznie za pomocą parametru `$select`, jeśli podczas tworzenia indeksu docelowego zostały oznaczone jako **możliwe do pobierania**. Po utworzeniu parametrów w indeksie nie można ich zmienić. Można jednak dodać dodatkowe parametry.

Przykładem zapytania podstawowego jest `$select=Gender,metadata_storage_size`, które zwraca wyłącznie te dwa parametry.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/lastquery.png)

Przykład bardziej złożonego zapytania wyglądałby następująco: `$filter=MinimumAge ge 30 and MaximumAge lt 75`. Jego wykonanie zwraca tylko te wyniki, gdzie parametr MinimumAge jest większy niż lub równy wartości 30, a parametr MaximumAge jest mniejszy niż 75.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/metadatashort.png)

Jeśli chcesz poeksperymentować, możesz samodzielnie wypróbować kilka zapytań. Pamiętaj, że możesz użyć operatorów logicznych (and, or, not) i operatorów porównania (eq, ne, gt, lt, ge, le). W porównaniach ciągów jest rozróżniana wielkość liter.

Parametr `$filter` działa wyłącznie z metadanymi, które podczas tworzenia indeksu zostały oznaczone jako możliwe do filtrowania.

## <a name="next-steps"></a>Następne kroki

Dzięki temu samouczkowi wiesz już, na czym polega wyszukiwanie częściowo ustrukturyzowanych danych za pomocą usługi Azure Search, w tym:

> [!div class="checklist"]
> * Tworzenie usługi Azure Search przy użyciu interfejsu API REST
> * Używanie usługi Azure Search na potrzeby przeszukiwania kontenera

Skorzystaj z tego linku, aby dowiedzieć się więcej o procesie wyszukiwania.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)