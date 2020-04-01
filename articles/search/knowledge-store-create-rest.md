---
title: Tworzenie magazynu wiedzy (wersja zapoznawcza) przy użyciu funkcji REST
titleSuffix: Azure Cognitive Search
description: Użyj interfejsu API REST i postmana, aby utworzyć magazyn wiedzy usługi Azure Cognitive Search dla utrwalania wzbogacenia z potoku wzbogacania sztucznej inteligencji. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 478a7e03b432006b429c96e03307fd8e494c88ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472319"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Tworzenie magazynu wiedzy przy użyciu restu i listonosza

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

Magazyn wiedzy zawiera dane wyjściowe z potoku wzbogacania usługi Azure Cognitive Search do późniejszej analizy lub innego przetwarzania podrzędnego. Potok wzbogacony o sztuczną inteligencję akceptuje pliki obrazów lub nieustrukturyzowane pliki tekstowe, indeksuje je przy użyciu usługi Azure Cognitive Search, stosuje wzbogacanie sztucznej inteligencji z usług Cognitive Services (takie jak analiza obrazu i przetwarzanie języka naturalnego), a następnie zapisuje wyniki w magazynie wiedzy w usłudze Azure Storage. Aby eksplorować magazyn wiedzy, można użyć narzędzi, takich jak usługa Power BI lub Eksplorator magazynu w witrynie Azure portal.

W tym artykule używasz interfejsu interfejsu INTERFEJSU API REST do pozyskiwania, indeksowania i stosowania wzbogacenia AI do zestawu recenzji hoteli. Opinie o hotelu są importowane do magazynu obiektów Blob platformy Azure. Wyniki są zapisywane jako magazyn wiedzy w usłudze Azure Table Storage.

Po utworzeniu magazynu wiedzy można dowiedzieć się, jak uzyskać dostęp do magazynu wiedzy za pomocą [Eksploratora magazynu](knowledge-store-view-storage-explorer.md) lub [usługi Power BI](knowledge-store-connect-power-bi.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

> [!TIP]
> Zalecamy [aplikację pocztową](https://www.getpostman.com/) dla tego artykułu. [Kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) tego artykułu zawiera kolekcję Listonosz zawierającą wszystkie żądania. 

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

Ten przewodnik Szybki start używa usługi Azure Cognitive Search, usługi Azure Blob storage i [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) dla sztucznej inteligencji. 

Ponieważ obciążenie jest tak małe, usługi Cognitive Services są wykorzystywane za kulisami, aby zapewnić bezpłatne przetwarzanie dla maksymalnie 20 transakcji dziennie. Ponieważ zestaw danych jest tak mały, można pominąć tworzenie lub dołączanie zasobu usług Cognitive Services.

1. [Pobierz HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dane te są danymi recenzji hotelu zapisanymi w pliku CSV (pochodzącymi z Kaggle.com) i zawierają 19 opinii klientów na temat jednego hotelu. 

1. [Utwórz konto magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) lub znajdź istniejące [konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) w ramach bieżącej subskrypcji. Użyjesz magazynu platformy Azure zarówno dla zawartości nieprzetworzonej do zaimportowania, jak i dla magazynu wiedzy, który jest wynikiem końcowym.

   Wybierz typ konta **StorageV2 (ogólnego przeznaczenia V2).**

1. Otwórz strony usług obiektów Blob i utwórz kontener o nazwie *hotel-reviews*.

1. Kliknij pozycję **Przekaż**.

    ![Przesyłanie danych](media/knowledge-store-create-portal/upload-command-bar.png "Prześlij opinie o hotelu")

1. Wybierz plik **HotelReviews-Free.csv** pobrany w pierwszym kroku.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

1. Prawie skończyłeś pracę z tym zasobem, ale przed opuszczeniem tych stron użyj łącza w lewym okienku nawigacji, aby otworzyć stronę **Klucze dostępu.** Pobierz ciąg połączenia, aby pobrać dane z magazynu obiektów Blob. Parametry połączenia wyglądają podobnie do następującego przykładu:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Nadal w portalu przełącz się na usługę Azure Cognitive Search. [Utwórz nową usługę](search-create-service-portal.md) lub [znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz skorzystać z bezpłatnej usługi dla tego ćwiczenia.

## <a name="configure-postman"></a>Konfigurowanie programu Postman

Zainstaluj i skonfiguruj listonosza.

### <a name="download-and-install-postman"></a>Pobieranie i instalowanie listonosza

1. Pobierz [kod źródłowy kolekcji Listonosz](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Wybierz **polecenie Import pliku,** > **Import** aby zaimportować kod źródłowy do listonosza.
1. Wybierz kartę **Kolekcje,** a następnie wybierz przycisk **...** (wielokropek).
1. Wybierz pozycję **Edit** (Edytuj). 
   
   ![Aplikacja Postman pokazująca nawigację](media/knowledge-store-create-rest/postman-edit-menu.png "Przejdź do menu Edycja w postmanie")
1. W oknie dialogowym **Edytowanie** wybierz kartę **Zmienne.** 

Na karcie **Zmienne** można dodawać wartości, które postman zamienia za każdym razem, gdy napotka określoną zmienną wewnątrz podwójnych nawiasów klamrowych. Na przykład listonosz zastępuje `{{admin-key}}` symbol bieżącą wartością `admin-key`ustawioną dla . Listonosz dokonuje podstawiania w adresach URL, nagłówkach, treści żądania i tak dalej. 

Aby uzyskać wartość `admin-key`dla , przejdź do usługi Azure Cognitive `search-service-name` Search `storage-account-name` i wybierz kartę **Klucze.** Zmień i do wartości wybranych w [obszarze Tworzenie usług](#create-services-and-load-data). Ustaw `storage-connection-string` przy użyciu wartości na karcie **Klucze dostępu** konta magazynu. Można pozostawić wartości domyślne dla innych wartości.

![Karta Zmienne aplikacji postmana](media/knowledge-store-create-rest/postman-variables-window.png "Okno zmiennych listonosza")


| Zmienna    | Skąd uzyskać |
|-------------|-----------------|
| `admin-key` | Na stronie **Klucze** usługi Azure Cognitive Search.  |
| `api-version` | Zostaw jako **2019-05-06-Preview**. |
| `datasource-name` | Zostaw jako **hotel-opinie-ds**. | 
| `indexer-name` | Zostaw jako **hotel-opinie-ixr**. | 
| `index-name` | Zostaw jako **hotel-opinie-ix**. | 
| `search-service-name` | Nazwa usługi Azure Cognitive Search. Adres URL `https://{{search-service-name}}.search.windows.net`to . | 
| `skillset-name` | Zostaw jako **hotel-opinie-ss**. | 
| `storage-account-name` | Nazwa konta magazynu. | 
| `storage-connection-string` | Na koncie magazynu na karcie **Klucze dostępu** wybierz**ciąg połączenia** **key1** > . | 
| `storage-container-name` | Zostaw jako **hotel-opinie**. | 

### <a name="review-the-request-collection-in-postman"></a>Przejrzyj kolekcję żądań w postman

Podczas tworzenia magazynu wiedzy należy wystawić cztery żądania HTTP: 

- **Put żądanie utworzenia indeksu:** Ten indeks przechowuje dane, które usługi Azure Cognitive Search używa i zwraca.
- **Żądanie POST, aby utworzyć źródło danych:** To źródło danych łączy zachowanie usługi Azure Cognitive Search z kontem magazynu danych i magazynu wiedzy. 
- **Put żądanie utworzenia skillset**: Zestaw umiejętności określa wzbogacenia, które są stosowane do danych i struktury magazynu wiedzy.
- **Put żądanie utworzenia indeksatora:** Uruchamianie indeksatora odczytuje dane, stosuje zestaw umiejętności i przechowuje wyniki. Musisz uruchomić to żądanie jako ostatnie.

[Kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) zawiera kolekcję Listonosz, która ma cztery żądania. Aby wystawić żądania, w obszarze Listonosz wybierz kartę żądania. Następnie dodaj `api-key` `Content-Type` i poproś o nagłówki. Ustaw wartość `api-key` na `{{admin-key}}`. Ustaw wartość `Content-type` `application/json`na . 

![Zrzut ekranu przedstawiający interfejs listonosza dla nagłówków](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Musisz ustawić `api-key` `Content-type` i nagłówki we wszystkich żądaniach. Jeśli postman rozpozna zmienną, zmienna pojawia się `{{admin-key}}` w pomarańczowym tekście, tak jak w poprzednim zrzucie ekranu. Jeśli zmienna jest błędnie, pojawia się w czerwonym tekście.
>

## <a name="create-an-azure-cognitive-search-index"></a>Tworzenie indeksu usługi Azure Cognitive Search

Utwórz indeks usługi Azure Cognitive Search, aby reprezentować dane, które cię interesują w wyszukiwaniu, filtrowaniu i stosowaniu ulepszeń. Utwórz indeks, wystawiając żądanie `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`PUT na . Listonosz zastępuje symbole, które są ujęte `{{search-service-name}}` `{{index-name}}`w `{{api-version}}`podwójne nawiasy klamrowe (takie jak , i ) wartościami ustawionymi w [polu Konfiguruj listonosza](#configure-postman). Jeśli do wydawania poleceń REST używasz innego narzędzia, należy samodzielnie zastąpić te zmienne.

Ustaw strukturę indeksu usługi Azure Cognitive Search w treści żądania. W obszarze Listonosz `api-key` po `Content-type` ustawieniu nagłówków i przejdź do okienka **Treść** żądania. Powinien zostać wyświetlony następujący JSON. Jeśli nie, wybierz **Raw** > **JSON (application/json),** a następnie wklej następujący kod jako treść:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Ta definicja indeksu to kombinacja danych, które chcesz przedstawić użytkownikowi (nazwa hotelu, zawartość recenzji, data), metadane wyszukiwania i dane ulepszeń AI (tonacji, frazy i język).

Wybierz **pozycję Wyślij,** aby wystawić żądanie PUT. Powinien zostać wyświetlony `201 - Created`stan . Jeśli widzisz inny stan, w **body** okienka, poszukaj odpowiedzi JSON, która zawiera komunikat o błędzie. 

## <a name="create-the-datasource"></a>Tworzenie źródła danych

Następnie połącz usługę Azure Cognitive Search z danymi hoteli przechowywanymi w magazynie obiektów Blob. Aby utworzyć źródło danych, wyślij `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`żądanie POST do pliku . Należy ustawić `api-key` nagłówki `Content-Type` i, jak wspomniano wcześniej. 

W postman, przejdź do **żądania Utwórz źródło danych,** a następnie do **body** okienka. Powinien zostać wyświetlony następujący kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Wybierz **pozycję Wyślij,** aby wystawić żądanie POST. 

## <a name="create-the-skillset"></a>Tworzenie zestawu umiejętności 

Następnym krokiem jest określenie zestawu umiejętności, który określa zarówno ulepszenia, które mają być zastosowane, jak i magazyn wiedzy, w którym będą przechowywane wyniki. W obszarze Listonosz wybierz kartę **Utwórz zestaw umiejętności.** To żądanie wysyła `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`PUT do . Ustaw `api-key` nagłówki `Content-type` i tak jak wcześniej. 

Istnieją dwa duże obiekty najwyższego poziomu: `skills` i `knowledgeStore`. Każdy obiekt `skills` wewnątrz obiektu jest usługą wzbogacania. Każda usługa wzbogacania ma `inputs` i `outputs`. Ma `LanguageDetectionSkill` wyjście `targetName` `Language`. Wartość tego węzła jest używana przez większość innych umiejętności jako dane wejściowe. Źródłem `document/Language`jest . Możliwość użycia danych wyjściowych jednego węzła jako danych wejściowych `ShaperSkill`do innego jest jeszcze bardziej widoczna w programie , który określa sposób przepływu danych do tabel magazynu wiedzy.

Obiekt `knowledge_store` łączy się z kontem `{{storage-connection-string}}` magazynu za pośrednictwem zmiennej Listonosz. `knowledge_store`zawiera zestaw mapowań między rozszerzonym dokumentem a tabelami i kolumnami w magazynie wiedzy. 

Aby wygenerować zestaw umiejętności, wybierz przycisk **Wyślij** w postmanie, aby umieścić żądanie:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Tworzenie indeksatora

Ostatnim krokiem jest utworzenie indeksatora. Indeksator odczytuje dane i aktywuje zestaw umiejętności. W obszarze Listonosz wybierz żądanie **Utwórz indeksator,** a następnie przejrzyj treść. Definicja indeksatora odnosi się do kilku innych zasobów, które zostały już utworzone: źródło danych, indeks i zestaw umiejętności. 

Obiekt `parameters/configuration` kontroluje sposób pozyskiwania danych przez indeksatora. W takim przypadku dane wejściowe są w jednym dokumencie, który ma wiersz nagłówka i wartości oddzielone przecinkami. Klucz dokumentu jest unikatowym identyfikatorem dokumentu. Przed kodowaniem klucz dokumentu jest adresem URL dokumentu źródłowego. Na koniec wartości wyjściowe zestaw umiejętności, takie jak kod języka, tonacji i fraz kluczowych, są mapowane do ich lokalizacji w dokumencie. Chociaż istnieje jedna wartość `Language`dla `Sentiment` , jest stosowana do każdego `pages`elementu w tablicy . `Keyphrases`jest tablicą, która jest również stosowana `pages` do każdego elementu w tablicy.

Po ustawieniu `api-key` `Content-type` nagłówków i potwierdzenia, że treść żądania jest podobna do następującego kodu źródłowego, wybierz **pozycję Wyślij** w postmanie. Listonosz wysyła żądanie `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`PUT do . Usługa Azure Cognitive Search tworzy i uruchamia indeksatora. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Uruchamianie indeksatora 

W witrynie Azure portal przejdź do strony **Przegląd** usługi Azure Cognitive Search. Wybierz kartę **Indeksatory,** a następnie wybierz **pozycję hotels-reviews-ixr**. Jeśli indeksator nie został jeszcze uruchomiony, wybierz pozycję **Uruchom**. Zadanie indeksowania może podnieść niektóre ostrzeżenia związane z rozpoznawaniem języka. Dane zawierają niektóre opinie, które są napisane w językach, które nie są jeszcze obsługiwane przez umiejętności poznawcze. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dane zostały wzbogacone przy użyciu usług Cognitive Services i rzutowane wyniki do magazynu wiedzy, można użyć Eksploratora magazynu lub usługi Power BI do eksplorowania wzbogaconego zestawu danych.

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy za pomocą Eksploratora magazynu, zobacz ten przewodnik:

> [!div class="nextstepaction"]
> [Wyświetlanie przy użyciu Eksploratora usługi Storage](knowledge-store-view-storage-explorer.md)

Aby dowiedzieć się, jak połączyć ten magazyn wiedzy z programem Power BI, zobacz ten przewodnik:

> [!div class="nextstepaction"]
> [Łączenie z usługą Power BI](knowledge-store-connect-power-bi.md)

Jeśli chcesz powtórzyć to ćwiczenie lub wypróbować inny przewodnik wzbogacania AI, usuń **indeksator hotel-reviews-idxr.** Usunięcie indeksatora resetuje licznik transakcji dziennego do zera.
