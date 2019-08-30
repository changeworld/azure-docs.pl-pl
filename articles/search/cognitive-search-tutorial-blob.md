---
title: 'Samouczek REST: Tworzenie potoku wzbogacania AI przy użyciu wyszukiwania poznawczego — Azure Search'
description: Przechodzenie między przykładem wyodrębniania tekstu i przetwarzania języka naturalnego nad zawartością w obiektach Blob JSON przy użyciu programu Poster i interfejsów API REST Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: bb37c9106149397f50e84b340b1be1189e0de7d1
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186266"
---
# <a name="tutorial-add-structure-to-unstructured-content-with-cognitive-search"></a>Samouczek: Dodawanie struktury do "zawartości bez struktury" przy użyciu wyszukiwania poznawczego

Jeśli masz niestrukturalną zawartość tekstu lub obrazu, funkcja [wyszukiwania poznawczego](cognitive-search-concept-intro.md) Azure Search może pomóc wyodrębnić informacje i utworzyć nową zawartość, która jest przydatna w przypadku wyszukiwania pełnotekstowego lub scenariuszy wyszukiwania w bazie wiedzy. Chociaż wyszukiwanie poznawcze może przetwarzać pliki obrazów (JPG, PNG, TIFF), ten samouczek koncentruje się na zawartości opartej na programie Word, stosowaniu wykrywania języka i analizie tekstu do tworzenia nowych pól i informacji, które można wykorzystać w zapytaniach, aspektach i filtrach.

> [!div class="checklist"]
> * Zacznij od całego dokumentu (tekst bez struktury), takiego jak PDF, MD, DOCX i PPTX, w usłudze Azure Blob Storage.
> * Utwórz potok, który wyodrębnia tekst, wykrywa język, rozpoznaje jednostki i wykrywa kluczowe frazy.
> * Zdefiniuj indeks do przechowywania danych wyjściowych (nieprzetworzona zawartość oraz pary nazwa-wartość w postaci potoku).
> * Wykonaj potok, aby utworzyć i załadować indeks.
> * Eksploruj zawartość przy użyciu wyszukiwania pełnotekstowego i sformatowanej składni zapytań.

Aby ukończyć ten przewodnik, będziesz potrzebować kilku usług oraz [aplikacji klasycznej](https://www.getpostman.com/) programu Poster lub innego narzędzia do testowania sieci Web, aby umożliwić wywoływanie interfejsu API REST. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="download-files"></a>Pobieranie plików

1. Otwórz ten [folder w usłudze OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) i w lewym górnym rogu, kliknij pozycję **Pobierz** , aby skopiować pliki do komputera. 

1. Kliknij prawym przyciskiem myszy plik zip i wybierz polecenie **Wyodrębnij wszystko**. Istnieje 14 plików różnych typów. W tym ćwiczeniu należy użyć 7.

## <a name="1---create-services"></a>1 — Tworzenie usług

W tym instruktażu zastosowano Azure Search indeksowania i zapytań, Cognitive Services do wzbogacania AI i usługi Azure Blob Storage w celu zapewnienia danych. Jeśli to możliwe, Utwórz wszystkie trzy usługi w tym samym regionie i grupie zasobów, aby umożliwić bliskość i łatwość zarządzania. W tym przypadku konto usługi Azure Storage może znajdować się w dowolnym regionie.

### <a name="start-with-azure-storage"></a>Rozpoczynanie pracy z usługą Azure Storage

1. [Zaloguj się do Azure Portal](https://portal.azure.com/) i kliknij pozycję **+ Utwórz zasób**.

1. Wyszukaj *konto magazynu* i wybierz ofertę konta magazynu firmy Microsoft.

   ![Utwórz konto magazynu](media/cognitive-search-tutorial-blob/storage-account.png "Utwórz konto magazynu")

1. Na karcie podstawowe wymagane są następujące elementy. Zaakceptuj wartości domyślne dla wszystkich innych elementów.

   + **Grupa zasobów**. Wybierz istniejący lub Utwórz nowy, ale Użyj tej samej grupy dla wszystkich usług, aby można było zarządzać nimi zbiorczo.

   + **Nazwa konta magazynu**. Jeśli uważasz, że może istnieć wiele zasobów tego samego typu, użyj nazwy, aby odróżnić według typu i regionu, na przykład *blobstoragewestus*. 

   + **Lokalizacja**. Jeśli to możliwe, wybierz tę samą lokalizację używaną dla Azure Search i Cognitive Services. Pojedyncza lokalizacja unieważnia opłaty za przepustowość.

   + **Rodzaj konta**. Wybierz wartość domyślną *StorageV2 (ogólnego przeznaczenia w wersji 2)* .

1. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć usługę.

1. Po jego utworzeniu kliknij pozycję **Przejdź do zasobu** , aby otworzyć stronę przegląd.

1. Kliknij pozycję **obiekty blob** usługa.

1. Kliknij pozycję **+ kontener** , aby utworzyć kontener i nadaj mu nazwę *koło zębate-Search-demonstracyjn*.

1. Wybierz pozycję *koło zębate-Search-demonstracyjny* , a następnie kliknij pozycję **Przekaż** , aby otworzyć folder, w którym zapisano pliki do pobrania. Zaznacz wszystkie pliki niebędące obrazami. Należy mieć 7 plików. Kliknij przycisk **OK** , aby przekazać.

   ![Przekaż pliki przykładowe](media/cognitive-search-tutorial-blob/sample-files.png "Przekaż pliki przykładowe")

1. Przed opuszczeniem usługi Azure Storage należy uzyskać parametry połączenia, aby można było sformułować połączenie w Azure Search. 

   1. Przejdź z powrotem do strony Przegląd Twojego konta magazynu (na przykład użyto *blobstragewestus* ). 
   
   1. W okienku nawigacji po lewej stronie wybierz pozycję **klucze dostępu** i skopiuj jeden z parametrów połączenia. 

   Ciąg połączenia jest adresem URL podobnym do poniższego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Zapisz parametry połączenia w Notatniku. Będzie ona potrzebna później podczas konfigurowania połączenia ze źródłem danych.

### <a name="cognitive-services"></a>Cognitive Services

Wzbogacenie AI w usłudze wyszukiwania poznawczego jest obsługiwane przez Cognitive Services, w tym analiza tekstu i przetwarzanie obrazów na potrzeby przetwarzania języka naturalnego i obrazu. Jeśli celem było ukończenie rzeczywistego prototypu lub projektu, w tym momencie należy udostępnić Cognitive Services (w tym samym regionie co Azure Search), tak aby można było dołączyć go do operacji indeksowania.

W tym ćwiczeniu można jednak pominąć Inicjowanie obsługi zasobów, ponieważ Azure Search może nawiązać połączenie Cognitive Services w tle i zapewni 20 bezpłatnych transakcji na uruchomienie indeksatora. Ponieważ w tym samouczku są stosowane 7 transakcji, wystarczające jest bezpłatne przydzielanie. W przypadku większych projektów Zaplanuj obsługę Cognitive Services w warstwie płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [Attach Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-search"></a>Azure Search

Trzeci składnik jest Azure Search, który można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna. 

Tak jak w przypadku usługi Azure Blob Storage, poświęć chwilę na zebranie klucza dostępu. Ponadto po rozpoczęciu tworzenia struktury żądań należy podać punkt końcowy i klucz interfejsu API administratora używany do uwierzytelniania każdego żądania.

### <a name="get-an-admin-api-key-and-url-for-azure-search"></a>Pobierz klucz API i adres URL administratora dla Azure Search

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Pobierz nazwę usługi wyszukiwania. Nazwę usługi można potwierdzić, przeglądając adres URL punktu końcowego. Jeśli adres URL `https://mydemo.search.windows.net`punktu końcowego to, nazwa usługi to `mydemo`.

2. W obszarze **Ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

    Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

![Pobieranie nazwy usługi i administratora oraz kluczy zapytań](media/search-get-started-nodejs/service-name-and-keys.png)

Wszystkie żądania wymagają klucza API-Key w nagłówku każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia zaufanie dla każdego żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="2---set-up-postman"></a>2 — Konfigurowanie wpisu

Uruchom narzędzie Postman i skonfiguruj żądanie HTTP. Jeśli nie znasz tego narzędzia, zobacz [eksplorowanie Azure Search interfejsów API REST przy użyciu programu Poster](search-get-started-postman.md).

Metody żądań używane w tym samouczku to **post**, **Put**i **Get**. Użyjesz metod, aby wykonać cztery wywołania interfejsu API do usługi wyszukiwania: tworzenie źródła danych, zestawu umiejętności, indeksu i indeksatora.

W obszarze nagłówki ustaw wartość "Content-Type" `application/json` na i `api-key` ustaw wartość na klucz API-Key administratora usługi Azure Search. Po ustawieniu nagłówków można używać ich dla każdego żądania w tym ćwiczeniu.

  ![Adres URL i nagłówek żądania post](media/search-get-started-postman/postman-url.png "Adres URL i nagłówek żądania post")

## <a name="3---create-the-pipeline"></a>3 — Tworzenie potoku

W Azure Search przetwarzanie AI odbywa się podczas indeksowania (lub pozyskiwania danych). W tej części przewodnika utworzono cztery obiekty: Źródło danych, definicja indeksu, zestawu umiejętności, indeksator. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Utwórz źródło danych

[Obiekt źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) dostarcza parametry połączenia z kontenerem obiektów BLOB zawierającym pliki.

1. Użyj **wpisu** i poniższego adresu URL, ZASTĘPUJĄC nazwę usługi nazwą rzeczywistą nazwą usługi.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. W **treści**żądania Skopiuj następującą definicję JSON, zastępując ją `connectionString` rzeczywistym połączeniem konta magazynu. 

   Pamiętaj, aby również edytować nazwę kontenera. Sugerujemy "koło zębate-Search-demonstracyjn" dla nazwy kontenera we wcześniejszym kroku.

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
1. Wyślij żądanie. Powinien zostać wyświetlony kod stanu 201 potwierdzenie sukcesu. 

Jeśli otrzymujesz błąd 403 lub 404, sprawdź, czy żądanie jest poprawnie skonstruowane: w punkcie końcowym powinien znajdować się element `api-version=2019-05-06`, natomiast w nagłówku, po elemencie `Content-Type`, powinien znajdować się element `api-key`, a jego wartość musi być prawidłowa dla usługi wyszukiwania. Aby upewnić się, że składnia jest poprawna, możesz chcieć uruchomić dokument JSON za pomocą modułu sprawdzania JSON w trybie online. 

### <a name="step-2-create-a-skillset"></a>Krok 2: Tworzenie zestawu umiejętności

[Obiekt zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) to zestaw kroków wzbogacania zastosowanych do zawartości. 

1. Użyj wartości **Put** i wpisz następujący adres URL, ZASTĘPUJĄC nazwę usługi nazwą rzeczywistą nazwą usługi.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. W **treści**żądania Skopiuj poniżej definicję JSON. Ten zestawu umiejętności składa się z następujących wbudowanych umiejętności.

   | Czy                 | Opis    |
   |-----------------------|----------------|
   | [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) | Wyodrębnia nazwy osób, organizacji i lokalizacji z zawartości w kontenerze obiektów BLOB. |
   | [Wykrywanie języka](cognitive-search-skill-language-detection.md) | Wykrywa język zawartości. |
   | [Podział tekstu](cognitive-search-skill-textsplit.md)  | Dzieli dużą zawartość na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania kluczowych fraz. Umiejętność wyodrębniania fraz kluczowych przyjmuje dane wejściowe składające się maksymalnie z 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie. |
   | [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) | Pobiera najważniejsze frazy. |

   Każda umiejętność jest wykonywana dla zawartości dokumentu. Podczas przetwarzania usługa Azure Search analizuje każdy dokument w celu odczytania zawartości plików w różnych formatach. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. W związku z tym dane wejściowe ```"/document/content"```staną się takie same.

   W celu wyodrębnienia frazy kluczowej, ponieważ używamy umiejętności rozdzielacza tekstu do dzielenia większych plików na strony, kontekst dla umiejętności wyodrębnienia ```"document/pages/*"``` frazy klucza to (dla każdej strony w dokumencie ```"/document/content"```) zamiast.

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

    ![Poznawanie zestawu umiejętności](media/cognitive-search-tutorial-blob/skillset.png "Poznawanie zestawu umiejętności")

1. Wyślij żądanie. Po potwierdzeniu pomyślnego zakończyła się kodem stanu 201. 

> [!NOTE]
> Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe. Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: Tworzenie indeksu

[Indeks](https://docs.microsoft.com/rest/api/searchservice/create-index) zawiera schemat używany do tworzenia fizycznego wyrażenia zawartości w odwróconych indeksach i innych konstrukcjach w Azure Search. Największym składnikiem indeksu jest kolekcja Fields, w której typ danych i atrybuty określają zawartość i zachowania w Azure Search.

1. Użyj wartości **Put** i wpisz następujący adres URL, ZASTĘPUJĄC nazwę usługi nazwą z rzeczywistą nazwą swojej usługi, aby nazwać indeks.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. W **treści**żądania Skopiuj następującą definicję JSON. W `content` polu jest przechowywany sam dokument. Dodatkowe pola dla `languageCode`, `keyPhrases`i `organizations` reprezentujące nowe informacje (pola i wartości) utworzone przez zestawu umiejętności.

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

1. Wyślij żądanie. Po potwierdzeniu pomyślnego zakończyła się kodem stanu 201. 

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Utwórz i uruchom indeksator

[Indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer) steruje potokiem. Trzy składniki, które zostały utworzone w tym przypadku (Źródło danych, zestawu umiejętności, indeks), są danymi wejściowymi indeksatora. Tworzenie indeksatora na Azure Search jest zdarzeniem, które umieszcza cały potok w ruchu. 

1. Użyj wartości **Put** i wpisz następujący adres URL, ZASTĘPUJĄC nazwę usługi nazwą z rzeczywistą nazwą usługi, aby nawiązać nazwę indeksatora.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. W **treści**żądania Skopiuj poniżej definicję JSON. Zwróć uwagę na elementy mapowania pól; te mapowania są ważne, ponieważ definiują przepływ danych. 

   `fieldMappings` Są przetwarzane przed zestawu umiejętności, wysyłając zawartość ze źródła danych do pól docelowych w indeksie. Mapowania pól są używane do wysyłania istniejącej, niezmodyfikowanej zawartości do indeksu. Jeśli nazwy pól i typy są takie same na obu końcach, mapowanie nie jest wymagane.

   `outputFieldMappings` Są dla pól utworzonych przez umiejętności i w ten sposób przetwarzane po zestawu umiejętności. Odwołania do `sourceFieldNames` programu w `outputFieldMappings` programie nie istnieją do momentu, w którym są tworzone lub wzbogacanie dokumentów. `targetFieldName` To pole w indeksie zdefiniowane w schemacie indeksu.

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

1. Wyślij żądanie. Po pomyślnym przetworzeniu należy zwrócić kod stanu 201. 

   Wykonanie tego kroku może potrwać kilka minut. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. 

> [!NOTE]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie. Aby ponownie uruchomić potok po zmianach kodu lub skryptu, może być konieczne uprzednie usunięcie obiektów. Aby uzyskać więcej informacji, zobacz [Resetowanie i ponowne uruchamianie](#reset).

#### <a name="about-indexer-parameters"></a>Informacje o parametrach indeksatora

Skrypt ustawia dla elementu ```"maxFailedItems"``` wartość -1, która powoduje, że aparat indeksowania ignoruje błędy podczas importowania danych. Jest to możliwe tylko w przypadku, gdy w źródle danych demonstracyjnych istnieje kilka dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

```"dataToExtract":"contentAndMetadata"``` Instrukcja nakazuje Indeksatorowi automatyczne Wyodrębnienie zawartości z różnych formatów plików, a także metadanych związanych z każdym plikiem. 

Gdy zawartość zostanie wyodrębniona, możesz ustawić element ```imageAction```, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Konfiguracja ```"imageAction":"generateNormalizedImages"```, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, wskazuje indeksatorowi, aby wyodrębniał tekst z obrazów (np. wyraz „stop” ze znaku drogowego Stop) i osadzał go jako część pola zawartości. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

## <a name="4---monitor-indexing"></a>4 — monitorowanie indeksowania

Indeksowanie i wzbogacenie rozpoczyna się zaraz po przesłaniu żądania utworzenia indeksatora. Indeksowanie może chwilę potrwać, w zależności od zdefiniowanych umiejętności poznawczych. Aby dowiedzieć się, czy indeksator jest nadal uruchomiony, wyślij następujące żądanie w celu sprawdzenia stanu indeksatora.

1. Użyj pozycji **Pobierz** i następujący adres URL, ZASTĘPUJĄC nazwę usługi nazwą z rzeczywistą nazwą usługi, aby nawiązać nazwę indeksatora.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Zapoznaj się z odpowiedzią, aby dowiedzieć się, czy indeksator jest uruchomiony, lub Wyświetl informacje o błędach i ostrzeżeniach.  

W przypadku korzystania z warstwy Bezpłatna jest oczekiwany następujący komunikat: "" nie można wyodrębnić zawartości lub metadanych z dokumentu. Obcięto wyodrębniony tekst do znaków "32768". Ten komunikat jest wyświetlany, ponieważ indeksowanie obiektów BLOB w warstwie Bezpłatna ma[limit 32 KB na ekstrakcji znaków](search-limits-quotas-capacity.md#indexer-limits). Ten komunikat dla tego zestawu danych nie zostanie wyświetlony w wyższych warstwach. 

> [!NOTE]
> Ostrzeżenia są typowe w niektórych scenariuszach i nie zawsze wskazują problem. Na przykład jeśli kontener obiektów BLOB zawiera pliki obrazów, a potok nie obsługuje obrazów, pojawi się ostrzeżenie z informacją o tym, że obrazy nie zostały przetworzone.

## <a name="5---search"></a>5 — wyszukiwanie

Teraz, gdy zostały utworzone nowe pola i informacje, Uruchommy kilka zapytań, aby poznać wartość wyszukiwania poznawczego w miarę odnoszącym się do typowego scenariusza wyszukiwania.

Odwołaj się do zawartości obiektu BLOB, gdzie cały dokument jest spakowany w jednym `content` polu. Możesz przeszukać to pole i znaleźć dopasowania do zapytań.

1. Użyj instrukcji **Get** i następującego adresu URL, zastępując nazwę usługi nazwą rzeczywistą nazwą usługi, aby wyszukać wystąpienia terminu lub frazy, zwracając wartość `content` pola i liczbę pasujących dokumentów.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Wyniki tej kwerendy zwracają zawartość dokumentu, która jest taka sama, jak w przypadku użycia indeksatora BLOB bez potoku wyszukiwania poznawczego. To pole jest możliwe do przeszukiwania, ale nie do pracy, jeśli chcesz użyć aspektów, filtrów lub autouzupełniania.

   ![Dane wyjściowe pola zawartości](media/cognitive-search-tutorial-blob/content-output.png "Dane wyjściowe pola zawartości")
   
1. W przypadku drugiego zapytania Zwróć kilka nowych pól utworzonych przez potok (osoby, organizacje, lokalizacje, languageCode). Pomijamy frazy kluczowe dla zwięzłości, ale należy je uwzględnić, jeśli chcesz zobaczyć te wartości.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Pola w instrukcji $select zawierają nowe informacje utworzone na podstawie możliwości przetwarzania języka naturalnego Cognitive Services. W zależności od tego, czy istnieją pewne zakłócenia w wynikach i zmianach w różnych dokumentach, ale w wielu przypadkach modele analityczne tworzą dokładne wyniki.

   Na poniższej ilustracji przedstawiono wyniki dla otwartego listu programu Satya Nadella, po założeniu, że rola dyrektor naczelny firmy Microsoft.

   ![Dane wyjściowe potoku](media/cognitive-search-tutorial-blob/pipeline-output.png "Dane wyjściowe potoku")

1. Aby zobaczyć, jak można korzystać z tych pól, Dodaj parametr facet, aby zwrócić agregację pasujących dokumentów według lokalizacji.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   W tym przykładzie dla każdej lokalizacji istnieją 2 lub 3 dopasowania.

   ![Dane wyjściowe aspektu](media/cognitive-search-tutorial-blob/facet-output.png "Dane wyjściowe aspektu")
   

1. W tym ostatnim przykładzie Zastosuj filtr w kolekcji organizacje, zwracając dwa dopasowania dla kryteriów filtrowania opartych na NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Te zapytania ilustrują kilka sposobów pracy z składnią zapytania i filtrami w nowych polach utworzonych przez wyszukiwanie poznawcze. Aby uzyskać więcej przykładów dotyczących zapytań, zobacz [przykłady w temacie Search Documents API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [proste zapytania składniowe](search-query-simple-examples.md)i [pełne przykłady zapytań](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

Na wczesnych, eksperymentalnych etapach opracowywania potoku najbardziej praktycznym podejściem do iteracji projektowych jest usunięcie obiektów z usługi Azure Search i odbudowanie ich przy użyciu kodu. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Aby zaindeksować dokumenty za pomocą nowych definicji:

1. Usuń indeksator, indeks i zestawu umiejętności.
2. Modyfikuj obiekty.
3. Utwórz ponownie w usłudze, aby uruchomić potok. 

Możesz użyć portalu, aby usunąć indeksy, indeksatory i umiejętności, lub użyć **Usuń** i podaj adresy URL do każdego obiektu. Następujące polecenie usuwa indeksator.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

W przypadku pomyślnego usunięcia jest zwracany kod stanu 204.

W miarę rozwoju kodu można udoskonalić strategię odbudowywania. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia wzbogaconego potoku indeksowania przez utworzenie elementów będących jego składnikami: źródła danych, zestawu umiejętności, indeksu i indeksatora.

Objaśniono [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) wraz z definicją zestawu umiejętności i mechaniką łączenia umiejętności w łańcuch za pomocą danych wejściowych i wyjściowych. Opisano także element `outputFieldMappings` w definicji indeksatora, który jest wymagany do kierowania wzbogaconych wartości z potoku do indeksu z możliwością wyszukiwania w usłudze Azure Search.

Ponadto przedstawiono sposób testowania wyników i resetowania systemu na potrzeby przyszłych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search i usługę Azure Blob Service. Zakładając, że obie te usługi są umieszczone w tej samej grupie, usuń teraz tę grupę zasobów, aby trwale usunąć wszystkie jej elementy, w tym usługi i zapisaną zawartość utworzoną na potrzeby tego samouczka. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd każdej usługi.

## <a name="next-steps"></a>Następne kroki

Dostosuj lub rozszerz potok za pomocą umiejętności niestandardowych. Utworzenie umiejętności niestandardowej i dołączenie jej do zestawu umiejętności pozwala na dodanie samodzielnie napisanej analizy tekstu lub obrazu. 

> [!div class="nextstepaction"]
> [Przykład: Tworzenie niestandardowej umiejętności wyszukiwania poznawczego](cognitive-search-create-custom-skill-example.md)