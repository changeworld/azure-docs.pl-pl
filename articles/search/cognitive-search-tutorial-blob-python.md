---
title: 'Samouczek języka Python: Wywoływania usług Cognitive Services w potoku indeksowania — usługa Azure Search'
description: Krokowo przykładem wyodrębnianie danych, języka naturalnego oraz obrazu sztucznej Inteligencji przetwarzania w usłudze Azure Search za pomocą notesu Jupyter w języku Python. Wyodrębnione dane są indeksowane i łatwo uzyskać dostęp przez zapytanie.
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: 50a252ff93f7e2cc6e5c6100c6bce850e9e96baf
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295634"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Samouczek języka Python: Wywołania interfejsów API usług Cognitive Services w usłudze Azure Search indeksowanie potoku

Za pomocą tego samouczka poznasz mechanizm programistycznego wzbogacania danych w usłudze Azure Search przy użyciu *umiejętności poznawczych*. Umiejętności są wspierane przez możliwości analizy obrazu, w usługach Cognitive Services i przetwarzania języka naturalnego (NLP). Za pomocą zestawu umiejętności tworzenia i konfiguracji można wyodrębnić tekst i reprezentacja tekstowa obrazu lub pliku skanowanego dokumentu. Może także wykryć język, jednostki, kluczowe frazy i. Wynik jest sformatowany dodatkowej zawartości do indeksu usługi Azure Search, utworzony za pomocą sztucznej Inteligencji wzbogacenia w potoku usługi indeksowania. 

W tym samouczku użyjesz języka Python do wykonywania następujących zadań:

> [!div class="checklist"]
> * Utworzenie potoku indeksowania, który wzbogaca przykładowe dane w drodze do indeksu
> * Zastosowanie wbudowanych umiejętności: rozpoznawania jednostek, wykrywania języka, manipulacji tekstem i wyodrębniania fraz kluczowych
> * Poznanie sposobu łączenia umiejętności w łańcuch przez mapowanie danych wejściowych na dane wyjściowe w zestawie umiejętności
> * Wykonywanie żądań i przeglądanie wyników
> * Resetowanie indeksu i indeksatorów w celu kontynuowania opracowywania

Dane wyjściowe stanowią indeks z możliwością wyszukiwania pełnotekstowego w usłudze Azure Search. Indeks możesz rozszerzyć za pomocą innych standardowych możliwości, takich jak [synonimy](search-synonyms.md), [profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analizatory](search-analyzers.md) i [filtry](search-filters.md). 

W tym samouczku jest uruchamiany bezpłatnej usługi, ale liczba bezpłatnych transakcji jest ograniczona do 20 dokumentów na dzień. Jeśli chcesz uruchomić w tym samouczku więcej niż jeden raz w ciągu tego samego dnia, należy użyć mniejszy plik ustawione, tak więc mieści się w dodatkowych uruchomień.

> [!NOTE]
> Możesz rozwiń zakres, zwiększając częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, konieczne będzie [dołączyć płatnych zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie za istniejącą [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Cennik wyodrębniania obraz został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi, narzędzia i dane są używane w ramach tego samouczka. 

+ [Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych. Upewnij się, że konto magazynu jest w tym samym regionie co usługa Azure Search.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), zapewniając Python 3.x i notesy Jupyter.

+ [Przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) zawiera zestaw mały plik o różnych typach. 

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Do interakcji z usługi Azure Search, konieczne będzie adres URL usługi i klucza dostępu. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania, na podstawie danego żądania między aplikacją wysyłającą żądanie i usługi, która je obsługuje.

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

Potok wzbogacania ściąga dane ze źródeł danych platformy Azure. Dane muszą pochodzić ze źródła danych, którego typ jest obsługiwany przez [indeksator usługi Azure Search](search-indexer-overview.md). Usługa Azure Table Storage nie jest obsługiwana dla usłudze wyszukiwania poznawczego. Na potrzeby tego ćwiczenia będziemy korzystać z usługi Blob Storage, aby zaprezentować wiele typów zawartości.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawiera przykładowe dane. Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń, aby przekazać pliki przykładowe został pobrany w poprzednim kroku.

   ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Po załadowaniu przykładowych plików uzyskaj nazwę kontenera i parametry połączenia dla usługi Blob Storage. Te dane są dostępne po przejściu do konta usługi Storage w witrynie Azure Portal. Kliknij przycisk **klucze dostępu**, a następnie skopiuj **parametry połączenia** pola.

Parametry połączenia mają następujący format: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Zachowaj parametrów połączenia pod ręką. Będzie potrzebny w przyszłym kroku.

Istnieją inne sposoby określania parametrów połączenia, takie jak podanie sygnatury dostępu współdzielonego. Aby dowiedzieć się więcej na temat poświadczeń źródła danych, zobacz [Indeksowanie w usłudze Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

> [!Note]
> W tym artykule przedstawiono sposób tworzenia źródła danych, indeks, indeksator i zestawu umiejętności, używając szeregu skryptów języka Python. Aby pobrać przykład pełną Notes, przejdź do [repozytorium Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Nawigator Anaconda umożliwia uruchamianie notesu Jupyter i Utwórz nowy notes Python 3.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

W notesie Uruchom ten skrypt, aby załadować bibliotek używanych do pracy z formatu JSON i sformułowania żądań HTTP.

```python
import json
import requests
from pprint import pprint
```

Następnie zdefiniuj nazwy źródła danych, indeks, indeksator i zestawu umiejętności. Uruchom ten skrypt, aby skonfigurować nazwy na potrzeby tego samouczka.

```python
#Define the names for the data source, skillset, index and indexer
datasource_name="cogsrch-py-datasource"
skillset_name="cogsrch-py-skillset"
index_name="cogsrch-py-index"
indexer_name="cogsrch-py-indexer"
```

> [!Tip]
> Bezpłatne usługi są ograniczone do trzech indeksów, indeksatorów i źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Upewnij się, że dysponujesz miejscem do tworzenia nowych obiektów, zanim przejdziesz dalej.

W poniższym skrypcie Zastąp symbole zastępcze dla Twojej usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) i interfejs API administratora klucza (YOUR-ADMIN-API-KEY), a następnie uruchom go, aby skonfigurować punkt końcowy usługi wyszukiwania.

```python
#Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
        'api-key': '<YOUR-ADMIN-API-KEY>' }
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Tworzenie źródła danych

Teraz, gdy usługi i pliki źródłowe są gotowe, rozpocznij łączenie składników potoku indeksowania. Rozpoczynają się od obiektu źródła danych, które informują usługę Azure Search, jak pobrać zewnętrznego źródła danych.

W poniższym skrypcie Zastąp symbol zastępczy swojej-BLOB-RESOURCE-CONNECTION-STRING przy użyciu parametrów połączenia dla obiektu blob, który został utworzony w poprzednim kroku. Następnie uruchom skrypt, aby utworzyć źródło danych o nazwie `cogsrch-py-datasource`.

```python
#Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
    "connectionString": datasourceConnectionString
   },
    "container": {
     "name": "basic-demo-data-pr"
   }
}
r = requests.put( endpoint + "/datasources/" + datasource_name, data=json.dumps(datasource_payload), headers=headers, params=params )
print (r.status_code)
```

Żądania powinien zwrócić kod stanu 201 potwierdzeniem powodzenia.

W witrynie Azure portal na stronie pulpitu nawigacyjnego usługi wyszukiwania, sprawdź, czy cogsrch py datasource znajduje się w **źródeł danych** listy. Kliknij przycisk **Odśwież** do aktualizacji strony.

![Kafelek Źródła danych w portalu](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Kafelek Źródła danych w portalu")

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

W tym kroku zdefiniujesz zestaw kroków wzbogacania do zastosowania do danych. Każdy krok wzbogacania to *umiejętność*, a zestaw kroków wzbogacenia to *zestaw umiejętności*. W tym samouczku [wbudowanych umiejętności poznawcze](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) — dzielenie dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania fraz kluczowych. Umiejętność wyodrębniania fraz kluczowych przyjmuje dane wejściowe składające się maksymalnie z 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacje z zawartości w kontenerze obiektów blob.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych. 

### <a name="python-script"></a>Skrypt w języku Python
Uruchom następujący skrypt, aby utworzyć zestawu umiejętności o nazwie `cogsrch-py-skillset`.

```python
#Create a skillset
skillset_payload = {
  "name": skillset_name,
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
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
      "textSplitMode" : "pages",
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
          "name":"languageCode", "source": "/document/languageCode"
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

r = requests.put(endpoint + "/skillsets/" + skillset_name, data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Żądania powinien zwrócić kod stanu 201 potwierdzeniem powodzenia.

Umiejętności wyodrębnianie fraz kluczowych jest stosowany dla każdej strony. Ustawiając kontekście `"document/pages/*"`, uruchom to enricher dla każdego elementu członkowskiego tablicy stron/dokumentu (dla każdej strony dokumentu).

Każda umiejętność jest wykonywana dla zawartości dokumentu. Podczas przetwarzania usługa Azure Search analizuje każdy dokument w celu odczytania zawartości plików w różnych formatach. Tekst znaleziony w pliku źródłowym jest umieszczana w `content` pól, jeden dla każdego dokumentu. W związku z tym, zestawu danych wejściowych jako `"/document/content"`.

Graficzna reprezentacja zestawu umiejętności jest przedstawiona poniżej.

![Poznawanie zestawu umiejętności](media/cognitive-search-tutorial-blob/skillset.png "Poznawanie zestawu umiejętności")

Dane wyjściowe mogą być mapowane do indeksu, który jest używany jako dane wejściowe podrzędnego umiejętności i / lub, tak jak w przypadku przy użyciu kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Tworzenie indeksu

W tej sekcji można zdefiniować schemat indeksu Określanie pól, które mają zostać objęte przeszukiwanie indeksu, a ustawienie atrybutów wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| field-names: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Uruchom ten skrypt, aby utworzyć indeks o nazwie `cogsrch-py-index`.

```python
#Create an index
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

r = requests.put(endpoint + "/indexes/" + index_name, data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Żądania powinien zwrócić kod stanu 201 potwierdzeniem powodzenia.

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [Tworzenie indeksu (interfejs API REST usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Tworzenie indeksatora, mapowanie pól i wykonywanie przekształceń

Do tej pory utworzono źródła danych, zestawu umiejętności i indeksu. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Powiązanie tych obiektów w indeksatorze, należy zdefiniować mapowania pól.

+ FieldMappings są przetwarzane przed zestawu umiejętności, mapowanie pola źródłowego ze źródła danych do docelowej pól w indeksie. Jeśli nazwy pól i typów są takie same na obu końcach, żadne mapowanie jest wymagana.

+ OutputFieldMappings są przetwarzane od zestawu umiejętności, odwołuje się do sourceFieldNames, które nie istnieją aż łamania dokumentów lub wzbogacania ich tworzenia. TargetFieldName jest polem w indeksie.

Oprócz Podłączanie dane wejściowe, aby dane wyjściowe, umożliwia także mapowania pól do spłaszczenia struktur danych. Aby uzyskać więcej informacji, zobacz [sposób mapowania pól wzbogaconego na indeks z możliwością wyszukiwania](cognitive-search-output-field-mapping.md).

Uruchom ten skrypt, aby utworzyć indeksatora o nazwie `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
   "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
   "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}

r = requests.put(endpoint + "/indexers/" + indexer_name, data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Żądanie szybko powinna zwrócić kod stanu 201, jednak przetwarzanie może zająć kilka minut. Mimo że zestaw danych jest mała, umiejętności analitycznych, takich jak analiza obrazu, czy wymaga dużej mocy obliczeniowej i czasu.

Użyj [Sprawdź stan indeksatora](#check-indexer-status) skryptu w następnej sekcji, aby określić, po zakończeniu procesu indeksatora.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występuje problem z dostępem do danych, mapowanie danych wejściowych i wyjściowych, lub z kolejność operacji, pojawi się na tym etapie. Aby ponownie uruchomić potok ze zmianami kodu lub skryptu, konieczne może być najpierw usuń obiekty. Aby uzyskać więcej informacji, zobacz [Resetowanie i ponowne uruchamianie](#reset).

#### <a name="explore-the-request-body"></a>Eksplorowanie treści żądania

Skrypt ustawia dla elementu `"maxFailedItems"` wartość -1, która powoduje, że aparat indeksowania ignoruje błędy podczas importowania danych. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Ponadto zwróć uwagę na instrukcję `"dataToExtract":"contentAndMetadata"` w parametrach konfiguracji. Ta instrukcja każe indeksator do wyodrębniania zawartości z innych formatów plików i metadanych związanych z każdego pliku.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Konfiguracja `"imageAction":"generateNormalizedImages"`, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, wskazuje indeksatorowi, aby wyodrębniał tekst z obrazów (np. wyraz „stop” ze znaku drogowego Stop) i osadzał go jako część pola zawartości. To zachowanie ma zastosowanie w źródle danych, na przykład plik JPG obrazów i obrazów osadzonych w dokumentach (myśl obraz w pliku PDF).

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Sprawdzanie stanu indeksatora

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy przetwarzanie indeksatora zostało ukończone, uruchom następujący skrypt.

```python
#Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name + "/status", headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

W odpowiedzi monitorowanie "lastResult" dla wartości "status" i "endTime". Okresowe uruchamianie skryptu, aby sprawdzić stan. Po zakończeniu indeksatora stan zostanie ustawiony "Powodzenie", "endTime" zostanie określony, a odpowiedź będzie zawierać wszelkie błędy i ostrzeżenia, które wystąpiły podczas wzbogacania.

![Zostanie utworzony indeksator](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "utworzeniu indeksatora")

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku sygnalizują poważnych ostrzeżenia. Na przykład jeden z plików JPEG, które nie ma tekstu pokaże ostrzeżenia na tym zrzucie ekranu.

![Ostrzeżenie indeksatora przykład](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "przykładowy indeksator ostrzeżenie")

## <a name="query-your-index"></a>Tworzenie zapytań względem indeksu

Po zakończeniu indeksowania uruchom zapytania zwracające zawartość poszczególnych pól. Domyślnie usługa Azure Search zwraca 50 najlepszych wyników. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [Jak wyświetlać strony wyników w usłudze Azure Search](search-pagination-page-layout.md).

W ramach kroku weryfikacji odpytaj indeks o wszystkie pola.

```python
#Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name, headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Wyniki powinny wyglądać podobnie do poniższego przykładu. Zrzut ekranu przedstawia tylko część odpowiedzi.

![Indeks zapytania dla wszystkich pól](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "tworzenie zapytań względem indeksu dla wszystkich pól")

Dane wyjściowe to schemat indeksu z nazwą, typem i atrybutami każdego pola.

Prześlij drugie zapytanie o element `"*"`, co spowoduje zwrócenie całej zawartości pojedynczego pola, takiego jak `organizations`.

```python
#Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name + "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Wyniki powinny wyglądać podobnie do poniższego przykładu. Zrzut ekranu przedstawia tylko część odpowiedzi.

![Indeks zapytania dla zawartości organizacji](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "tworzenie zapytań względem indeksu, aby zwrócić zawartość organizacji")

Powtórz tę procedurę dla pola dodatkowe: zawartość, languageCode, keyPhrases i organizacji, w tym ćwiczeniu. Istnieje możliwość zwrócenia wielu pól za pomocą elementu `$select` używającego listy wartości rozdzielonych przecinkami.

W zależności od złożoności i długości ciągu zapytania można użyć metody GET lub POST. Aby uzyskać więcej informacji, zobacz [Odpytywanie przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).
go <a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

Na wczesnych, eksperymentalnych etapach opracowywania potoku najbardziej praktycznym podejściem do iteracji projektowych jest usunięcie obiektów z usługi Azure Search i odbudowanie ich przy użyciu kodu. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Aby zaindeksować dokumenty za pomocą nowych definicji:

1. Usuń indeks, aby usunąć utrwalone dane. Usuń indeksator, aby utworzyć go ponownie w usłudze.
2. Modyfikowanie definicji zestawu umiejętności i indeksu.
3. Utwórz ponownie indeks i indeksator dla usługi w celu uruchomienia potoku.

Portal umożliwia usunięcie indeksów, indeksatorów i dokładniejsze. Po usunięciu indeksatora, można opcjonalnie, selektywnie usunąć źródło indeksu, zestawu umiejętności i danych w tym samym czasie.

![Usuwanie obiektów wyszukiwania](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "wyszukiwania usuwania obiektów w portalu")

Można również usunąć je za pomocą skryptu. Poniższy skrypt spowoduje usunięcie zestawu umiejętności, którą utworzyliśmy. Można łatwo zmodyfikować żądanie usunięcia indeks, indeksator i źródło danych.

```python
#delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name, headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

W miarę rozwoju kodu można udoskonalić strategię odbudowywania. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia wzbogaconego potoku indeksowania przez utworzenie elementów będących jego składnikami: źródła danych, zestawu umiejętności, indeksu i indeksatora.

[Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) zostały wprowadzone, wraz z definicji zestawu umiejętności oraz sposób łańcuch umiejętności razem za pośrednictwem danych wejściowych i wyjściowych. Opisano także element `outputFieldMappings` w definicji indeksatora, który jest wymagany do kierowania wzbogaconych wartości z potoku do indeksu z możliwością wyszukiwania w usłudze Azure Search.

Na koniec pokazaliśmy ci, jak przetestować wyniki i zresetowanie systemu pod kątem dalsze iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. W tej wersji jest dostępny mechanizm wyświetlania wewnętrznych konstrukcji (wzbogaconych dokumentów utworzonych przez system). Przedstawiono również sposób sprawdzić stan indeksatora i jakie obiekty muszą zostać usunięte przed ponowne uruchamianie potoku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search i usługę Azure Blob Service. Przy założeniu, że obie te usługi należy umieścić w tej samej grupy, Usuń grupę zasobów, aby trwale usunąć wszystkie elementy, łącznie z usługami i przechowywana zawartość, który został utworzony na potrzeby tego samouczka. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd każdej usługi.

## <a name="next-steps"></a>Kolejne kroki

Dostosuj lub rozszerz potok za pomocą umiejętności niestandardowych. Utworzenie umiejętności niestandardowej i dołączenie jej do zestawu umiejętności pozwala na dodanie samodzielnie napisanej analizy tekstu lub obrazu.

> [!div class="nextstepaction"]
> [Przykład: tworzenie umiejętności niestandardowej](cognitive-search-create-custom-skill-example.md)
