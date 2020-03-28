---
title: 'Samouczek: Indeks danych o częściowo ustrukturyzowanych w obiektach blob JSON'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować i przeszukiwać półstrukturajne obiekty blob platformy Azure JSON przy użyciu interfejsów API rest usługi Azure Cognitive Search i listonosza.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: ce3b3839319de38020b968ff8db1ee6713b29c47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269973"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Samouczek: Indeksowanie obiektów blob JSON z usługi Azure Storage przy użyciu funkcji REST

Usługa Azure Cognitive Search może indeksować dokumenty json i tablice w magazynie obiektów blob platformy Azure przy użyciu [indeksatora,](search-indexer-overview.md) który wie, jak odczytywać dane o częściowo ustrukturyzowanej strukturze. Częściowo ustrukturyzowane dane zawierają tagi lub oznaczenia, które dzielą zawartość w ramach danych. Dzieli różnicę między niestrukturalnych danych, które muszą być w pełni indeksowane i formalnie strukturalnych danych, które przylega do modelu danych, takich jak schemat relacyjnej bazy danych, które mogą być indeksowane na podstawie pola.

W tym samouczku użyto interfejsów API postmana i [rest wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie źródła danych usługi Azure Cognitive Search dla kontenera obiektów blob platformy Azure
> * Tworzenie indeksu usługi Azure Cognitive Search w celu umieszczenia zawartości z wyszukuj
> * Konfigurowanie i uruchamianie indeksatora w celu odczytywania kontenera i wyodrębniania zawartości z przeszukiwalnej platformy Azure z magazynu obiektów blob platformy Azure
> * Przeszukać utworzony indeks

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)
+ [Tworzenie](search-create-service-portal.md) lub [znajdowanie istniejącej usługi wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby zaakceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) zawiera dane użyte w tym samouczku. Pobierz i rozpaj ten plik do własnego folderu. Dane pochodzą z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), przekonwertowane na JSON dla tego samouczka.

## <a name="1---create-services"></a>1 - Tworzenie usług

Ten samouczek używa usługi Azure Cognitive Search do indeksowania i zapytań oraz magazynu obiektów Blob platformy Azure w celu dostarczenia danych. 

Jeśli to możliwe, utwórz zarówno w tym samym regionie, jak i w grupie zasobów, aby uzyskać bliskość i łatwość zarządzania. W praktyce twoje konto usługi Azure Storage może znajdować się w dowolnym regionie.

### <a name="start-with-azure-storage"></a>Zacznij od usługi Azure Storage

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/) i kliknij przycisk **+ Utwórz zasób**.

1. Wyszukaj *konto magazynu* i wybierz ofertę konta magazynu firmy Microsoft.

   ![Utwórz konto magazynu](media/cognitive-search-tutorial-blob/storage-account.png "Utwórz konto magazynu")

1. Na karcie Podstawy wymagane są następujące elementy. Zaakceptuj ustawienia domyślne dla wszystkich innych.

   + **Grupa zasobów**. Wybierz istniejącą lub utwórz nową, ale użyj tej samej grupy dla wszystkich usług, aby można było zarządzać nimi zbiorowo.

   + **Nazwa konta magazynu**. Jeśli uważasz, że możesz mieć wiele zasobów tego samego typu, użyj nazwy, aby odróżnić według typu i regionu, na przykład *blobstoragewestus*. 

   + **Lokalizacja**. Jeśli to możliwe, wybierz tę samą lokalizację, która jest używana dla usługi Azure Cognitive Search i Usług Cognitive Services. Pojedyncza lokalizacja powoduje unieważnienie opłat za przepustowość.

   + **Rodzaj konta**. Wybierz *domyślny, StorageV2 (ogólnego przeznaczenia v2)*.

1. Kliknij **przycisk Recenzja + Utwórz,** aby utworzyć usługę.

1. Po utworzeniu kliknij pozycję **Przejdź do zasobu,** aby otworzyć stronę Przegląd.

1. Kliknij pozycję Usługa **obiektów Blobs.**

1. [Utwórz kontener obiektów blob,](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) aby zawierał przykładowe dane. Można ustawić publiczny poziom dostępu na dowolną z jego prawidłowych wartości.

1. Po utworzeniu kontenera otwórz go i wybierz **pozycję Przekaż** na pasku poleceń.

   ![Przekazywanie na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "Przekazywanie na pasku poleceń")

1. Przejdź do folderu zawierającego przykładowe pliki. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   ![Przekazywanie plików](media/search-semi-structured-data/clinicalupload.png "Przekazywanie plików")

Po zakończeniu przekazywania pliki powinny pojawić się w podfolderze wewnątrz kontenera danych.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Następnym zasobem jest usługa Azure Cognitive Search, którą można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten instruktaż, można użyć warstwy Bezpłatna. 

Podobnie jak w tym magazynie obiektów Blob platformy Azure, poświęć chwilę, aby zebrać klucz dostępu. Dalej po rozpoczęciu tworzenia żądań strukturyzowania należy podać punkt końcowy i klucz interfejsu api administratora używany do uwierzytelniania każdego żądania.

### <a name="get-a-key-and-url"></a>Uzyskaj klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

![Uzyskiwanie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza api przy każdym żądaniu wysłanym do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-postman"></a>2 - Konfigurowanie listonosza

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie znasz tego narzędzia, zobacz [Eksplorowanie interfejsów API rest usługi Azure Cognitive Search przy użyciu usługi Postman](search-get-started-postman.md).

Metody żądania dla każdego wywołania w tym samouczku są **POST** i **GET**. Wywrzesz trzy wywołania interfejsu API do usługi wyszukiwania, aby utworzyć źródło danych, indeks i indeksator. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.

W nagłówkach ustaw "Typ zawartości" `application/json` `api-key` i ustaw klucz interfejsu api administratora usługi Azure Cognitive Search. Po ustawieniu nagłówków można ich używać do każdego żądania w tym ćwiczeniu.

  ![Adres URL i nagłówek żądania listonosza](media/search-get-started-postman/postman-url.png "Adres URL i nagłówek żądania listonosza")

Identyfikatory URI muszą określać wersję interfejsu API, a każde wywołanie powinno zwracać **201 Utworzony**. Ogólnie dostępna wersja interfejsu api do korzystania `2019-05-06`z tablic JSON to .

## <a name="3---create-a-data-source"></a>3 - Tworzenie źródła danych

Interfejs [API źródła danych tworzenia](https://docs.microsoft.com/rest/api/searchservice/create-data-source) platformy Azure Cognitive Search, który określa, jakie dane mają być indeksowane.

1. Ustaw punkt końcowy tego `https://[service name].search.windows.net/datasources?api-version=2019-05-06`wywołania na . Zastąp element `[service name]` nazwą usługi wyszukiwania. 

1. Skopiuj następujący JSON do treści żądania.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Zastąp parametry połączenia prawidłowym ciągiem dla swojego konta.

1. Zamień "[nazwa kontenera obiektów blob]" na kontener utworzony dla przykładowych danych. 

1. Wyślij żądanie. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - Tworzenie indeksu
    
Drugie wywołanie to [Tworzenie interfejsu API indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index), tworząc indeks usługi Azure Cognitive Search, który przechowuje wszystkie dane z wyszukuj. Indeks określa wszystkie parametry i ich atrybuty.

1. Ustaw punkt końcowy tego `https://[service name].search.windows.net/indexes?api-version=2019-05-06`wywołania na . Zastąp element `[service name]` nazwą usługi wyszukiwania.

1. Skopiuj następujący JSON do treści żądania.

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
      ]
    }
   ```

1. Wyślij żądanie. Odpowiedź powinna wyglądać następująco:

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Tworzenie i uruchamianie indeksatora

Indeksator łączy się ze źródłem danych, importuje dane do docelowego indeksu wyszukiwania i opcjonalnie udostępnia harmonogram automatyzacji odświeżania danych. Interfejs API REST to [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Ustaw identyfikator URI dla `https://[service name].search.windows.net/indexers?api-version=2019-05-06`tego wywołania na . Zastąp element `[service name]` nazwą usługi wyszukiwania.

1. Skopiuj następujący JSON do treści żądania.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Wyślij żądanie. Żądanie jest przetwarzane natychmiast. Po powrocie odpowiedzi, będziesz miał indeks, który jest pełnotekstowy przeszukiwania. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - Wyszukiwanie plików JSON

Wyszukiwanie można rozpocząć natychmiast po załadowaniu pierwszego dokumentu.

1. Zmień czasownik na **GET**.

1. Ustaw identyfikator URI dla `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2019-05-06&$count=true`tego wywołania na . Zastąp element `[service name]` nazwą usługi wyszukiwania.

1. Wyślij żądanie. Jest to nieokreślona kwerenda wyszukiwania pełnotekstowego, która zwraca wszystkie pola oznaczone jako możliwe do pobrania w indeksie, wraz z liczbą dokumentów. Odpowiedź powinna wyglądać następująco:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Dodaj `$select` parametr kwerendy, aby ograniczyć `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2019-05-06&$count=true`wyniki do mniejszej liczby pól: .  W przypadku tej kwerendy 100 dokumentów jest zgodnych, ale domyślnie usługa Azure Cognitive Search zwraca tylko 50 w wynikach.

   ![Zapytanie sparametryzowane](media/search-semi-structured-data/lastquery.png "Paramterized kwerendy")

1. Przykład kwerendy bardziej złożone `$filter=MinimumAge ge 30 and MaximumAge lt 75`może zawierać , który zwraca tylko wyniki, gdy parametry MinimumAge jest większa lub równa 30 i MaximumAge jest mniejsza niż 75. Zastąp `$select` `$filter` wyrażenie wyrażenie wyrażeniem.

   ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/metadatashort.png)

Można również użyć operatorów logicznych (i, lub, nie) i operatorów porównania (eq, ne, gt, lt, ge, le). W porównaniach ciągów jest rozróżniana wielkość liter. Aby uzyskać więcej informacji i przykładów, zobacz [Tworzenie prostej kwerendy](search-query-simple-examples.md).

> [!NOTE]
> Parametr `$filter` działa wyłącznie z metadanymi, które podczas tworzenia indeksu zostały oznaczone jako możliwe do filtrowania.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalnych rozwoju najbardziej praktyczne podejście do iteracji projektowania jest usunięcie obiektów z usługi Azure Cognitive Search i umożliwić kod, aby je odbudować. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Za pomocą portalu można usuwać indeksy, indeksatory i źródła danych. Możesz też użyć **funkcji DELETE** i udostępnić adresy URL każdemu obiektowi. Następujące polecenie usuwa indeksatora.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza Wszystkie zasoby lub Grupy zasobów w lewym okienku nawigacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawy indeksowania obiektów blob platformy Azure, przyjrzyjmy się bliżej konfiguracji indeksatora dla obiektów blob JSON w usłudze Azure Storage.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksowania obiektów blob JSON](search-howto-index-json-blobs.md)