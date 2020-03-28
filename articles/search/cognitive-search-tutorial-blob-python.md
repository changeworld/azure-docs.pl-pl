---
title: 'Samouczek: Python i sztuczna inteligencja nad obiektami blob platformy Azure'
titleSuffix: Azure Cognitive Search
description: Krok po przykładzie wyodrębniania tekstu i przetwarzania języka naturalnego za pośrednictwem zawartości w magazynie obiektów Blob przy użyciu notesu języka Python Jupyter i interfejsów API rest usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78194058"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Samouczek: Generowanie zawartości z przeszukiwania z obiektów blob platformy Azure za pomocą języka Python i sztucznej inteligencji

Jeśli masz nieustrukturyzowanego tekstu lub obrazów w magazynie obiektów Blob platformy Azure potok [wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md) można wyodrębnić informacje i utworzyć nową zawartość, która jest przydatna w przypadku wyszukiwania pełnotekstowego lub eksploracji wiedzy scenariuszy. Chociaż potok może przetwarzać obrazy, ten samouczek języka Python koncentruje się na tekście, stosując wykrywanie języka i przetwarzanie języka naturalnego w celu utworzenia nowych pól, które można wykorzystać w kwerendach, aspektach i filtrach.

W tym samouczku użyto interfejsu Api języka Python i [rest wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zacznij od całych dokumentów (tekstu nieustrukturyzowanego), takich jak PDF, HTML, DOCX i PPTX w magazynie obiektów Blob platformy Azure.
> * Zdefiniuj potok, który wyodrębnia tekst, wykrywa język, rozpoznaje jednostki i wykrywa kluczowe frazy.
> * Zdefiniuj indeks do przechowywania danych wyjściowych (zawartość nieprzetworzona oraz pary nazwa-wartość wygenerowana przez potok).
> * Wykonaj potok, aby rozpocząć transformacje i analizy oraz utworzyć i załadować indeks.
> * Eksploruj wyniki przy użyciu wyszukiwania pełnotekstowego i składni kwerendy rozszerzonej.

Jeśli nie masz subskrypcji platformy Azure, otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anakonda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Tworzenie](search-create-service-portal.md) lub [znajdowanie istniejącej usługi wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby zaakceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

1. Otwórz ten [folder usługi OneDrive,](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a w lewym górnym rogu kliknij pozycję **Pobierz,** aby skopiować pliki na komputer. 

1. Kliknij prawym przyciskiem myszy plik zip i wybierz polecenie **Wyodrębnij wszystkie**. Istnieje 14 plików różnych typów. Użyjesz 7 do tego ćwiczenia.

## <a name="1---create-services"></a>1 - Tworzenie usług

W tym samouczku użyto usługi Azure Cognitive Search do indeksowania i kwerend, usług Cognitive Services w wewnętrznej bazy danych w celu wzbogacania sztucznej inteligencji i magazynu obiektów Blob platformy Azure w celu dostarczenia danych. Ten samouczek pozostaje w ramach bezpłatnej alokacji 20 transakcji na indeksator dziennie w usługach Cognitive Services, więc jedynymi usługami, które musisz utworzyć, są wyszukiwanie i przechowywanie.

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

1. Kliknij **+ Kontener,** aby utworzyć kontener i nazwać go *cog-search-demo*.

1. Wybierz *cog-search-demo,* a następnie kliknij przycisk **Przekaż,** aby otworzyć folder, w którym zostały zapisane pliki pobierania. Zaznacz wszystkie pliki niebędące obrazami. Powinieneś mieć 7 plików. Kliknij **przycisk OK,** aby przesłać.

   ![Przekazywanie przykładowych plików](media/cognitive-search-tutorial-blob/sample-files.png "Przekazywanie przykładowych plików")

1. Przed opuszczeniem usługi Azure Storage pobierz ciąg połączenia, aby można było sformułować połączenie w usłudze Azure Cognitive Search. 

   1. Przejdź z powrotem do strony Przegląd konta magazynu (użyliśmy *blobstragewestus* jako przykład). 
   
   1. W lewym okienku nawigacji wybierz pozycję **Klawisze programu Access** i skopiuj jeden z ciągów połączeń. 

   Parametry połączenia to adres URL podobny do następującego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Zapisz ciąg połączenia w Notatniku. Będzie on potrzebny później podczas konfigurowania połączenia źródła danych.

### <a name="cognitive-services"></a>Cognitive Services

Wzbogacanie sztucznej inteligencji jest wspierane przez usługi Cognitive Services, w tym analizę tekstu i wizję komputerową w zakresie przetwarzania języka naturalnego i obrazu. Jeśli celem było ukończenie rzeczywistego prototypu lub projektu, w tym momencie należy udostępnić usługi Cognitive Services (w tym samym regionie co usługa Azure Cognitive Search), aby można było dołączyć go do operacji indeksowania.

W tym ćwiczeniu można jednak pominąć inicjowanie obsługi administracyjnej zasobów, ponieważ usługa Azure Cognitive Search może łączyć się z usługami Cognitive Services w tle i zapewniać 20 bezpłatnych transakcji na uruchomienie indeksatora. Ponieważ ten samouczek używa 7 transakcji, przydział bezpłatnych uprawnień jest wystarczający. W przypadku większych projektów należy zaplanować inicjowanie obsługi administracyjnej usług Cognitive Services w warstwie S0 zgodnie z rzeczywistym obciążeniem. Aby uzyskać więcej informacji, zobacz [Dołączanie usług Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Trzecim składnikiem jest usługa Azure Cognitive Search, którą można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten instruktaż, można użyć warstwy Bezpłatna. 

Podobnie jak w tym magazynie obiektów Blob platformy Azure, poświęć chwilę, aby zebrać klucz dostępu. Dalej po rozpoczęciu tworzenia żądań strukturyzowania należy podać punkt końcowy i klucz interfejsu api administratora używany do uwierzytelniania każdego żądania.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz api administratora i adres URL usługi Azure Cognitive Search

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania uzyskaj nazwę usługi wyszukiwania. Nazwę usługi można potwierdzić, przeglądając adres URL punktu końcowego. Jeśli adres URL `https://mydemo.search.windows.net`punktu końcowego był, nazwa usługi będzie `mydemo`.

2. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

   Pobierz klucz zapytania, jak również. Jest najlepszym rozwiązaniem do wystawiania żądań zapytań z dostępem tylko do odczytu.

   ![Pobierz nazwę usługi i klucze administratora i zapytania](media/search-get-started-nodejs/service-name-and-keys.png)

Wszystkie żądania wymagają klucza interfejsu API w nagłówku każdego żądania wysłanego do usługi. Prawidłowy klucz ustanawia zaufanie, na podstawie żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="2---start-a-notebook"></a>2 - Uruchamianie notesu

Utwórz notes, korzystając z poniższych instrukcji, lub pobierz gotowy notes z [repozytorium azure-search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Użyj Anaconda Navigator, aby uruchomić notebook Jupyter i utworzyć nowy notes Pythona 3.

W notesie uruchom ten skrypt, aby załadować biblioteki używane do pracy z json i formułowania żądań HTTP.

```python
import json
import requests
from pprint import pprint
```

W tym samym notesie zdefiniuj nazwy źródła danych, indeksu, indeksatora i zestawu umiejętności. Uruchom ten skrypt, aby skonfigurować nazwy dla tego samouczka.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

W poniższym skrypcie zastąp symbole zastępcze usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) i klucza interfejsu API administratora (YOUR-ADMIN-API-KEY), a następnie uruchom go w celu skonfigurowania punktu końcowego usługi wyszukiwania.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3 - Tworzenie potoku

W usłudze Azure Cognitive Search przetwarzanie sztucznej inteligencji występuje podczas indeksowania (lub pozyskiwania danych). Ta część przewodnika tworzy cztery obiekty: źródło danych, definicja indeksu, skillset, indeksator. 

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

[Obiekt źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) udostępnia ciąg połączenia z kontenerem obiektu Blob zawierającym pliki.

W poniższym skrypcie zastąp zastępczy ciąg YOUR-BLOB-RESOURCE-CONNECTION-STRING ciągiem połączenia dla obiektu blob utworzonego w poprzednim kroku. Zastąp tekst zastępczy kontenera. Następnie uruchom skrypt, aby utworzyć `cogsrch-py-datasource`źródło danych o nazwie .

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 potwierdzający powodzenie.

W witrynie Azure portal na stronie pulpitu nawigacyjnego usługi wyszukiwania sprawdź, czy źródło danych cogsrch-py jest wyświetlane na liście **Źródła danych.** Kliknij **przycisk Odśwież,** aby zaktualizować stronę.

![Kafelek źródeł danych w portalu](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Kafelek źródeł danych w portalu")

### <a name="step-2-create-a-skillset"></a>Krok 2: Tworzenie umiejętności

W tym kroku zdefiniujesz zestaw kroków wzbogacania, które mają być stosowane do danych. Każdy krok wzbogacania to *umiejętność*, a zestaw kroków wzbogacenia to *zestaw umiejętności*. W tym samouczku użyto [wbudowanych umiejętności poznawczych](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacji z zawartości w kontenerze obiektów blob.

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) — dzielenie dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania fraz kluczowych. Umiejętność wyodrębniania fraz kluczowych przyjmuje dane wejściowe składające się maksymalnie z 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych. 

Uruchom następujący skrypt, aby utworzyć `cogsrch-py-skillset`zestaw umiejętności o nazwie .

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 potwierdzający powodzenie.

Umiejętności wyodrębniania fraz kluczowych są stosowane dla każdej strony. Ustawiając kontekst `"document/pages/*"`na , należy uruchomić ten wzbogacacz dla każdego elementu członkowskiego tablicy dokumentu/strony (dla każdej strony w dokumencie).

Każda umiejętność jest wykonywana dla zawartości dokumentu. Podczas przetwarzania usługa Azure Cognitive Search pęka każdy dokument, aby odczytać zawartość z różnych formatów plików. Tekst znaleziony w pliku źródłowym `content` jest umieszczany w polu, po jednym dla każdego dokumentu. W związku z tym `"/document/content"`ustaw dane wejściowe jako .

Graficzna reprezentacja zestawu umiejętności jest przedstawiona poniżej.

![Opis zestaw umiejętności](media/cognitive-search-tutorial-blob/skillset.png "Opis zestaw umiejętności")

Dane wyjściowe mogą być mapowane do indeksu, używane jako dane wejściowe do niższego rzędu umiejętności lub obu, jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: Tworzenie indeksu

W tej sekcji można zdefiniować schemat indeksu, określając pola, które mają być uwzględnione w indeksie z wyszukującymi, i ustawiając atrybuty wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| field-names: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Uruchom ten skrypt, aby `cogsrch-py-index`utworzyć indeks o nazwie .

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno zwrócić kod stanu 201 potwierdzający powodzenie.

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [Tworzenie indeksu (interfejsu API REST usługi Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Tworzenie i uruchamianie indeksatora

[Indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer) kieruje potoku. Trzy składniki utworzone do tej pory (źródło danych, skillset, index) są dane wejściowe do indeksatora. Tworzenie indeksatora w usłudze Azure Cognitive Search jest zdarzeniem, które uruchamia cały potok. 

Aby połączyć te obiekty ze sobą w indeksatorze, należy zdefiniować mapowania pól.

+ FieldMappings są przetwarzane przed zestawem umiejętności, mapowanie pól źródłowych ze źródła danych do pól docelowych w indeksie. Jeśli nazwy pól i typy są takie same na obu końcach, nie jest wymagane mapowanie.

+ OutputFieldMappings są przetwarzane po skillset, odwołując się sourceFieldNames, które nie istnieją, dopóki dokument pękanie lub wzbogacanie tworzy je. TargetFieldName jest polem w indeksie.

Oprócz podłączania wejść do wyjść, można również użyć mapowania pola, aby spłaszczyć struktury danych. Aby uzyskać więcej informacji, zobacz [Jak mapować wzbogacone pola do indeksu z wyszukanego](cognitive-search-output-field-mapping.md).

Uruchom ten skrypt, aby `cogsrch-py-indexer`utworzyć indeksator o nazwie .

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie powinno wkrótce zwrócić kod stanu 201, jednak przetworzenie może potrwać kilka minut. Chociaż zestaw danych jest mały, umiejętności analityczne, takie jak analiza obrazu, są intensywne obliczeniowo i zajmują trochę czasu.

Można [monitorować stan indeksatora,](#check-indexer-status) aby określić, kiedy indeksator jest uruchomiony lub zakończony.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występuje problem z dostępem do danych, mapowania wejść i wyjść lub z kolejnością operacji, pojawi się na tym etapie. Aby ponownie uruchomić potok ze zmianami kodu lub skryptu, może być konieczne najpierw usunięcie obiektów. Aby uzyskać więcej informacji, zobacz [Resetowanie i ponowne uruchamianie](#reset).

#### <a name="about-the-request-body"></a>Informacje o treści żądania

Skrypt ustawia dla elementu `"maxFailedItems"` wartość -1, która powoduje, że aparat indeksowania ignoruje błędy podczas importowania danych. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Ponadto zwróć uwagę na instrukcję `"dataToExtract":"contentAndMetadata"` w parametrach konfiguracji. Ta instrukcja informuje indeksatora, aby wyodrębnić zawartość z różnych formatów plików i metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Konfiguracja `"imageAction":"generateNormalizedImages"`, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, wskazuje indeksatorowi, aby wyodrębniał tekst z obrazów (np. wyraz „stop” ze znaku drogowego Stop) i osadzał go jako część pola zawartości. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (pomyśl o obrazie w pliku PDF), jak i obrazów znalezionych w źródle danych, na przykład pliku JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorowanie indeksowania

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy przetwarzanie indeksatora jest zakończone, uruchom następujący skrypt.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

W odpowiedzi należy monitorować "lastResult" dla jego "status" i "endTime" wartości. Okresowo uruchamiaj skrypt, aby sprawdzić stan. Po zakończeniu indeksatora stan zostanie ustawiony na "sukces", zostanie określony "endTime", a odpowiedź będzie zawierać wszelkie błędy i ostrzeżenia, które wystąpiły podczas wzbogacania.

![Indeksator jest tworzony](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indeksator jest tworzony")

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. Wiele ostrzeżeń jest łagodnych. Na przykład jeśli indeksujesz plik JPEG, który nie zawiera tekstu, zobaczysz ostrzeżenie na tym zrzucie ekranu.

![Przykładowe ostrzeżenie indeksatora](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Przykładowe ostrzeżenie indeksatora")

## <a name="5---search"></a>5 - Szukaj

Po zakończeniu indeksowania uruchom zapytania zwracające zawartość poszczególnych pól. Domyślnie usługa Azure Cognitive Search zwraca 50 najlepszych wyników. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [Jak wyświetlić wyniki strony w usłudze Azure Cognitive Search](search-pagination-page-layout.md).

Jako krok weryfikacji pobierz definicję indeksu pokazującą wszystkie pola.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Wyniki powinny wyglądać podobnie do poniższego przykładu. Zrzut ekranu pokazuje tylko część odpowiedzi.

![Indeks kwerend dla wszystkich pól](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Kwerenda indeksu dla wszystkich pól")

Dane wyjściowe to schemat indeksu z nazwą, typem i atrybutami każdego pola.

Prześlij drugie zapytanie o element `"*"`, co spowoduje zwrócenie całej zawartości pojedynczego pola, takiego jak `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Wyniki powinny wyglądać podobnie do poniższego przykładu. Zrzut ekranu pokazuje tylko część odpowiedzi.

![Indeks kwerend zawartości organizacji](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Kwerenda indeksu, aby zwrócić zawartość organizacji")

Powtórz dla dodatkowych pól: zawartość, languageCode, keyPhrases i organizacji w tym ćwiczeniu. Istnieje możliwość zwrócenia wielu pól za pomocą elementu `$select` używającego listy wartości rozdzielonych przecinkami.

W zależności od złożoności i długości ciągu zapytania można użyć metody GET lub POST. Aby uzyskać więcej informacji, zobacz [Odpytywanie przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalnych rozwoju najbardziej praktyczne podejście do iteracji projektowania jest usunięcie obiektów z usługi Azure Cognitive Search i umożliwić kod, aby je odbudować. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Za pomocą portalu można usuwać indeksy, indeksatory, źródła danych i zestawy umiejętności. Po usunięciu indeksatora można opcjonalnie selektywnie usunąć indeks, zestaw umiejętności i źródło danych w tym samym czasie.

![Usuwanie obiektów wyszukiwania](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Usuwanie obiektów wyszukiwania w portalu")

Można je również usunąć za pomocą skryptu. Poniższy skrypt pokazuje, jak usunąć zestaw umiejętności. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia wzbogaconego potoku indeksowania przez utworzenie elementów będących jego składnikami: źródła danych, zestawu umiejętności, indeksu i indeksatora.

[Wprowadzono wbudowane umiejętności,](cognitive-search-predefined-skills.md) wraz z definicjami zestawów umiejętności i sposobem na łączenie umiejętności poprzez dane wejściowe i wyjściowe. Dowiedziałeś się `outputFieldMappings` również, że w definicji indeksatora jest wymagane do routingu wzbogacone wartości z potoku do indeksu z wyszukujalny w usłudze Azure Cognitive Search.

Na koniec dowiedziałeś się, jak przetestować wyniki i zresetować system do dalszych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. W tej wersji jest dostępny mechanizm wyświetlania wewnętrznych konstrukcji (wzbogaconych dokumentów utworzonych przez system). Dowiesz się również, jak sprawdzić stan indeksatora i jakie obiekty muszą zostać usunięte przed ponownym uruchomieniem potoku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza Wszystkie zasoby lub Grupy zasobów w lewym okienku nawigacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz wszystkie obiekty w potoku wzbogacania SI, przyjrzyjmy się bliżej definicjom zestawów umiejętności i indywidualnym umiejętnościom.

> [!div class="nextstepaction"]
> [Jak utworzyć zestaw umiejętności](cognitive-search-defining-skillset.md)
