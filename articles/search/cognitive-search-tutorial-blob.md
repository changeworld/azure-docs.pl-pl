---
title: 'Samouczek: REST i sztuczna inteligencja nad obiektami blob platformy Azure'
titleSuffix: Azure Cognitive Search
description: Krok po przykładzie wyodrębniania tekstu i przetwarzania języka naturalnego za pośrednictwem zawartości w magazynie obiektów Blob przy użyciu postman i interfejsów API REST usługi Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 8acafa14afab507b704806056efac0f877a47684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190726"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Samouczek: Generowanie zawartości z przeszukiwania z obiektów blob platformy Azure za pomocą restu i sztucznej inteligencji

Jeśli masz nieustrukturyzowanego tekstu lub obrazów w magazynie obiektów Blob platformy Azure potok [wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md) można wyodrębnić informacje i utworzyć nową zawartość, która jest przydatna w przypadku wyszukiwania pełnotekstowego lub eksploracji wiedzy scenariuszy. Chociaż potok może przetwarzać obrazy, ten samouczek REST koncentruje się na tekście, stosując wykrywanie języka i przetwarzanie języka naturalnego w celu utworzenia nowych pól, które można wykorzystać w kwerendach, aspektach i filtrach.

W tym samouczku użyto interfejsów API postmana i [rest wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zacznij od całych dokumentów (tekstu nieustrukturyzowanego), takich jak PDF, HTML, DOCX i PPTX w magazynie obiektów Blob platformy Azure.
> * Zdefiniuj potok, który wyodrębnia tekst, wykrywa język, rozpoznaje jednostki i wykrywa kluczowe frazy.
> * Zdefiniuj indeks do przechowywania danych wyjściowych (zawartość nieprzetworzona oraz pary nazwa-wartość wygenerowana przez potok).
> * Wykonaj potok, aby rozpocząć transformacje i analizy oraz utworzyć i załadować indeks.
> * Eksploruj wyniki przy użyciu wyszukiwania pełnotekstowego i składni kwerendy rozszerzonej.

Jeśli nie masz subskrypcji platformy Azure, otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)
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

## <a name="2---set-up-postman"></a>2 - Konfigurowanie listonosza

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie znasz tego narzędzia, zobacz [Eksplorowanie interfejsów API rest usługi Azure Cognitive Search przy użyciu usługi Postman](search-get-started-postman.md).

Metody żądania używane w tym samouczku to **POST**, **PUT**i **GET**. Użyjesz tych metod do wykonania czterech wywołań interfejsu API w usłudze wyszukiwania: utworzenia źródła danych, zestawu umiejętności, indeksu i indeksatora.

W nagłówkach ustaw "Typ zawartości" `application/json` `api-key` i ustaw klucz interfejsu api administratora usługi Azure Cognitive Search. Po ustawieniu nagłówków można ich używać do każdego żądania w tym ćwiczeniu.

  ![Adres URL i nagłówek żądania listonosza](media/search-get-started-postman/postman-url.png "Adres URL i nagłówek żądania listonosza")

## <a name="3---create-the-pipeline"></a>3 - Tworzenie potoku

W usłudze Azure Cognitive Search przetwarzanie sztucznej inteligencji występuje podczas indeksowania (lub pozyskiwania danych). Ta część przewodnika tworzy cztery obiekty: źródło danych, definicja indeksu, skillset, indeksator. 

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

[Obiekt źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) udostępnia ciąg połączenia z kontenerem obiektu Blob zawierającym pliki.

1. Użyj **POST** i następującego adresu URL, zastępując YOUR-SERVICE-NAME rzeczywistą nazwą usługi.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. W **treści**żądania skopiuj następującą definicję JSON, zastępując `connectionString` rzeczywiste połączenie konta magazynu. 

   Pamiętaj, aby edytować również nazwę kontenera. Zaproponowaliśmy "cog-search-demo" dla nazwy kontenera we wcześniejszym kroku.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Wyślij żądanie. Powinien zostać wyświetlony kod stanu 201 potwierdzający sukces. 

Jeśli otrzymujesz błąd 403 lub 404, sprawdź, czy żądanie jest poprawnie skonstruowane: w punkcie końcowym powinien znajdować się element `api-version=2019-05-06`, natomiast w nagłówku, po elemencie `Content-Type`, powinien znajdować się element `api-key`, a jego wartość musi być prawidłowa dla usługi wyszukiwania. Można uruchomić dokument JSON za pośrednictwem internetowego walidatora JSON, aby upewnić się, że składnia jest poprawna. 

### <a name="step-2-create-a-skillset"></a>Krok 2: Tworzenie umiejętności

[Obiekt zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) to zestaw kroków wzbogacania zastosowanych do zawartości. 

1. Użyj **PUT** i następującego adresu URL, zastępując YOUR-SERVICE-NAME rzeczywistą nazwą usługi.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. W **treści**żądania, skopiuj definicję JSON poniżej. Ten zestaw umiejętności składa się z następujących wbudowanych umiejętności.

   | Umiejętności                 | Opis    |
   |-----------------------|----------------|
   | [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) | Wyodrębnia nazwy osób, organizacji i lokalizacji z zawartości w kontenerze obiektów blob. |
   | [Wykrywanie języka](cognitive-search-skill-language-detection.md) | Wykrywa język zawartości. |
   | [Dzielenie tekstu](cognitive-search-skill-textsplit.md)  | Dzieli dużą zawartość na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania fraz kluczowych. Umiejętność wyodrębniania fraz kluczowych przyjmuje dane wejściowe składające się maksymalnie z 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie. |
   | [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) | Wyciąga najważniejsze frazy. |

   Każda umiejętność jest wykonywana dla zawartości dokumentu. Podczas przetwarzania usługa Azure Cognitive Search pęka każdy dokument, aby odczytać zawartość z różnych formatów plików. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. W związku z ```"/document/content"```tym dane wejściowe stają się .

   W przypadku wyodrębniania fraz kluczowych, ponieważ używamy umiejętności rozdzielacza tekstu do dzielenia ```"document/pages/*"``` większych plików na strony, ```"/document/content"```kontekstem umiejętności wyodrębniania fraz kluczowych jest (dla każdej strony w dokumencie) zamiast .

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Graficzna reprezentacja zestawu umiejętności jest przedstawiona poniżej. 

    ![Opis zestaw umiejętności](media/cognitive-search-tutorial-blob/skillset.png "Opis zestaw umiejętności")

1. Wyślij żądanie. Listonosz powinien zwrócić kod stanu 201 potwierdzający sukces. 

> [!NOTE]
> Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe. Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: Tworzenie indeksu

[Indeks](https://docs.microsoft.com/rest/api/searchservice/create-index) zawiera schemat używany do tworzenia fizycznego wyrażenia zawartości w odwróconych indeksów i innych konstrukcji w usłudze Azure Cognitive Search. Największym składnikiem indeksu jest zbieranie pól, gdzie typ danych i atrybuty określają zawartość i zachowania w usłudze Azure Cognitive Search.

1. Użyj **PUT** i następującego adresu URL, zastępując YOUR-SERVICE-NAME rzeczywistą nazwą usługi, aby nadać nazwę indeksowi.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. W **treści**żądania skopiuj następującą definicję JSON. W `content` polu jest on akcją dokumentu. Dodatkowe pola `languageCode` `keyPhrases`dla `organizations` programu , i reprezentują nowe informacje (pola i wartości) utworzone przez zestaw umiejętności.

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
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
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
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Wyślij żądanie. Listonosz powinien zwrócić kod stanu 201 potwierdzający sukces. 

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Tworzenie i uruchamianie indeksatora

[Indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer) kieruje potoku. Trzy składniki utworzone do tej pory (źródło danych, skillset, index) są dane wejściowe do indeksatora. Tworzenie indeksatora w usłudze Azure Cognitive Search jest zdarzeniem, które uruchamia cały potok. 

1. Użyj **PUT** i następującego adresu URL, zastępując YOUR-SERVICE-NAME rzeczywistą nazwą usługi, aby nadać nazwę indeksatorowi.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. W **treści**żądania, skopiuj definicję JSON poniżej. Zwróć uwagę na elementy mapowania pól; te mapowania są ważne, ponieważ definiują przepływ danych. 

   Są `fieldMappings` przetwarzane przed zestawem umiejętności, wysyłając zawartość ze źródła danych do pól docelowych w indeksie. Mapowania pól będą używane do wysyłania do indeksu istniejącej, niezmodyfikowanej zawartości. Jeśli nazwy pól i typy są takie same na obu końcach, nie jest wymagane mapowanie.

   Są `outputFieldMappings` dla pól utworzonych przez umiejętności, a tym samym przetwarzane po uruchomieniu skillset. Odwołania do `sourceFieldNames` w `outputFieldMappings` nie istnieją, dopóki dokument pękanie lub wzbogacenie tworzy je. Jest `targetFieldName` to pole w indeksie, zdefiniowane w schemacie indeksu.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
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
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
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
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Wyślij żądanie. Listonosz powinien zwrócić kod stanu 201 potwierdzający pomyślne przetworzenie. 

   Wykonanie tego kroku może potrwać kilka minut. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. 

> [!NOTE]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie. Aby ponownie uruchomić potok po zmianach kodu lub skryptu, może być konieczne uprzednie usunięcie obiektów. Aby uzyskać więcej informacji, zobacz [Resetowanie i ponowne uruchamianie](#reset).

#### <a name="about-indexer-parameters"></a>Informacje o parametrach indeksatora

Skrypt ustawia dla elementu ```"maxFailedItems"``` wartość -1, która powoduje, że aparat indeksowania ignoruje błędy podczas importowania danych. Jest to dopuszczalne, ponieważ istnieje tak mało dokumentów w źródle danych demonstracyjnych. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Instrukcja ```"dataToExtract":"contentAndMetadata"``` informuje indeksatora, aby automatycznie wyodrębnić zawartość z różnych formatów plików, a także metadanych związanych z każdym plikiem. 

Gdy zawartość zostanie wyodrębniona, możesz ustawić element ```imageAction```, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Konfiguracja ```"imageAction":"generateNormalizedImages"```, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, wskazuje indeksatorowi, aby wyodrębniał tekst z obrazów (np. wyraz „stop” ze znaku drogowego Stop) i osadzał go jako część pola zawartości. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

## <a name="4---monitor-indexing"></a>4 - Monitorowanie indeksowania

Indeksowanie i wzbogacanie rozpoczyna się natychmiast po przesłaniu żądania utwórz indeksatora. W zależności od zdefiniowanych umiejętności poznawczych indeksowanie może trochę potrwać. Aby dowiedzieć się, czy indeksator jest nadal uruchomiony, wyślij następujące żądanie w celu sprawdzenia stanu indeksatora.

1. Użyj **GET** i następującego adresu URL, zastępując YOUR-SERVICE-NAME rzeczywistą nazwą usługi, aby nadać nazwę indeksatorowi.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Przejrzyj odpowiedź, aby dowiedzieć się, czy indeksator jest uruchomiony, lub wyświetlić informacje o błędach i ostrzeżeniach.  

Jeśli używasz warstwy Bezpłatna, oczekuje się następującego komunikatu: "Nie można wyodrębnić zawartości lub metadanych z dokumentu. Obcięty wyodrębniony tekst do znaków "32768". Ten komunikat pojawia się, ponieważ indeksowanie obiektów blob w warstwie Bezpłatna ma[limit 32K dotyczący wyodrębniania znaków.](search-limits-quotas-capacity.md#indexer-limits) Ten komunikat nie będzie wyświetlany dla tego zestawu danych w wyższych warstwach. 

> [!NOTE]
> Ostrzeżenia są powszechne w niektórych scenariuszach i nie zawsze wskazują na problem. Na przykład jeśli kontener obiektu blob zawiera pliki obrazów, a potok nie obsługuje obrazów, otrzymasz ostrzeżenie informujące, że obrazy nie zostały przetworzone.

## <a name="5---search"></a>5 - Szukaj

Teraz, gdy utworzono nowe pola i informacje, uruchommy niektóre zapytania, aby zrozumieć wartość wyszukiwania poznawczego, ponieważ odnosi się do typowego scenariusza wyszukiwania.

Przypomnijmy, że zaczęliśmy od zawartości obiektu blob, `content` gdzie cały dokument jest spakowany w jedno pole. Możesz wyszukiwać to pole i znajdować dopasowania do zapytań.

1. Użyj **GET** i następującego adresu URL, zastępując NAZWĘ USŁUGI rzeczywistą nazwą usługi, aby wyszukać wystąpienia `content` terminu lub frazy, zwracając pole i liczbę pasujących dokumentów.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Wyniki tej kwerendy zwracają zawartość dokumentu, który jest taki sam wynik można uzyskać, jeśli używany indeksator obiektów blob bez potoku wyszukiwania poznawczego. To pole można przeszukiwać, ale niewykonalne, jeśli chcesz używać aspektów, filtrów lub autouzupełniania.

   ![Dane wyjściowe pola zawartości](media/cognitive-search-tutorial-blob/content-output.png "Dane wyjściowe pola zawartości")
   
1. W przypadku drugiej kwerendy zwróć niektóre nowe pola utworzone przez potok (osoby, organizacje, lokalizacje, languageCode). Pomijamy keyPhrases dla zwięzłości, ale należy dołączyć go, jeśli chcesz zobaczyć te wartości.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Pola w instrukcji $select zawierają nowe informacje utworzone na podstawie możliwości przetwarzania języka naturalnego usług Cognitive Services. Jak można się spodziewać, istnieje pewien szum w wynikach i różnice między dokumentami, ale w wielu przypadkach modele analityczne dają dokładne wyniki.

   Na poniższej ilustracji przedstawiono wyniki listu otwartego Satyi Nadelli po objęciu stanowiska dyrektora generalnego w firmie Microsoft.

   ![Wyjście rurociągu](media/cognitive-search-tutorial-blob/pipeline-output.png "Wyjście rurociągu")

1. Aby zobaczyć, jak można skorzystać z tych pól, dodaj parametr aspektu, aby zwrócić agregację pasujących dokumentów według lokalizacji.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   W tym przykładzie dla każdej lokalizacji są 2 lub 3 dopasowania.

   ![Wyjście fasetowe](media/cognitive-search-tutorial-blob/facet-output.png "Wyjście fasetowe")
   

1. W tym ostatnim przykładzie zastosuj filtr do kolekcji organizacji, zwracając dwa dopasowania dla kryteriów filtrowania na podstawie nasdaq.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Te kwerendy ilustrują kilka sposobów pracy ze składnią kwerendy i filtrami w nowych polach utworzonych przez wyszukiwanie poznawcze. Aby uzyskać więcej przykładów kwerend, zobacz [Przykłady w interfejsie API REST dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [Przykłady kwerend składni proste](search-query-simple-examples.md)i [przykłady kwerendy pełnej luceny](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalnych rozwoju najbardziej praktyczne podejście do iteracji projektowania jest usunięcie obiektów z usługi Azure Cognitive Search i umożliwić kod, aby je odbudować. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Za pomocą portalu można usuwać indeksy, indeksatory, źródła danych i zestawy umiejętności. Po usunięciu indeksatora można opcjonalnie selektywnie usunąć indeks, zestaw umiejętności i źródło danych w tym samym czasie.

![Usuwanie obiektów wyszukiwania](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Usuwanie obiektów wyszukiwania w portalu")

Możesz też użyć **funkcji DELETE** i udostępnić adresy URL każdemu obiektowi. Następujące polecenie usuwa indeksatora.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia wzbogaconego potoku indeksowania przez utworzenie elementów będących jego składnikami: źródła danych, zestawu umiejętności, indeksu i indeksatora.

[Wprowadzono wbudowane umiejętności,](cognitive-search-predefined-skills.md) wraz z definicją umiejętności i mechaniką łączenia umiejętności poprzez wejścia i wyjścia. Dowiedziałeś się `outputFieldMappings` również, że w definicji indeksatora jest wymagane do routingu wzbogacone wartości z potoku do indeksu z wyszukujalny w usłudze Azure Cognitive Search.

Ponadto przedstawiono sposób testowania wyników i resetowania systemu na potrzeby przyszłych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza Wszystkie zasoby lub Grupy zasobów w lewym okienku nawigacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz wszystkie obiekty w potoku wzbogacania SI, przyjrzyjmy się bliżej definicjom zestawów umiejętności i indywidualnym umiejętnościom.

> [!div class="nextstepaction"]
> [Jak utworzyć zestaw umiejętności](cognitive-search-defining-skillset.md)