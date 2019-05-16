---
title: Jak rozpocząć pracę z usługą wiedzy store (wersja zapoznawcza) — usługa Azure Search
description: Poznaj procedurę wysyłania wzbogaconego dokumentów utworzonych przez sztuczną Inteligencję indeksowania potoki w usłudze Azure Search w magazynie wiedzy na Twoim koncie usługi Azure storage. Z tego miejsca można wyświetlić, zmienić kształt i używanie wzbogaconego dokumentów w usłudze Azure Search i w innych aplikacjach.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.openlocfilehash: e7be2dfc811caa087726339846a1de2516f1e2b2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540724"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Jak rozpocząć pracę z magazynem wiedzę w usłudze Azure Search

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Brak obsługi zestawu SDK platformy .NET w tej chwili.
>

[Magazyn wiedzy](knowledge-store-concept-intro.md) zapisuje wzbogacenia sztucznej Inteligencji, utworzony podczas indeksowania do konta usługi Azure storage dla wyszukiwania wiedzy podrzędne w innych aplikacjach. Umożliwia także zapisane wzbogacenia do zrozumienia i uściślić potoku indeksowania usługi Azure Search.

Magazyn wiedzy jest definiowany przez zestawu umiejętności. W przypadku regularnego scenariuszy usługi Azure Search wyszukiwanie pełnotekstowe celem zestawu umiejętności jest zapewnienie wzbogacenia sztucznej Inteligencji, aby wprowadzić więcej wyszukiwanie zawartości. W przypadku scenariuszy wyszukiwania wiedzy roli zestawu umiejętności jest tworzenie, wypełnianie i przechowywanie wielu struktur danych do analizy lub modelowania w innych aplikacjach i procesach.

W tym ćwiczeniu Uruchom z przykładowych danych, usługami i narzędziami, aby dowiedzieć się, podstawowy przepływ pracy dotyczące tworzenia i używania pierwszy sklepu wiedzy, z naciskiem na definicję zestawu umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

Następujących usług, narzędzi i danych są używane w tym przewodniku Szybki Start. 

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku. 

+ [Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych. Sklepu wiedzy będzie istnieć w usłudze Azure storage. 

+ [Tworzenie zasobu usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) w warstwie zgodnie z rzeczywistym użyciem S0 broad-spectrum dostęp do pełnego zakresu umiejętności używane w wzbogacenia sztucznej Inteligencji. Ten zasób i usługi Azure Search są wymagane w tym samym regionie.

+ [Aplikacja klasyczna narzędzia postman](https://www.getpostman.com/) wysyłania żądań do usługi Azure Search.

+ [Kolekcja postman Collection](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Caselaw) z żądaniami przygotowany do tworzenia źródła danych, indeksu, zestawu umiejętności i indeksatora. Kilka definicji obiektu jest zbyt długa, aby uwzględnić w tym artykule. Należy uzyskać tej kolekcji, aby zobaczyć definicji indeksu i zestawu umiejętności w całości.

+ [Orzecznictwa przykładowych danych](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) pochodzące z [projektu programu Access orzecznictwa](https://case.law/bulk/download/) stronę pobierania danych zbiorczego publicznej. W szczególności wykonywania używa 10 pierwszych dokumenty pierwszego pobieranego (Arkansas). Przykładowy dokument 10 przekazany do usługi GitHub na potrzeby tego ćwiczenia.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

    ![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi.

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawiera przykładowe dane. Użyj kontenera nazwy "orzecznictwa test". Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "przekazywania na pasku poleceń")

1. Przejdź do folderu zawierającego **sample.json orzecznictwa** przykładowy plik. Wybierz plik, a następnie kliknij przycisk **przekazywanie**.


## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Uruchom narzędzie Postman i importowanie kolekcji Postman orzecznictwa. Alternatywnie skonfiguruj serii żądań HTTP. Jeśli nie jesteś zaznajomiony z tego narzędzia, zobacz [Eksplorowanie usługi Azure Search interfejsów API REST przy użyciu narzędzia Postman](search-fiddler.md).

+ Metoda żądania dla każdego wywołania w tym instruktażu jest **umieścić** lub **WPIS**.
+ Następujące nagłówki żądania (2): "Content-type" wartość "application/json", "api-key" wartość "admin key" (klucz administratora to symbol zastępczy podstawowego klucza wyszukiwania) odpowiednio. 
+ Treść żądania jest, w którym umieszcza się właściwą zawartość wywołania. 

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/postmanoverview.png)

Używamy narzędzia Postman się cztery wywołań interfejsu API usługi search, tworzenie źródła danych, indeksu, zestawu umiejętności i indeksatora — w tej kolejności. Źródło danych zawiera wskaźnik do swojego konta usługi Azure storage i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas importowania danych.

[Tworzenie zestawu umiejętności](#create-skillset) skupia się w tym przewodniku: Określa on kroki wzbogacanie i jak dane są utrwalane w magazynie wiedzy.

Końcowy adres URL należy określić wersji interfejsu api oraz dla każdego wywołania powinna zwrócić **201 utworzono**. Wersja interfejsu api (wersja zapoznawcza) do tworzenia zestawu umiejętności dzięki obsłudze magazynu knowledge to `2019-05-06-Preview` (z uwzględnieniem wielkości liter).

Wykonaj następujące wywołania interfejsu API z klienta REST.

## <a name="create-a-data-source"></a>Tworzenie źródła danych

[Danych źródłowych interfejsu API tworzenia](https://docs.microsoft.com/rest/api/searchservice/create-data-source) tworzy obiekt usługi Azure Search, który określa, jakie dane mają być indeksowane.

Punkt końcowy tego wywołania jest `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Zastąp element `[service name]` nazwą usługi wyszukiwania. 

2. Dla tego wywołania treść żądania musi zawierać nazwę konta magazynu połączenia ciągu i obiektów blob kontenera. Połączenia można znaleźć w witrynie Azure portal w ramach konta magazynu **klucze dostępu**. 

   Upewnij się zastąpić ciąg i obiektów blob kontenera nazwę połączenia w treści żądania, przed wykonaniem wywołania.

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

3. Wyślij żądanie. Odpowiedź powinna wyglądać **201** i treść odpowiedzi powinna wyglądać prawie identyczne podane ładunku żądania.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Tworzenie indeksu
    
Drugie wywołanie ma [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-data-source), tworzenie indeksu usługi Azure Search, który przechowuje wszystkie dane z możliwością wyszukiwania. Indeks określa wszystkie pola, parametry i atrybutów.

Nie muszą mieć indeksu dla wyszukiwania wiedzy, ale indeksator nie będzie działać, chyba że podano indeksu. 

Adres URL dla tego wywołania jest `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Zastąp element `[service name]` nazwą usługi wyszukiwania.

2. Skopiuj definicję indeksu z żądania tworzenia indeksu w kolekcji Postman do treści żądania. Definicja indeksu jest kilka wierszy kilkuset, zbyt długi, aby wydrukować tutaj. 

   Zewnętrzne powłoki indeksu składa się z następujących elementów. 

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

3. `fields` Kolekcja zawiera zbiorczego definicję indeksu. Zawiera proste pola [pól złożonych](search-howto-complex-data-types.md) przy użyciu zagnieżdżonych podstruktury i kolekcje.

   Przejrzyj definicji pola `casebody` na linii 302 384. Zwróć uwagę, czy złożonych pole może zawierać innych pól złożonych hierarchiczne oświadczenia są potrzebne.

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

4. Wyślij żądanie. 

   Odpowiedź powinna wyglądać **201** i wyglądać podobnie do poniższego przykładu, przedstawiający pierwsze kilka pól:

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
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
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Tworzenie zestawu umiejętności i wiedzy magazynu

[Interfejsu API zestawu umiejętności tworzenia](https://docs.microsoft.com/rest/api/searchservice/create-skillset) tworzy obiekt usługi Azure Search, który określa, jakie umiejętności poznawcze, aby wywołać, jak połączyć ze sobą, a co najważniejsze umiejętności w ramach tego przewodnika — jak określić magazyn wiedzy.

Punkt końcowy tego wywołania jest `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Zastąp element `[service name]` nazwą usługi wyszukiwania.

2. Skopiuj definicję zestawu umiejętności z żądania tworzenia zestawu umiejętności w kolekcji Postman do treści żądania. Definicja zestawu umiejętności jest kilka wierszy kilkuset, zbyt długi, aby wydrukować tutaj, ale jest fokus w tym przewodniku.

   Zewnętrzne powłoki zestawu umiejętności składa się z następujących elementów. `skills` Kolekcji definiuje wzbogacenia w pamięci, ale `knowledgeStore` definicja określa sposób przechowywania danych wyjściowych. `cognitiveServices` Definicja jest połączeniem aparatów wzbogacania sztucznej Inteligencji.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Najpierw ustaw `cognitiveServices` i `knowledgeStore` klucza i parametry połączenia. W tym przykładzie te ciągi znajdują się po definicji zestawu umiejętności, pod koniec treści żądania. Użyj zasobu usług Cognitive Services, aprowizowane w warstwie S0 znajduje się w tym samym regionie co usługa Azure Search.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

3. Przejrzyj umiejętności kolekcję, w szczególności umiejętności Shaper w wierszach 85 i 170, odpowiednio. Umiejętności Shaper jest ważne, ponieważ jego składa struktur danych, dla wyszukiwania wiedzy. Podczas wykonywania zestawu umiejętności struktury te są tylko w pamięci, ale w przypadku przejścia do następnego kroku, zobaczysz, jak te dane wyjściowe można zapisywać magazynu wiedzy w zakresie dalszych badań.

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

3. Przegląd `projections` element `knowledgeStore`rozpoczynają się od wiersza 262. Projekcje Określ kompozycji magazynu wiedzy. Prognozy są określone w pary obiektów tabel, ale obecnie tylko jeden w czasie. Jak widać w pierwszym projekcji `tables` jest określony, ale `objects` nie jest. W drugim jest przeciwieństwem.

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

5. Wyślij żądanie. Odpowiedź powinna wyglądać **201** i wyglądać podobnie do poniższego przykładu, przedstawiający pierwszą część odpowiedzi.

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

[Tworzenie interfejsu API indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) tworzy i uruchamia natychmiast indeksatora. Wszystkie definicje, które do tej pory utworzono są umieszczane w ruchu w ramach tego kroku. Indeksator uruchamia natychmiast, ponieważ nie istnieje w usłudze. Istnieje, wywołanie metody POST, do istniejącego indeksatora po operacji aktualizacji.

Punkt końcowy tego wywołania jest `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Zastąp element `[service name]` nazwą usługi wyszukiwania. 

2. Dla tego wywołania treść żądania określa nazwę indeksatora. Źródło danych i indeksu są wymagane przez indeksator. Zestawu umiejętności jest opcjonalny dla indeksatora, ale wymagany wzbogacania sztucznej Inteligencji.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
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
    }
    ```

3. Wyślij żądanie. Odpowiedź powinna wyglądać **201** i treść odpowiedzi powinna wyglądać prawie identyczne ładunek żądania podane (spacje dla zwięzłości).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
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