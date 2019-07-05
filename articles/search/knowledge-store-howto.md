---
title: Sposób, aby rozpocząć korzystanie z wiedzy, wyszukiwania (wersja zapoznawcza) — usługa Azure Search
description: Poznaj procedurę wysyłania wzbogaconego dokumentów utworzonych przez sztuczną Inteligencję indeksowania potoki w usłudze Azure Search w magazynie wiedzy na Twoim koncie usługi Azure storage. Z tego miejsca można wyświetlić, zmienić kształt i używanie wzbogaconego dokumentów w usłudze Azure Search i w innych aplikacjach.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: e50dfcdc5ac2fbe2435066546a340874e1b8f682
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551068"
---
# <a name="how-to-get-started-with-knowledge-mining-in-azure-search"></a>Jak rozpocząć pracę z wiedzy wyszukiwania w usłudze Azure Search

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Brak obsługi zestawu SDK platformy .NET w tej chwili.
>
[Magazyn wiedzy](knowledge-store-concept-intro.md) zapisuje wzbogacony sztucznej Inteligencji dokumenty tworzone podczas indeksowania do konta usługi Azure storage dla wyszukiwania wiedzy podrzędne w innych aplikacjach. Umożliwia także zapisane wzbogacenia do zrozumienia i uściślić potoku indeksowania usługi Azure Search. 

Magazyn wiedzy jest definiowany przez *zestawu umiejętności* i tworzone przez *indeksatora*. Wyrażenie fizyczny magazynu wiedzy jest określony za pomocą *projekcje* określające struktury danych w magazynie. Do czasu zakończenia tego przewodnika zostanie utworzony wszystkie te obiekty, i będziesz wiedzieć, jak one współdziałają ze sobą. 

W tym ćwiczeniu Uruchom z przykładowych danych, usługami i narzędziami, aby dowiedzieć się, podstawowy przepływ pracy dotyczące tworzenia i używania pierwszy sklepu wiedzy, z naciskiem na definicję zestawu umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

Magazyn wiedzy jest w środku wielu usług za pomocą usługi Azure Blob storage i Azure Table storage, zapewniając magazynu fizycznego i usługi Azure Search i Cognitive Services do tworzenia obiektów i aktualizacje. Znajomość [Podstawowa architektura](knowledge-store-concept-intro.md) to warunek wstępny do tego przewodnika.

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

+ [Pobierz aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/), który jest używany do wysyłania żądań HTTP do usługi Azure Search.

+ [Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych i wiedzy przechowywania. Sklepu wiedzy będzie istnieć w usłudze Azure storage.

+ [Tworzenie zasobu usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) w warstwie zgodnie z rzeczywistym użyciem S0 broad-spectrum dostęp do pełnego zakresu umiejętności używane w wzbogacenia sztucznej Inteligencji. Usługi cognitive Services i usługi Azure Search są wymagane w tym samym regionie.

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku. 

Przykładowe dokumenty JSON i plik kolekcji narzędzia Postman wymagane są również. Instrukcje do lokalizowania i ładowania dodatkowych plików znajduje się w [przygotować dane przykładowe](#prepare-sample-data) sekcji.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

    ![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-get-started-postman/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Nazwa usługi i klucza API-key udostępni do wszystkich żądań HTTP w poniższych sekcjach.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

Magazyn wiedzy zawiera dane wyjściowe wzbogacony potok. Dane wejściowe składają się z "" danymi, które ostatecznie staje się "użyteczne" miarę za pośrednictwem potoku. Przykłady danymi może zawierać pliki obrazów, które muszą zostać przeanalizowane pod kątem tekst lub właściwości obrazu lub pliki tekstowe gęstą, które mogą być analizowane dla jednostki, kluczowe frazy lub wskaźniki nastrojów klientów. 

W tym ćwiczeniu użyto pliki tekstowe gęstą (informacje o przypadku prawo), które pochodzą z [projektu programu Access orzecznictwa](https://case.law/bulk/download/) publiczne dane zbiorcze strony pobierania. Przykładowy dokument 10 przekazany do usługi GitHub na potrzeby tego ćwiczenia. 

W tym zadaniu utworzysz kontener obiektów Blob platformy Azure dla tych dokumentów do użycia jako dane wejściowe do potoku. 

1. Pobierz i Wyodrębnij [usługi Azure Search przykładowych danych](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) repozytorium, aby uzyskać [zestawu danych orzecznictwa](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawiera przykładowe dane: 

   1. Nazwa kontenera `caselaw-test`. 
   
   1. Ustaw poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "przekazywania na pasku poleceń")

1. Przejdź do folderu zawierającego **sample.json orzecznictwa** przykładowy plik. Wybierz plik, a następnie kliknij przycisk **przekazywanie**.

1. Gdy jesteś w usłudze Azure storage, należy uzyskać nazwę ciągu i kontener połączenia.  Będą potrzebne następujące ciągi w [Utwórz źródło danych](#create-data-source):

   1. Na stronie Przegląd kliknij **klucze dostępu** i skopiuj *parametry połączenia*. Zaczyna się od `DefaultEndpointsProtocol=https;` i nie zawiera z `EndpointSuffix=core.windows.net`. Nazwa konta i klucz są między. 

   1. Nazwa kontenera powinien być `caselaw-test` lub przypisać dowolną nazwę.



## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Postman to aplikacja kliencka, które będą używane do wysyłania żądań i dokumentów JSON do usługi Azure Search. Kilka żądań można formułować, za pomocą tylko informacje w tym artykule. Jednak zawierać dwa z największych żądania (Tworzenie indeksu, tworzenie zestawu umiejętności) informacji pełnej JSON, które są zbyt duże, aby osadzić w artykule. 

Aby udostępnić wszystkie dokumenty JSON i żądań w pełni, utworzyliśmy plik kolekcji narzędzia Postman. Pobieranie, a następnie zaimportować ten plik jest pierwsze zadanie w konfigurowaniu klienta.

1. Pobierz i Rozpakuj [przykładów usługi Azure Search Postman](https://github.com/Azure-Samples/azure-search-postman-samples) repozytorium.

1. Uruchom narzędzie Postman i importowanie kolekcji Postman orzecznictwa:

   1. Kliknij przycisk **importu** > **importować pliki** > **wybierz pliki**. 

   1. Przejdź do folderu \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Select **Caselaw.postman_collection_v2.json**. Powinny zostać wyświetlone cztery **WPIS** żądań w kolekcji.

   ![Kolekcji postman na potrzeby pokazu orzecznictwa](media/knowledge-store-howto/postman-collection.png "kolekcji Postman na potrzeby pokazu orzecznictwa")
   

## <a name="create-an-index"></a>Tworzenie indeksu
    
Pierwsze żądanie używa [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-data-source), tworzenie indeksu usługi Azure Search, który przechowuje wszystkie dane z możliwością wyszukiwania. Indeks określa wszystkie pola, parametry i atrybutów.

Nie muszą mieć indeksu dla wyszukiwania wiedzy, ale indeksator nie będzie działać, chyba że podano indeksu. 

1. W adresie URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`, Zastąp `YOUR-AZURE-SEARCH-SERVICE-NAME` przy użyciu nazwy usługi wyszukiwania. 

1. W sekcji nagłówka Zastąp `<YOUR AZURE SEARCH ADMIN API-KEY>` przy użyciu klucza administratora interfejsu API usługi Azure Search.

1. W sekcji Body dokumentu JSON jest schematu indeksu. Zwinięte widoczności, zewnętrzne powłoki indeksu składa się z następujących elementów. Kolekcja pól odnosi się do pola w zestawie danych orzecznictwa.

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

1. Rozwiń `fields` kolekcji. Zawiera on zbiorczego definicji indeksu, składające się z prostego pola [pól złożonych](search-howto-complex-data-types.md) przy użyciu zagnieżdżonych podstruktury i kolekcje.

   Poświęć chwilę, aby zapoznać się z definicji pola `casebody` złożonych pola w wierszach 302 384. Zwróć uwagę, czy złożonych pole może zawierać innych pól złożonych hierarchiczne oświadczenia są potrzebne. Mogą być modelowane struktury hierarchiczne do indeksu, jak pokazano poniżej, a także jako projekcji zestawu umiejętności, co powoduje utworzenie zagnieżdżonej struktury danych w magazynie wiedzy.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

1. Kliknij przycisk **wysyłania** można wykonać żądania.  Powinna pojawić się **stanu: 201 utworzono** wiadomości jako odpowiedzi.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Tworzenie źródła danych

Drugie żądanie używa [danych źródłowych interfejsu API tworzenia](https://docs.microsoft.com/rest/api/searchservice/create-data-source) nawiązać połączenia z usługą Azure Blob storage. 

1. W adresie URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`, Zastąp `YOUR-AZURE-SEARCH-SERVICE-NAME` przy użyciu nazwy usługi wyszukiwania. 

1. W sekcji nagłówka Zastąp `<YOUR AZURE SEARCH ADMIN API-KEY>` przy użyciu klucza administratora interfejsu API usługi Azure Search.

1. W sekcji Body dokumentu JSON zawiera nazwę konta magazynu połączenia ciągu i obiektów blob kontenera. Parametry połączenia można znaleźć w witrynie Azure portal w ramach konta magazynu **klucze dostępu**. 

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

1. Kliknij przycisk **wysyłania** można wykonać żądania.  Powinna pojawić się **stanu: 201 utworzono** wiadomości jako odpowiedzi.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Tworzenie zestawu umiejętności i wiedzy magazynu

Trzecie żądanie używa [interfejsu API zestawu umiejętności tworzenia](https://docs.microsoft.com/rest/api/searchservice/create-skillset), tworzenie obiektu usługi Azure Search, który określa, jakie umiejętności poznawcze, aby wywołać, jak połączyć ze sobą, a co najważniejsze umiejętności w ramach tego przewodnika — jak określić magazyn wiedzy.

1. W adresie URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`, Zastąp `YOUR-AZURE-SEARCH-SERVICE-NAME` przy użyciu nazwy usługi wyszukiwania. 

1. W sekcji nagłówka Zastąp `<YOUR AZURE SEARCH ADMIN API-KEY>` przy użyciu klucza administratora interfejsu API usługi Azure Search.

1. W sekcji Body dokumentu JSON jest definicja zestawu umiejętności. Zwinięty widoczności, zewnętrzne powłoki zestawu umiejętności składa się z następujących elementów. `skills` Kolekcji definiuje wzbogacenia w pamięci, ale `knowledgeStore` definicja określa sposób przechowywania danych wyjściowych. `cognitiveServices` Definicja jest połączeniem aparatów wzbogacania sztucznej Inteligencji.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Rozwiń `cognitiveServices` i `knowledgeStore` tak, aby można było zapewnić informacje o połączeniu. W tym przykładzie te ciągi znajdują się po definicji zestawu umiejętności, pod koniec treści żądania. 

   Aby uzyskać `cognitiveServices`, aprowizowanie zasobów w warstwie S0 znajduje się w tym samym regionie co usługa Azure Search. Z tej samej stronie w witrynie Azure portal, możesz uzyskać cognitiveServices nazwy i klucza. 
   
   Aby uzyskać `knowledgeStore`, można użyć tego samego parametry połączenia używane dla kontenera obiektów Blob orzecznictwa.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Rozwiń umiejętności kolekcję, w szczególności umiejętności Shaper w wierszach 85 i 179, odpowiednio. Umiejętności Shaper jest ważne, ponieważ jego składa struktur danych, dla wyszukiwania wiedzy. Podczas wykonywania zestawu umiejętności struktury te są tylko w pamięci, ale w przypadku przejścia do następnego kroku, zobaczysz, jak te dane wyjściowe można zapisywać magazynu wiedzy w zakresie dalszych badań.

   Poniższy fragment jest z wiersza 217. 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

1. Rozwiń `projections` element `knowledgeStore`rozpoczynają się od wiersza 262. Projekcje Określ kompozycji magazynu wiedzy. Prognozy są określone w pary obiektów tabel, ale obecnie tylko jeden w czasie. Jak widać w pierwszym projekcji `tables` jest określony, ale `objects` nie jest. W drugim jest przeciwieństwem.

   W usłudze Azure storage zostaną utworzone tabele w usłudze Table storage dla każdej tabeli, którą tworzysz, a każdy obiekt pobiera kontener w usłudze Blob storage.

   Obiekty BLOB są zwykle zawierają pełnego wyrażenia wzbogacenia. Tabele zawierają zwykle wzbogacenia częściowe, w przypadku kombinacji, które zostały rozmieszczone do określonych celów. W tym przykładzie tabele przypadków i opinie, ale nie wyświetlane są inne tabele, takich jak jednostki, pracownicy, jury i stron.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

1. Kliknij przycisk **wysyłania** można wykonać żądania. Odpowiedź powinna wyglądać **201** i wyglądać podobnie do poniższego przykładu, przedstawiający pierwszą część odpowiedzi.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Tworzenie i uruchamianie indeksatora

Czwarty żądanie używa [Tworzenie interfejsu API indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer), tworząc indeksator usługi Azure Search. Indeksator jest aparatem wykonywania potoku indeksowania. Wszystkie definicje, które do tej pory utworzono są umieszczane w ruchu w ramach tego kroku.

1. W adresie URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`, Zastąp `YOUR-AZURE-SEARCH-SERVICE-NAME` przy użyciu nazwy usługi wyszukiwania. 

1. W sekcji nagłówka Zastąp `<YOUR AZURE SEARCH ADMIN API-KEY>` przy użyciu klucza administratora interfejsu API usługi Azure Search.

1. W sekcji Body dokumentu JSON Określa nazwę indeksatora. Źródło danych i indeksu są wymagane przez indeksator. Zestawu umiejętności jest opcjonalny dla indeksatora, ale wymagany wzbogacania sztucznej Inteligencji.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    ```

1. Rozwiń outputFieldMappings. W przeciwieństwie do fieldMappings, które są używane niestandardowe mapowania między polami w źródle danych i pól w indeksie, outputFieldMappings są używane do mapowania pól wzbogacony, tworzone i wypełniane przez potok w danych wyjściowych pól w indeksie ani w projekcji.

    ```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
            "targetFieldName": "people",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
            "targetFieldName": "orginizations",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
            "targetFieldName": "locations",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
            "targetFieldName": "entities",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases",
            "mappingFunction": null
        }
    ]
    ```

1. Kliknij przycisk **wysyłania** można wykonać żądania. Odpowiedź powinna wyglądać **201** i treść odpowiedzi powinna wyglądać prawie identyczne ładunek żądania podane (spacje dla zwięzłości).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    }
    ```

## <a name="explore-knowledge-store"></a>Zapoznaj się z wiedzy magazynu

Możesz rozpocząć eksplorowanie tak szybko, jak pierwszy dokument jest importowany. W tym celu należy użyć [ **Eksploratora usługi Storage** ](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) w portalu.

Należy weź pod uwagę, że magazynu wiedzy jest całkowicie odłączony od usługi Azure Search. Indeks usługi Azure Search i wiedzy magazynu oba zawierają reprezentacji danych i zawartość, ale część sposobów, w tym miejscu. Użyć indeksu dla wyszukiwania pełnotekstowego, wyszukiwanie filtrowane i wszystkie scenariusze, które są obsługiwane w usłudze Azure Search. Lub przejściem do kolejnych po prostu wiedzy sklepu, dołączając innych narzędzi do analizowania zawartości.

## <a name="takeaways"></a>Wnioski

Utworzeniu teraz pierwszy sklepu wiedzę w usłudze Azure storage i umożliwia wyświetlanie wzbogacenia Eksploratora usługi Storage. Jest to podstawowe środowisko do pracy z wzbogacenia przechowywanych. 

## <a name="next-steps"></a>Kolejne kroki

Umiejętności Shaper wykonuje skomplikowanymi na tworzeniu formularzy szczegółowe dane, które mogą być połączone w nowe kształty. Kolejnym krokiem Przejrzyj stronę odwołanie to umiejętności, aby uzyskać szczegółowe informacje na temat sposobu ich wykorzystywania.

> [!div class="nextstepaction"]
> [Odwołanie do shaper umiejętności](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
