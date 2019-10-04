---
title: Tworzenie sklepu z bazami danych przy użyciu Azure Search REST
description: Utwórz Azure Search magazyn wiedzy na potrzeby utrwalania wzbogacania z potoku wyszukiwania poznawczego przy użyciu interfejsu API REST i programu Poster.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: 26dc66474eecffd7f5a34bcfcaf93fd49f59606c
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936498"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Tworzenie Azure Search sklepu z bazami danych przy użyciu usługi REST

Magazyn wiedzy to funkcja w Azure Search, która utrzymuje dane wyjściowe z potoku wzbogacania AI na potrzeby późniejszej analizy lub przetwarzania podrzędnego. Potok wzbogacony AI akceptuje pliki obrazów lub pliki tekstowe bez struktury, indeksuje je przy użyciu Azure Search, stosuje wzbogacenia AI z Cognitive Services (takich jak analiza obrazów i przetwarzanie języka naturalnego), a następnie zapisuje wyniki w sklepie z bazami danych na platformie Azure Chowan. Można następnie użyć narzędzi takich jak Power BI lub Eksplorator usługi Storage do eksplorowania sklepu z bazami danych.

W tym artykule zostanie użyty interfejs API REST do pozyskiwania, indeksowania i stosowania wzbogacania AI do zestawu przeglądów hotelu. Przeglądy hotelu są importowane do usługi Azure Blob Storage, a wyniki są zapisywane jako magazyn wiedzy na platformie Azure Table Storage.

Po utworzeniu sklepu z bazami danych możesz uzyskać informacje na temat uzyskiwania dostępu do tego sklepu merytorycznego za pomocą [Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md) lub [Power BI](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1 — Tworzenie usług

+ [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi.

+ [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych i sklepu z bazami informacji. Twoje konto magazynu musi używać tej samej lokalizacji (na przykład US-zachodni) dla usługi Azure Search. *Rodzaj konta* musi być *StorageV2 (ogólnego przeznaczenia w wersji 2)* (domyślnie) lub *magazynu (ogólnego przeznaczenia w wersji 1)* .

+ Zalecane: [publikowanie aplikacji klasycznej](https://www.getpostman.com/) na potrzeby wysyłania żądań do Azure Search. Za pomocą interfejsu API REST można korzystać z dowolnego narzędzia, które może pracować z żądaniami HTTP i odpowiedziami. Program Poster jest dobrym rozwiązaniem do eksplorowania interfejsów API REST i zostanie użyty w tym artykule. Ponadto [kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) tego artykułu zawiera kolekcję żądań post. 

## <a name="2---store-the-data"></a>2 — Przechowywanie danych

Załaduj plik CSV przeglądów hotelu do magazynu obiektów blob platformy Azure, aby uzyskać do niego dostęp za pomocą indeksatora Azure Search i przechodzącego przez potok wzbogacania AI.

### <a name="create-an-azure-blob-container-with-the-data"></a>Tworzenie kontenera obiektów blob platformy Azure z danymi

1. [Pobierz dane z przeglądu hotelu zapisane w pliku CSV (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Te dane pochodzą z programu Kaggle.com i zawierają opinie klientów dotyczące hoteli.
1. [Zaloguj się do Azure Portal](https://portal.azure.com)i przejdź do konta usługi Azure Storage.
1. [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Aby utworzyć kontener, na lewym pasku nawigacyjnym dla konta magazynu kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener** na pasku poleceń.
1. Dla nowej **nazwy**kontenera wprowadź `hotel-reviews`.
1. Wybierz dowolny **poziom dostępu publicznego**. Użyto domyślnej.
1. Kliknij przycisk **OK** , aby utworzyć kontener obiektów blob platformy Azure.
1. Otwórz nowy kontener `hotels-review`, kliknij przycisk **Przekaż**, a następnie wybierz plik **HotelReviews-Free. csv** , który został pobrany w pierwszym kroku.

    ![Przekazywanie danych](media/knowledge-store-create-portal/upload-command-bar.png "przekazywanie przeglądów hotelu")

1. Kliknij przycisk **Przekaż** , aby ZAIMPORTOWAĆ plik CSV do usługi Azure Blob Storage. Zostanie wyświetlony nowy kontener.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

## <a name="3---configure-postman"></a>3 — Konfigurowanie wpisu

Pobierz [kod źródłowy kolekcji Poster](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) i zaimportuj go do programu do publikowania przy użyciu **pliku, zaimportuj...** . Przejdź do karty **kolekcje** i kliknij przycisk **...** , a następnie wybierz pozycję **Edytuj**. 

![Aplikacja programu Poster pokazująca nawigację](media/knowledge-store-create-rest/postman-edit-menu.png "do menu Edycja w programie Poster")

W oknie dialogowym Edytowanie wyników przejdź do karty **zmienne** . 

Karta **zmienne** umożliwia dodanie wartości, które program Poster będzie zamieniany w każdym momencie napotkanym w podwójnych nawiasach klamrowych. Na przykład, program Poster zamieni symbol `{{admin-key}}` z "bieżącą wartość" `admin-key`. Program pocztowy wprowadzi te podstawie w adresach URL, nagłówkach, treści żądania i tak dalej. 

Wartość `admin-key` znajduje się na karcie **klucze** Search Service. Należy zmienić `search-service-name` i `storage-account-name` na wartości wybrane w [kroku 1](#1---create-services). Ustaw wartość `storage-connection-string` na karcie **klucze dostępu** konta magazynu. Inne wartości, które można pozostawić bez zmian.

![Karta zmienne aplikacji po wprowadzeniu](media/knowledge-store-create-rest/postman-variables-window.png "")


| Zmienna    | Gdzie uzyskać |
|-------------|-----------------|
| `admin-key` | Search Service, karta **klucze**              |
| `api-version` | Pozostaw jako "2019-05-06 — wersja zapoznawcza" |
| `datasource-name` | Pozostaw "przeglądy hotelowe — DS" | 
| `indexer-name` | Pozostaw jako "Hotel-Recenzje-IXR" | 
| `index-name` | Pozostaw jako "Hotel-Recenzje-IX" | 
| `search-service-name` | Search Service, nazwa główna. Adres URL jest `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Pozostaw jako "Hotel-Recenzje-SS" | 
| `storage-account-name` | Konto magazynu, główna nazwa | 
| `storage-connection-string` | Konto magazynu, karta **klucze dostępu** , **Parametry połączenia** Klucz1 | 
| `storage-container-name` | Pozostaw "Recenzje w hotelu" | 

### <a name="review-the-request-collection-in-postman"></a>Przeglądanie kolekcji żądań w programie Poster

Tworzenie sklepu z bazami informacji wymaga, aby wystawić cztery żądania HTTP: 

1. Żądanie PUT, aby utworzyć indeks. Ten indeks zawiera dane używane i zwracane przez Azure Search.
1. Żądanie POST, aby utworzyć źródło danych. To źródło danych łączy zachowanie Azure Search z kontem magazynu danych i magazynu informacji. 
1. Żądanie PUT umożliwiające utworzenie zestawu umiejętności. Zestawu umiejętności określa wzbogacenia zastosowane do danych i strukturę magazynu wiedzy.
1. Żądanie PUT umożliwiające utworzenie indeksatora. Uruchomienie indeksatora odczytuje dane, stosuje zestawu umiejętności i przechowuje wyniki. Należy uruchomić to żądanie jako ostatnie.

[Kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) zawiera kolekcję ogłoszeń obejmującą te cztery żądania. Aby wydać żądania, przejdź do karty żądania w programie Poster i Dodaj nagłówki żądania `api-key` i `Content-Type`. Ustaw wartość `api-key` na `{{admin-key}}`. Ustaw wartość `Content-type` na `application/json`. 

> [!div class="mx-imgBorder"]
> ![Screenshot przedstawiający interfejs autora dla nagłówków @ no__t-1

> [!Note]
> Musisz ustawić nagłówki `api-key` i `Content-type` we wszystkich żądaniach. Jeśli zmienna jest rozpoznawana przez program Poster, będzie ona renderowana w pomarańczowym tekście, jak w przypadku `{{admin-key}}` na zrzucie ekranu. Jeśli zmienna jest błędna, zostanie wyrenderowana w kolorze czerwonym.
>

## <a name="4---create-an-azure-search-index"></a>4 — Tworzenie indeksu Azure Search

Należy utworzyć indeks Azure Search, aby reprezentować dane, na których interesuje Cię wyszukiwanie, filtrowanie i wykonywanie ulepszeń. Indeks można utworzyć, wydając żądanie PUT `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Po zazastąpieniu symboli ujętych w nawiasy klamrowe, na przykład `{{search-service-name}}`, `{{index-name}}` i `{{api-version}}` wartościami określonymi w [kroku 3](#3---configure-postman). Jeśli używasz innego narzędzia do wystawiania poleceń REST, musisz samodzielnie zastąpić te zmienne.

Określ strukturę indeksu Azure Search w treści żądania. Po ustawieniu nagłówków `api-key` i `Content-type` w programie Poster przejdź do okienka **Body** żądania. Powinien zostać wyświetlony następujący plik JSON, ale jeśli nie, wybierz **RAW** i **JSON (Application/JSON)** i wklej następujący kod jako treść:

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

Zobaczysz, że ta definicja indeksu jest kombinacją danych, które chcesz przedstawić użytkownikowi (nazwa hotelu, zawartość, Data itd.), metadanych wyszukiwania i danych ulepszeń AI (tonacji, frazy kluczowe i język).

Naciśnij przycisk **Wyślij** , aby wydać żądanie Put. Powinien zostać wyświetlony komunikat o stanie `201 - Created`. Jeśli zostanie wyświetlony inny stan, w okienku **treść** zostanie wyświetlona odpowiedź JSON z komunikatem o błędzie. 

## <a name="5---create-the-datasource"></a>5 — Tworzenie źródła danych

Teraz musisz połączyć Azure Search z danymi hotelu zapisanymi w [kroku 2](#2---store-the-data). Tworzenie źródła danych jest wykonywane z WPISem do `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Ponownie musisz ustawić `api-key` i `Content-Type` nagłówków zgodnie z podanymi wcześniej. 

W programie Poster Otwórz żądanie "Utwórz źródło danych". Przejdź do okienka **treść** , który powinien mieć następujący kod:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Naciśnij przycisk **Wyślij** , aby wydać żądanie post. 

## <a name="6---create-the-skillset"></a>6 — Tworzenie zestawu umiejętności 

Następnym krokiem jest określenie zestawu umiejętności, który określa ulepszenia do zastosowania oraz magazyn wiedzy, w którym będą przechowywane wyniki. W programie Poster Otwórz kartę "Tworzenie zestawu umiejętności". To żądanie wysyła do `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Ustaw nagłówki `api-key` i `Content-type` tak samo jak wcześniej. 

Istnieją dwa duże obiekty najwyższego poziomu: `"skills"` i `"knowledgeStore"`. Każdy obiekt w obiekcie `"skills"` jest usługą wzbogacania. Każda usługa wzbogacania ma `"inputs"` i `"outputs"`. Zwróć uwagę, jak `LanguageDetectionSkill` zawiera dane wyjściowe `targetName` z `"Language"`. Wartość tego węzła jest używana przez większość innych umiejętności jako dane wejściowe, ze źródłem jako `document/Language`. Ta możliwość używania danych wyjściowych z jednego węzła jako dane wejściowe do innego jest jeszcze bardziej oczywista w `ShaperSkill`, która określa, jak dane będą przepływać do tabel w sklepie z bazami wiedzy.

Obiekt `"knowledge_store"` nawiązuje połączenie z kontem magazynu za pośrednictwem zmiennej post `{{storage-connection-string}}`. Następnie zawiera zestaw mapowań między rozszerzonym dokumentem a tabelami i kolumnami, które będą dostępne w samym sklepie merytorycznym. 

Aby wygenerować zestawu umiejętności, umieść żądanie, naciskając przycisk **Wyślij** w programie Poster.

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

## <a name="7---create-the-indexer"></a>7\. Tworzenie indeksatora

Ostatnim krokiem jest utworzenie indeksatora, który faktycznie odczytuje dane i aktywuje zestawu umiejętności. W programie Poster przejdź do żądania "Utwórz indeksator" i przejrzyj treść. Jak widać, definicja indeksatora odwołuje się do kilku innych już utworzonych zasobów — źródła danych, indeksu i zestawu umiejętności. 

Obiekt `"parameters/configuration"` kontroluje sposób pozyskiwania danych przez indeksator. W takim przypadku dane wejściowe znajdują się w jednym dokumencie z wierszem nagłówka i wartościami rozdzielonymi przecinkami. Klucz dokumentu jest unikatowym identyfikatorem dokumentu, który przed kodowaniem jest adresem URL dokumentu źródłowego. Na koniec wartości wyjściowe zestawu umiejętności, takie jak kod języka, tonacji i kluczowe frazy, są mapowane do odpowiednich lokalizacji w dokumencie. Zwróć uwagę, że gdy istnieje jedna wartość dla `Language`, `Sentiment` jest stosowana do każdego elementu w tablicy `pages`. `Keyphrases` jest sama tablicą i jest również stosowana do każdego elementu w tablicy `pages`.

Po ustawieniu nagłówków `api-key` i `Content-type` i potwierdzeniu, że treść żądania jest podobna do kodu źródłowego, który następuje po naciśnięciu przycisku **Wyślij** w programie Poster. Po przeniesieniu żądania do `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` zostanie wysłane żądanie. Azure Search utworzy i uruchomi indeksator. 

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

## <a name="8---run-the-indexer"></a>8 — uruchamianie indeksatora 

W Azure Portal przejdź do **omówienia** Search Service i wybierz kartę **indeksatory** . kliknij pozycję **Hotele-Recenzje-IXR** utworzone w poprzednim kroku. Jeśli indeksator nie został jeszcze uruchomiony, naciśnij przycisk Run ( **Uruchom** ). Zadanie indeksowania może zgłosić pewne ostrzeżenia dotyczące rozpoznawania języka, ponieważ dane obejmują pewne przeglądy w językach, które nie są jeszcze obsługiwane przez umiejętności poznawcze. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dane zostały wzbogacone za pomocą usług poznawczych, a wyniki są zorganizowane w sklepie z bazami danych, można użyć Eksplorator usługi Storage lub Power BI do eksplorowania wzbogaconego zestawu.

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy przy użyciu Eksplorator usługi Storage, zobacz następujący przewodnik.

> [!div class="nextstepaction"]
> [Wyświetl z Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md)

Aby dowiedzieć się, jak połączyć ten magazyn wiedzy z Power BI, zobacz następujący przewodnik.

> [!div class="nextstepaction"]
> [Łączenie z usługą Power BI](knowledge-store-connect-power-bi.md)

Jeśli chcesz powtórzyć to ćwiczenie lub spróbować użyć innego instruktażu do wzbogacania, Usuń usługę *Hotel-Recenzje-idxr* indeksator. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji z powrotem do zera.
