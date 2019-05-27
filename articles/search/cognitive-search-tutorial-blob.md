---
title: 'Samouczek: Wywoływanie interfejsów API REST usług Cognitive w potoku indeksowania — usługa Azure Search'
description: Krokowo przykładem wyodrębnianie danych, języka naturalnego oraz obrazu sztucznej Inteligencji przetwarzania w usłudze Azure Search indeksowanie podczas przekształcania i wyodrębnianie danych za pośrednictwem obiektów blob JSON za pomocą interfejsu API REST i narzędzia Postman.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 09cef1758b247810f4ef03be9ebe01b498238ac9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242828"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Samouczek dotyczący architektury REST: Wywołania interfejsów API usług Cognitive Services w usłudze Azure Search indeksowanie potoku

Za pomocą tego samouczka poznasz mechanizm programistycznego wzbogacania danych w usłudze Azure Search przy użyciu *umiejętności poznawczych*. Umiejętności są wspierane przez możliwości analizy obrazu, w usługach Cognitive Services i przetwarzania języka naturalnego (NLP). Za pomocą zestawu umiejętności tworzenia i konfiguracji można wyodrębnić tekst i reprezentacja tekstowa obrazu lub pliku skanowanego dokumentu. Może także wykryć język, jednostki, kluczowe frazy i. Wynik końcowy to zaawansowane dodatkowej zawartości do indeksu usługi Azure Search, utworzony za pomocą sztucznej Inteligencji wzbogacenia w potoku usługi indeksowania. 

W tym samouczku interfejs API REST jest wywoływany w celu wykonania następujących zadań:

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

+ [Aplikacja klasyczna narzędzia postman](https://www.getpostman.com/) jest używany dla wywołań REST do usługi Azure Search.

+ [Przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) zawiera zestaw mały plik o różnych typach. 

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

Potok wzbogacania ściąga dane ze źródeł danych platformy Azure. Dane muszą pochodzić ze źródła danych, którego typ jest obsługiwany przez [indeksator usługi Azure Search](search-indexer-overview.md). Usługa Azure Table Storage nie jest obsługiwana dla usłudze wyszukiwania poznawczego. Na potrzeby tego ćwiczenia będziemy korzystać z usługi Blob Storage, aby zaprezentować wiele typów zawartości.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawiera przykładowe dane. Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń, aby przekazać pliki przykładowe został pobrany w poprzednim kroku.

   ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Po załadowaniu przykładowych plików uzyskaj nazwę kontenera i parametry połączenia dla usługi Blob Storage. Te dane są dostępne po przejściu do konta usługi Storage w witrynie Azure Portal. Na stronie **Klucze dostępu**, a następnie skopiuj pole **Parametry połączenia**.

   Parametry połączenia powinny być adresem URL podobnym do następującego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Istnieją inne sposoby określania parametrów połączenia, takie jak podanie sygnatury dostępu współdzielonego. Aby dowiedzieć się więcej na temat poświadczeń źródła danych, zobacz [Indeksowanie w usłudze Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie jesteś zaznajomiony z tego narzędzia, zobacz [Eksplorowanie usługi Azure Search interfejsów API REST przy użyciu narzędzia Postman](search-fiddler.md).

Metody żądania, w tym samouczku używane są **WPIS**, **umieścić**, i **UZYSKAĆ**. Klucze nagłówka to "Content-type" wartość "application/json" i "api-key" Ustaw klucz administratora usługi Azure Search. Treść to miejsce, w którym umieszcza się właściwą zawartość wywołania. 

  ![Wyszukiwanie częściowo ustrukturyzowane](media/search-semi-structured-data/postmanoverview.png)

Używamy narzędzia Postman wykonywania cztery wywołań interfejsu API usługi wyszukiwania w celu utworzenia źródła danych, zestawu umiejętności, indeksu i indeksatora. Źródło danych zawiera wskaźnik do konta magazynu i danych JSON. Usługa wyszukiwania nawiązuje połączenie podczas ładowania danych.


## <a name="create-a-data-source"></a>Tworzenie źródła danych

Teraz, gdy usługi i pliki źródłowe są gotowe, rozpocznij łączenie składników potoku indeksowania. Zacznij od [obiektu źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source), który informuje usługę Azure Search o tym, jak pobrać zewnętrzne dane źródłowe.

W nagłówku żądania podaj nazwę usługi użytą podczas tworzenia usługi Azure Search i klucz api-key wygenerowany dla usługi wyszukiwania. W treści żądania określ nazwę kontenera obiektów blob i parametry połączenia.

### <a name="sample-request"></a>Przykładowe żądanie
```http
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Wyślij żądanie. Internetowe narzędzie do testowania powinno zwrócić kod stanu 201 oznaczający powodzenie. 

Ponieważ jest to pierwsze żądanie, sprawdź w witrynie Azure Portal, czy źródło danych zostało utworzone w usłudze Azure Search. Na stronie pulpitu nawigacyjnego usługi wyszukiwania Sprawdź, czy lista źródeł danych, ma nowy element. Może trzeba będzie zaczekać kilka minut na odświeżenie strony portalu. 

  ![Kafelek Źródła danych w portalu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kafelek Źródła danych w portalu")

Jeśli otrzymujesz błąd 403 lub 404, sprawdź, czy żądanie jest poprawnie skonstruowane: w punkcie końcowym powinien znajdować się element `api-version=2019-05-06`, natomiast w nagłówku, po elemencie `Content-Type`, powinien znajdować się element `api-key`, a jego wartość musi być prawidłowa dla usługi wyszukiwania. Tego nagłówka możesz użyć ponownie w pozostałych krokach tego samouczka.

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

W tym kroku zdefiniujesz zestaw kroków wzbogacania, które mają być stosowane dla danych. Każdy krok wzbogacania to *umiejętność*, a zestaw kroków wzbogacenia to *zestaw umiejętności*. W tym samouczku [wbudowanych umiejętności poznawcze](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) — dzielenie dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania fraz kluczowych. Umiejętność wyodrębniania fraz kluczowych przyjmuje dane wejściowe składające się maksymalnie z 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacje z zawartości w kontenerze obiektów blob.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych. 

### <a name="sample-request"></a>Przykładowe żądanie
Przed wykonaniem tego wywołania REST pamiętaj o zastąpieniu nazwy usługi i klucza administratora w żądaniu poniżej, jeśli narzędzie nie zachowuje nagłówka żądania między wywołaniami. 

To żądanie powoduje utworzenie zestawu umiejętności. W pozostałej części tego samouczka przywołuj ten zestaw umiejętności za pomocą nazwy ```demoskillset```.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
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
```

Wyślij żądanie. Internetowe narzędzie do testowania powinno zwrócić kod stanu 201 oznaczający powodzenie. 

#### <a name="explore-the-request-body"></a>Eksplorowanie treści żądania

Zwróć uwagę, jak umiejętność wyodrębniania fraz kluczowych jest stosowana dla każdej strony. Ustawienie kontekstu na ```"document/pages/*"``` powoduje uruchomienie tego elementu wzbogacającego dla każdego elementu członkowskiego tablicy dokumentów/stron (dla każdej strony w dokumencie).

Każda umiejętność jest wykonywana dla zawartości dokumentu. Podczas przetwarzania usługa Azure Search analizuje każdy dokument w celu odczytania zawartości plików w różnych formatach. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. Dlatego dane wejściowe należy określić jako ```"/document/content"```.

Graficzna reprezentacja zestawu umiejętności jest przedstawiona poniżej. 

![Poznawanie zestawu umiejętności](media/cognitive-search-tutorial-blob/skillset.png "Poznawanie zestawu umiejętności")

Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Tworzenie indeksu

W tej sekcji zdefiniujesz schemat indeksu przez określenie pól do umieszczenia w indeksie z możliwością wyszukiwania oraz atrybutów wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Przykładowe żądanie
Przed wykonaniem tego wywołania REST pamiętaj o zastąpieniu nazwy usługi i klucza administratora w żądaniu poniżej, jeśli narzędzie nie zachowuje nagłówka żądania między wywołaniami. 

To żądanie powoduje utworzenie indeksu. W pozostałej części tego samouczka używaj nazwy indeksu ```demoindex```.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Składnia treści żądania

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Wyślij żądanie. Internetowe narzędzie do testowania powinno zwrócić kod stanu 201 oznaczający powodzenie. 

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [Tworzenie indeksu (interfejs API REST usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Tworzenie indeksatora, mapowanie pól i wykonywanie przekształceń

Do tej pory utworzono źródło danych, zestaw umiejętności i indeks. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Aby powiązać je razem w indeksatorze, należy zdefiniować mapowania pól. 

+ FieldMappings są przetwarzane przed zestawu umiejętności, mapowanie pola źródłowego ze źródła danych do docelowej pól w indeksie. Jeśli nazwy pól i typów są takie same na obu końcach, żadne mapowanie jest wymagana.

+ OutputFieldMappings są przetwarzane od zestawu umiejętności, odwołuje się do sourceFieldNames, które nie istnieją aż łamania dokumentów lub wzbogacania ich tworzenia. TargetFieldName jest polem w indeksie.

Oprócz Podłączanie dane wejściowe, aby dane wyjściowe, umożliwia także mapowania pól do spłaszczenia struktur danych. Aby uzyskać więcej informacji, zobacz [sposób mapowania pól wzbogaconego na indeks z możliwością wyszukiwania](cognitive-search-output-field-mapping.md).

### <a name="sample-request"></a>Przykładowe żądanie

Przed wykonaniem tego wywołania REST pamiętaj o zastąpieniu nazwy usługi i klucza administratora w żądaniu poniżej, jeśli narzędzie nie zachowuje nagłówka żądania między wywołaniami. 

Ponadto podaj nazwę indeksatora. W pozostałej części tego samouczka możesz przywoływać go za pomocą nazwy ```demoindexer```.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Składnia treści żądania

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
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
```

Wyślij żądanie. Internetowe narzędzie do testowania powinno zwrócić kod stanu 201 oznaczający powodzenie przetwarzania. 

Wykonanie tego kroku może potrwać kilka minut. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. Wykonywanie niektórych umiejętności, takich jak analiza obrazu, jest długotrwałe.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie. Aby ponownie uruchomić potok po zmianach kodu lub skryptu, może być konieczne uprzednie usunięcie obiektów. Aby uzyskać więcej informacji, zobacz [Resetowanie i ponowne uruchamianie](#reset).

#### <a name="explore-the-request-body"></a>Eksplorowanie treści żądania

Skrypt ustawia dla elementu ```"maxFailedItems"``` wartość -1, która powoduje, że aparat indeksowania ignoruje błędy podczas importowania danych. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Ponadto zwróć uwagę na instrukcję ```"dataToExtract":"contentAndMetadata"``` w parametrach konfiguracji. Ta instrukcja nakazuje indeksatorowi automatyczne wyodrębnianie zawartości z plików w różnych formatach, a także metadanych związanych z każdym plikiem. 

Gdy zawartość zostanie wyodrębniona, możesz ustawić element ```imageAction```, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Konfiguracja ```"imageAction":"generateNormalizedImages"```, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, wskazuje indeksatorowi, aby wyodrębniał tekst z obrazów (np. wyraz „stop” ze znaku drogowego Stop) i osadzał go jako część pola zawartości. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

## <a name="check-indexer-status"></a>Sprawdzanie stanu indeksatora

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy indeksator jest nadal uruchomiony, wyślij następujące żądanie w celu sprawdzenia stanu indeksatora.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Odpowiedź informuje o tym, czy indeksator działa. Po zakończeniu indeksowania użyj kolejnego żądania HTTP GET wysłanego do punktu końcowego STATUS (jak powyżej), aby wyświetlić raport wszelkich błędów i ostrzeżeń, które wystąpiły podczas wzbogacania.  

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku ostrzeżenia są niegroźne (np. brak tekstowych danych wejściowych dla plików JPEG). Istnieje możliwość przejrzenia odpowiedzi stanu w ramach informacji pełnych dotyczących ostrzeżeń zgłoszonych podczas indeksowania.
 
## <a name="query-your-index"></a>Tworzenie zapytań względem indeksu

Po zakończeniu indeksowania uruchom zapytania zwracające zawartość poszczególnych pól. Domyślnie usługa Azure Search zwraca 50 najlepszych wyników. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [Jak wyświetlać strony wyników w usłudze Azure Search](search-pagination-page-layout.md).

W ramach kroku weryfikacji odpytaj indeks o wszystkie pola.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Dane wyjściowe to schemat indeksu z nazwą, typem i atrybutami każdego pola.

Prześlij drugie zapytanie o element `"*"`, co spowoduje zwrócenie całej zawartości pojedynczego pola, takiego jak `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Powtórz tę procedurę dla pola dodatkowe: zawartość, languageCode, keyPhrases i organizacji, w tym ćwiczeniu. Istnieje możliwość zwrócenia wielu pól za pomocą elementu `$select` używającego listy wartości rozdzielonych przecinkami.

W zależności od złożoności i długości ciągu zapytania można użyć metody GET lub POST. Aby uzyskać więcej informacji, zobacz [Odpytywanie przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>Dostęp do wzbogaconego dokumentu

Funkcja wyszukiwania poznawczego umożliwia wyświetlenie struktury wzbogaconego dokumentu. Wzbogacone dokumenty to tymczasowe struktury tworzone podczas wzbogacania, a następnie usuwane po zakończeniu procesu.

Aby przechwycić migawkę wzbogaconego dokumentu utworzoną podczas indeksowania, dodaj pole o nazwie ```enriched``` do indeksu. Indeksator automatycznie zrzuca do tego pola ciąg będący reprezentacją wszystkich wzbogaceń dokumentu.

Pole ```enriched``` będzie zawierać ciąg, który jest logiczną reprezentacją wzbogaconego dokumentu w pamięci w formacie JSON.  Wartość pola jest jednak prawidłowym dokumentem w formacie JSON. Cudzysłowy są umieszczane w sekwencji ucieczki, więc musisz zastąpić ciąg `\"` znakiem `"`, aby wyświetlić dokument w postaci sformatowanego kodu JSON.  

Pole ```enriched``` jest przeznaczone do debugowania i ma za zadanie ułatwić zrozumienie logicznego kształtu zawartości, względem której wyrażenia są oceniane. Może to być przydatne narzędzie do poznawania i debugowania zestawu umiejętności.

Powtórz poprzednie ćwiczenie, dołączając pole `enriched` w celu przechwycenia zawartości wzbogaconego dokumentu:

### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

Na wczesnych, eksperymentalnych etapach opracowywania potoku najbardziej praktycznym podejściem do iteracji projektowych jest usunięcie obiektów z usługi Azure Search i odbudowanie ich przy użyciu kodu. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Aby zaindeksować dokumenty za pomocą nowych definicji:

1. Usuń indeks, aby usunąć utrwalone dane. Usuń indeksator, aby utworzyć go ponownie w usłudze.
2. Zmodyfikuj zestaw umiejętności i definicję indeksu.
3. Utwórz ponownie indeks i indeksator dla usługi w celu uruchomienia potoku. 

Do usunięcia indeksów, indeksatorów i zestawów umiejętności można użyć portalu.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

W miarę rozwoju kodu można udoskonalić strategię odbudowywania. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia wzbogaconego potoku indeksowania przez utworzenie elementów będących jego składnikami: źródła danych, zestawu umiejętności, indeksu i indeksatora.

Objaśniono [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) wraz z definicją zestawu umiejętności i mechaniką łączenia umiejętności w łańcuch za pomocą danych wejściowych i wyjściowych. Opisano także element `outputFieldMappings` w definicji indeksatora, który jest wymagany do kierowania wzbogaconych wartości z potoku do indeksu z możliwością wyszukiwania w usłudze Azure Search.

Ponadto przedstawiono sposób testowania wyników i resetowania systemu na potrzeby przyszłych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. W tej wersji jest dostępny mechanizm wyświetlania wewnętrznych konstrukcji (wzbogaconych dokumentów utworzonych przez system). Zaprezentowano procedurę sprawdzania stanu indeksatora i usuwania obiektów przed ponownym uruchomieniem potoku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search i usługę Azure Blob Service. Zakładając, że obie te usługi są umieszczone w tej samej grupie, usuń teraz tę grupę zasobów, aby trwale usunąć wszystkie jej elementy, w tym usługi i zapisaną zawartość utworzoną na potrzeby tego samouczka. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd każdej usługi.

## <a name="next-steps"></a>Kolejne kroki

Dostosuj lub rozszerz potok za pomocą umiejętności niestandardowych. Utworzenie umiejętności niestandardowej i dołączenie jej do zestawu umiejętności pozwala na dodanie samodzielnie napisanej analizy tekstu lub obrazu. 

> [!div class="nextstepaction"]
> [Przykład: tworzenie umiejętności niestandardowej](cognitive-search-create-custom-skill-example.md)
