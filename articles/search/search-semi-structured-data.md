---
title: 'Samouczek: indeksowanie częściowo struturedch danych w obiektach Blob JSON'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować i przeszukiwać rozbudowane obiekty blob w formacie JSON platformy Azure przy użyciu usługi Azure Wyszukiwanie poznawcze API REST i programu Poster.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0603ad1fbecf33e5880fd7f18d35af51795f8e39
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251995"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>Samouczek REST: indeksowanie i wyszukiwanie danych częściowo strukturalnych (obiektów BLOB JSON) na platformie Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze umożliwia indeksowanie dokumentów i tablic w formacie JSON w usłudze Azure Blob Storage za pomocą [indeksatora](search-indexer-overview.md) , który wie, jak odczytywać dane z częściową strukturą. Częściowo ustrukturyzowane dane zawierają tagi lub oznaczenia, które dzielą zawartość w ramach danych. Dzieli różnicę między danymi niestrukturalnymi, które muszą być w pełni indeksowane i formalnie uporządkowane dane, które są zgodne z modelem danych, takim jak schemat relacyjnej bazy danych, który można indeksować według poszczególnych pól.

W tym samouczku Użyj [interfejsów API REST usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/rest/api/searchservice/) i klienta REST, aby wykonać następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie źródła danych Wyszukiwanie poznawcze platformy Azure dla kontenera obiektów blob platformy Azure
> * Utwórz indeks Wyszukiwanie poznawcze platformy Azure, aby zawierał zawartość z możliwością przeszukiwania
> * Konfigurowanie i uruchamianie indeksatora w celu odczytu kontenera i wyodrębnienia zawartości możliwej do przeszukania z usługi Azure Blob Storage
> * Przeszukać utworzony indeks

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki Start są używane następujące usługi, narzędzia i dane. 

[Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi. 

[Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych.

[Publikowanie aplikacji klasycznych](https://www.getpostman.com/) na potrzeby wysyłania żądań do usługi Azure wyszukiwanie poznawcze.

Plik [Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) zawiera dane używane w tym samouczku. Pobierz i rozpakuj ten plik do własnego folderu. Dane pochodzą z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), konwertowane na format JSON dla tego samouczka.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

1. [Zaloguj się do Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , aby zawierał przykładowe dane. Można ustawić poziom dostępu publicznego na dowolną z jego prawidłowych wartości.

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "Przekaż na pasku poleceń")

1. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   ![Przekazywanie plików](media/search-semi-structured-data/clinicalupload.png "Przekazywanie plików")

Po zakończeniu przekazywania pliki powinny pojawić się w podfolderze wewnątrz kontenera danych.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie znasz tego narzędzia, zobacz [Eksplorowanie interfejsów API REST platformy Azure wyszukiwanie poznawcze przy użyciu programu Poster](search-get-started-postman.md).

Metoda żądania dla każdego wywołania w tym samouczku to **post**. Klucze nagłówka to „Content-type” i „api-key”. Wartości kluczy nagłówka to odpowiednio „application/json” i „admin key” (klucz administratora to symbol zastępczy podstawowego klucza wyszukiwania). Treść to miejsce, w którym umieszcza się właściwą zawartość wywołania. W zależności od używanego klienta mogą występować pewne różnice w sposobie konstruowania zapytania, ale to są jego podstawy.

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/postmanoverview.png)

Używamy narzędzia Postman do wykonania trzech wywołań interfejsu API do usługi wyszukiwania w celu utworzenia źródła danych, indeksu i indeksatora. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.

Ciągi zapytań muszą określać wersję interfejsu API i każde wywołanie powinno zwrócić **201 utworzony**. Ogólnie dostępna wersja interfejsu API do korzystania z tablic JSON jest `2019-05-06`.

Wykonaj trzy następujące wywołania interfejsu API z poziomu klienta REST.

## <a name="create-a-data-source"></a>Tworzenie źródła danych

[Interfejs API tworzenia źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) tworzy obiekt wyszukiwanie poznawcze platformy Azure, który określa, jakie dane mają być indeksowane.

Punkt końcowy tego wywołania to `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Zastąp element `[service name]` nazwą usługi wyszukiwania. 

W przypadku tego wywołania treść żądania musi zawierać nazwę konta magazynu, klucz konta magazynu i nazwę kontenera obiektów BLOB. Klucz konta magazynu można znaleźć w witrynie Azure Portal w obszarze **Klucze dostępu** konta magazynu. Lokalizację pokazano na poniższej ilustracji:

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/storagekeys.png)

Pamiętaj, aby zastąpić `[storage account name]`, `[storage account key]`i `[blob container name]` w treści wywołania przed wykonaniem wywołania.

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
    
Drugie wywołanie to [Tworzenie interfejsu API indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index), tworzenie indeksu wyszukiwanie poznawcze platformy Azure, w którym są przechowywane wszystkie dane z możliwością wyszukiwania. Indeks określa wszystkie parametry i ich atrybuty.

Adres URL dla tego wywołania wygląda następująco: `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Zastąp element `[service name]` nazwą usługi wyszukiwania.

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

## <a name="create-and-run-an-indexer"></a>Utwórz i uruchom indeksator

Indeksator nawiązuje połączenie ze źródłem danych, importuje dane do docelowego indeksu wyszukiwania i opcjonalnie zapewnia harmonogram do automatyzowania odświeżania danych. Interfejs API REST służy do [tworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Adres URL dla tego wywołania wygląda następująco: `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Zastąp element `[service name]` nazwą usługi wyszukiwania.

Najpierw zastąp adres URL. Następnie skopiuj i wklej następujący kod do treści i Wyślij żądanie. Żądanie jest przetwarzane od razu. Po powrocie odpowiedzi odpowiedź będzie miała indeks, który jest przeszukiwany w trybie pełnotekstowym.

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

Możesz rozpocząć wyszukiwanie zaraz po załadowaniu pierwszego dokumentu. W przypadku tego zadania Użyj [**Eksploratora wyszukiwania**](search-explorer.md) w portalu.

W Azure Portal Otwórz stronę **Przegląd** usługi wyszukiwania i Znajdź Indeks utworzony na liście **indeksy** .

Upewnij się, że wybrano właśnie utworzony indeks. 

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

Najszybszym sposobem oczyszczenia po samouczku jest usunięcie grupy zasobów zawierającej usługę Wyszukiwanie poznawcze platformy Azure. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. Nazwa grupy zasobów w portalu znajduje się na stronie Przegląd usługi Azure Wyszukiwanie poznawcze.

## <a name="next-steps"></a>Następne kroki

Istnieje kilka metod i wiele opcji indeksowania obiektów BLOB JSON. W następnym kroku Przejrzyj i Przetestuj różne opcje, aby zobaczyć, co najlepiej sprawdza się w danym scenariuszu.

> [!div class="nextstepaction"]
> [Jak indeksować obiekty blob JSON przy użyciu usługi Azure Wyszukiwanie poznawcze BLOB Indexer](search-howto-index-json-blobs.md)
