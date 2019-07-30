---
title: Jak rozpocząć pracę z usługą Knowledge Store (wersja zapoznawcza) — Azure Search
description: Zapoznaj się z instrukcjami dotyczącymi wysyłania ulepszonych dokumentów utworzonych przez potoki indeksowania AI w Azure Search do sklepu z bazami danych na koncie usługi Azure Storage. Z tego miejsca można wyświetlać i przekształcać wzbogacone dokumenty oraz korzystać z nich w Azure Search i innych aplikacjach.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: 5794a24931b613bf1bdddd983799367bb02cf44d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641012"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Jak rozpocząć pracę z usługą Knowledge Store w Azure Search

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>
[Magazyn wiedzy](knowledge-store-concept-intro.md) zapisuje dokumenty wzbogacone AI utworzone podczas indeksowania na koncie usługi Azure Storage na potrzeby wyszukiwania danych podrzędnych w innych aplikacjach. Możesz również użyć zapisanych wzbogaceń, aby zrozumieć i udoskonalić potok Azure Search indeksowania. 

Magazyn wiedzy jest definiowany przez *zestawu umiejętności* i tworzony przez *indeksator*. Wyrażenie fizyczne magazynu informacji jest określane przez projekcje, które określają struktury danych w magazynie. Po zakończeniu tego przewodnika zostaną utworzone wszystkie te obiekty, a ty wiesz, jak wszystkie one pasują do siebie. 

W tym ćwiczeniu Zacznij od przykładowych danych, usług i narzędzi, aby poznać podstawowy przepływ pracy służący do tworzenia i używania pierwszego sklepu z bazami danych, z uwzględnieniem definicji zestawu umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

Magazyn wiedzy znajduje się w centrum z wieloma usługami, usługą Azure Blob Storage i usługą Azure Table Storage dostarczającą magazyn fizyczny oraz Azure Search i Cognitive Services do tworzenia i aktualizowania obiektów. Znajomość [podstawowej architektury](knowledge-store-concept-intro.md) jest wymaganiem wstępnym tego przewodnika.

W tym przewodniku Szybki Start są używane następujące usługi i narzędzia. 

+ [Pobierz aplikację komputerową](https://www.getpostman.com/)programu Poster służącą do wysyłania żądań HTTP do Azure Search.

+ [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych i sklepu z bazami informacji. Twój sklep merytoryczny będzie istnieć w usłudze Azure Storage.

+ [Utwórz zasób Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) w warstwie płatność zgodnie z rzeczywistym użyciem, aby uzyskać szeroki dostęp do pełnego zakresu umiejętności używanych w wzbogacaniu AI. Cognitive Services i usługa Azure Search musi znajdować się w tym samym regionie.

+ [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi. 

Przykładowe dokumenty JSON i plik kolekcji programu Poster są również wymagane. Instrukcje dotyczące lokalizowania i ładowania plików dodatkowych znajdują się w sekcji [przygotowanie przykładowych danych](#prepare-sample-data) .

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

    ![Pobieranie punktu końcowego http i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego http i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Podaj nazwę usługi i klucz API-Key w poszczególnych żądaniach HTTP w poniższych sekcjach.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

Magazyn wiedzy zawiera dane wyjściowe potoku wzbogacania. Dane wejściowe składają się z danych "niezdatnych do użytku", które ostatecznie staną się "użyteczne" w miarę postępu przez potok. Przykłady nieużytecznych danych mogą obejmować pliki obrazów, które muszą być analizowane pod kątem charakterystyki tekstu lub obrazu, lub gęstych plików tekstowych, które mogą być analizowane dla jednostek, kluczowych fraz lub tonacji. 

W tym ćwiczeniu są używane gęste pliki tekstowe (informacje o prawach do wielkości liter) pochodzące z publicznej strony pobierania danych zbiorczych [projektu dostępu Caselaw](https://case.law/bulk/download/) . W ramach tego ćwiczenia przekazanomy przykład 10-dokumentowy do usługi GitHub. 

W tym zadaniu utworzysz kontener obiektów blob platformy Azure, który będzie używany jako dane wejściowe dla potoku. 

1. Pobierz i Wyodrębnij [Azure Search przykładowe repozytorium danych](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) , aby uzyskać [zestaw danych Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Zaloguj się do Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , aby zawierał przykładowe dane: 

   1. Nazwij kontener `caselaw-test`. 
   
   1. Ustaw poziom dostępu publicznego na dowolną z jego prawidłowych wartości.

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "Przekaż na pasku poleceń")

1. Przejdź do folderu zawierającego przykładowy plik **caselaw-Sample. JSON** . Wybierz plik, a następnie kliknij przycisk **Przekaż**.

1. Podczas pracy w usłudze Azure Storage Pobierz parametry połączenia i nazwę kontenera.  Oba te ciągi są potrzebne podczas [tworzenia źródła danych](#create-data-source):

   1. Na stronie Przegląd kliknij pozycję **klucze dostępu** i skopiuj *Parametry połączenia*. Zaczyna `DefaultEndpointsProtocol=https;` się od i zawiera `EndpointSuffix=core.windows.net`. Nazwa konta i klucz znajdują się między. 

   1. Nazwa kontenera musi być lub `caselaw-test` być dowolną nazwą, którą chcesz przypisać.



## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Poster to aplikacja kliencka, która będzie używana do wysyłania żądań i dokumentów JSON do Azure Search. Kilka żądań można sformułować przy użyciu tylko tych informacji w tym artykule. Jednak dwa z największych żądań (Tworzenie indeksu, tworzenie zestawu umiejętności) zawierają pełny kod JSON, który jest zbyt duży do osadzenia w artykule. 

Aby wszystkie dokumenty i żądania JSON były w pełni dostępne, utworzyliśmy plik kolekcji programu Poster. Pobranie, a następnie zaimportowanie tego pliku to pierwsze zadanie w celu skonfigurowania klienta.

1. Pobierz i rozpakuj repozytorium [przykładów programu Azure Search](https://github.com/Azure-Samples/azure-search-postman-samples) .

1. Uruchom Ogłoś i zaimportuj kolekcję Caselaw Poster:

   1. Kliknij kolejno pozycje **Importuj** > **Importuj pliki** > **Wybierz pliki**. 

   1. Przejdź do folderu \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Wybierz **Caselaw. postman_collection_v2. JSON**. W kolekcji powinny być widoczne cztery żądania **post** .

   ![Kolekcja ogłoszeń dla demonstracji Caselaw](media/knowledge-store-howto/postman-collection.png "Kolekcja ogłoszeń dla demonstracji Caselaw")
   

## <a name="create-an-index"></a>Tworzenie indeksu
    
Pierwsze żądanie używa [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-data-source), tworząc indeks Azure Search, w którym są przechowywane wszystkie dane z możliwością wyszukiwania. Indeks określa wszystkie pola, parametry i atrybuty.

Nie musisz być indeksem dla wyszukiwania w bazie wiedzy, ale indeksator nie zostanie uruchomiony, chyba że podano indeks. 

1. W adresie `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`URL Zamień `YOUR-AZURE-SEARCH-SERVICE-NAME` na nazwę usługi wyszukiwania. 

1. W sekcji nagłówek Zamień `<YOUR AZURE SEARCH ADMIN API-KEY>` na klucz interfejsu API administratora dla Azure Search.

1. W sekcji body dokument JSON jest schematem indeksu. W celu zapewnienia widoczności zewnętrzna powłoka indeksu składa się z następujących elementów. Kolekcja Fields odnosi się do pól w zestawie danych caselaw.

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

1. `fields` Rozwiń kolekcję. Zawiera zbiorczą część definicji indeksu składającą się z pól prostych, [złożonych pól](search-howto-complex-data-types.md) z zagnieżdżonymi podstrukturami i kolekcjami.

   Poświęć chwilę na zapoznanie się z definicją `casebody` pola dla złożonego pola w wierszach 302-384. Należy zauważyć, że złożone pole może zawierać inne złożone pola, gdy są zbędne reprezentacje hierarchiczne. Struktury hierarchiczne można modelować w indeksie, jak pokazano tutaj, a także jako projekcja w zestawu umiejętności, tworząc w ten sposób zagnieżdżoną strukturę danych w sklepie merytorycznym.

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

1. Kliknij przycisk **Wyślij** , aby wykonać żądanie.  Powinien zostać wyświetlony **stan: 201 Utworzono** komunikat jako odpowiedź.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Utwórz źródło danych

Drugie żądanie używa [interfejsu API tworzenia źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) do nawiązywania połączenia z usługą Azure Blob Storage. 

1. W adresie `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`URL Zamień `YOUR-AZURE-SEARCH-SERVICE-NAME` na nazwę usługi wyszukiwania. 

1. W sekcji nagłówek Zamień `<YOUR AZURE SEARCH ADMIN API-KEY>` na klucz interfejsu API administratora dla Azure Search.

1. W sekcji treść dokumentu JSON zawiera parametry połączenia z kontem magazynu i nazwę kontenera obiektów BLOB. Parametry połączenia można znaleźć w Azure Portal wewnątrz **kluczy dostępu**konta magazynu. 

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

1. Kliknij przycisk **Wyślij** , aby wykonać żądanie.  Powinien zostać wyświetlony **stan: 201 Utworzono** komunikat jako odpowiedź.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Tworzenie zestawu umiejętności i magazynu wiedzy

Trzecia prośba korzysta z [interfejsu API tworzenia zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset), tworząc obiekt Azure Search, który określa, jakie umiejętności poznawcze są wywoływane, jak połączyć się ze sobą, i najważniejsze dla tego przewodnika — jak określić magazyn wiedzy.

1. W adresie `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`URL Zamień `YOUR-AZURE-SEARCH-SERVICE-NAME` na nazwę usługi wyszukiwania. 

1. W sekcji nagłówek Zamień `<YOUR AZURE SEARCH ADMIN API-KEY>` na klucz interfejsu API administratora dla Azure Search.

1. W sekcji body dokument JSON jest definicją zestawu umiejętności. Rozwinięta powłoka zewnętrzna zestawu umiejętności składa się z następujących elementów. Kolekcja definiuje wzbogacenia pamięci, `knowledgeStore` ale definicja określa sposób przechowywania danych wyjściowych. `skills` `cognitiveServices` Definicja jest połączeniem z aparatami wzbogacenia AI.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Rozwiń `cognitiveServices` i`knowledgeStore` , aby podać informacje o połączeniu. W tym przykładzie te ciągi znajdują się po definicji zestawu umiejętności w kierunku końca treści żądania. 

   W `cognitiveServices`przypadku programu zainicjuj zasób w warstwie S0 znajdujący się w tym samym regionie co Azure Search. Możesz uzyskać nazwę i klucz cognitiveServices z tej samej strony w Azure Portal. 
   
   Dla `knowledgeStore`programu można użyć tych samych parametrów połączenia, które są używane dla kontenera obiektów BLOB caselaw.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Rozwiń kolekcję umiejętności, w szczególności umiejętności kształtu odpowiednio w wierszach 85 i 179. Umiejętność kształtu jest ważna, ponieważ składa się ze struktur danych, które mają być przeznaczone do wyszukiwania w bazie wiedzy. Podczas wykonywania zestawu umiejętności te struktury są tylko w pamięci, ale po przejściu do następnego kroku zobaczysz, jak te dane wyjściowe można zapisać w sklepie z bazami danych w celu przeprowadzenia dalszej eksploracji.

   Poniższy fragment kodu pochodzi z wiersza 217. 

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

1. Rozwiń `projections` element w `knowledgeStore`, rozpoczynając od wiersza 262. Projekcje określają kompozycję ze sklepu wiedzy. Projekcje są określane w parach obiektów Tables, ale obecnie tylko jeden w czasie. Jak widać w pierwszej projekcji, jest określony `tables` , ale `objects` nie jest. W drugim jest to przeciwieństwo.

   W usłudze Azure Storage tabele zostaną utworzone w usłudze Table Storage dla każdej utworzonej tabeli, a każdy obiekt pobiera kontener w usłudze BLOB Storage.

   Obiekty blob zwykle zawierają pełne wyrażenie wzbogacania. Tabele zwykle zawierają częściowe wzbogacanie w kombinacjach, które są rozmieszczane do określonych celów. W tym przykładzie przedstawiono tabelę przypadków i tabelę opinii, ale nie są one wyświetlane w innych tabelach, takich jak jednostki, osoby prawnika, sędziowie i strony.

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

1. Kliknij przycisk **Wyślij** , aby wykonać żądanie. Odpowiedź powinna być **201** i wyglądać podobnie do poniższego przykładu, pokazując pierwszą część odpowiedzi.

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

## <a name="create-and-run-an-indexer"></a>Utwórz i uruchom indeksator

Czwarte żądanie używa [interfejsu API tworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer), tworząc indeksator Azure Search. Indeksator jest aparatem wykonywania potoku indeksowania. Wszystkie definicje utworzone do tej pory są umieszczane w przeniesieniu do tego kroku.

1. W adresie `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`URL Zamień `YOUR-AZURE-SEARCH-SERVICE-NAME` na nazwę usługi wyszukiwania. 

1. W sekcji nagłówek Zamień `<YOUR AZURE SEARCH ADMIN API-KEY>` na klucz interfejsu API administratora dla Azure Search.

1. W sekcji body dokumentu JSON określa nazwę indeksatora. Źródło danych i indeks są wymagane przez indeksator. Zestawu umiejętności jest opcjonalny dla indeksatora, ale jest wymagany do wzbogacania AI.

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

1. Rozwiń węzeł outputFieldMappings. W przeciwieństwie do fieldMappings, które są używane do mapowania niestandardowego między polami w źródle danych i polami w indeksie, outputFieldMappings są używane do mapowania ulepszonych pól, utworzonych i wypełnianych przez potok, do pól wyjściowych w indeksie lub projekcji.

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

1. Kliknij przycisk **Wyślij** , aby wykonać żądanie. Odpowiedź powinna wynosić **201** , a treść odpowiedzi powinna wyglądać prawie identyczna z podanym ładunkiem żądania (przyciętym do zwięzłości).

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

## <a name="explore-knowledge-store"></a>Eksplorowanie sklepu z bazami danych

Możesz zacząć Eksplorowanie natychmiast po zaimportowaniu pierwszego dokumentu. W przypadku tego zadania Użyj [**Eksplorator usługi Storage**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) w portalu.

Należy pamiętać, że magazyn wiedzy jest w pełni odłączony od Azure Search. Indeks Azure Search i magazyn wiedzy zawierają zarówno reprezentację danych, jak i zawartość, ale z tego względu. Użyj indeksu do wyszukiwania pełnotekstowego, wyszukiwania odfiltrowanego i wszystkich scenariuszy obsługiwanych w Azure Search. Lub przechodź do przodu ze swoim magazynem wiedzy, dołączając inne narzędzia do analizy zawartości.

## <a name="takeaways"></a>Wnioski

Utworzono swój pierwszy magazyn wiedzy w usłudze Azure Storage i użyto Eksplorator usługi Storage, aby wyświetlić wzbogacenia. Jest to podstawowe środowisko pracy z przechowywanymi wzbogacami. 

## <a name="next-steps"></a>Następne kroki

Umiejętność kształtu ma duże znaczenie dla tworzenia formularzy danych szczegółowych, które mogą być połączone z nowymi kształtami. W następnym kroku zapoznaj się ze stroną referencyjną tej umiejętności, aby uzyskać szczegółowe informacje na temat sposobu ich używania.

> [!div class="nextstepaction"]
> [Dokumentacja umiejętności kształtu](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
