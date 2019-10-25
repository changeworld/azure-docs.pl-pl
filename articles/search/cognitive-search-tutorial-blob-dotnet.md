---
title: C#Samouczek dotyczący wywoływania interfejsy API usług Cognitive Services w potoku wzbogacenia AI
titleSuffix: Azure Cognitive Search
description: Przechodzenie między przykładem wyodrębniania danych, języka naturalnego i przetwarzania obrazu AI na platformie Azure Wyszukiwanie poznawcze potoku indeksowania.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7a8146f524a6e6f9abed2440c98a83aa3878f0c7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790228"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-cognitive-search-indexing-pipeline"></a>C#Samouczek: wywoływanie interfejsy API usług Cognitive Services w potoku indeksowania Wyszukiwanie poznawcze platformy Azure

W ramach tego samouczka nauczysz się Mechanics wzbogacania danych programistycznych na platformie Wyszukiwanie poznawcze Azure, korzystając z *umiejętności poznawczych*. Umiejętności są obsługiwane przez funkcję przetwarzania języka naturalnego (NLP) i możliwości analizy obrazów w Cognitive Services. Za poorednictwem kompozycji i konfiguracji zestawu umiejętności można wyodrębnić tekst i tekst reprezentacje obrazu lub zeskanowanego pliku dokumentu. Możesz również wykryć język, jednostki, kluczowe frazy i inne. Wynik końcowy to rozbudowana dodatkowa zawartość w indeksie wyszukiwania utworzonym przez potok indeksowania oparty na formacie AI.

W tym samouczku użyjesz zestawu SDK platformy .NET do wykonywania następujących zadań:

> [!div class="checklist"]
> * Utworzenie potoku indeksowania, który wzbogaca przykładowe dane w drodze do indeksu
> * Stosowanie wbudowanych umiejętności: optyczne rozpoznawanie znaków, scalanie tekstu, wykrywanie języka, podział tekstu, rozpoznawanie jednostek, wyodrębnianie kluczowych fraz
> * Poznanie sposobu łączenia umiejętności w łańcuch przez mapowanie danych wejściowych na dane wyjściowe w zestawie umiejętności
> * Wykonywanie żądań i przeglądanie wyników
> * Resetowanie indeksu i indeksatorów w celu kontynuowania opracowywania

Wyjście jest indeksem pełnotekstowym z możliwością wyszukiwania tekstu na platformie Azure Wyszukiwanie poznawcze. Indeks możesz rozszerzyć za pomocą innych standardowych możliwości, takich jak [synonimy](search-synonyms.md), [profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analizatory](search-analyzers.md) i [filtry](search-filters.md).

Ten samouczek jest uruchamiany w ramach bezpłatnej usługi, ale liczba bezpłatnych transakcji jest ograniczona do 20 dokumentów dziennie. Jeśli chcesz uruchomić ten samouczek więcej niż raz w tym samym dniu, użyj mniejszego zestawu plików, aby można było zmieścić więcej uruchomień.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy dołączyć Cognitive Services rozliczanego zasobu. Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/) użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://go.microsoft.com/fwlink/?linkid=2042400).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku są używane następujące usługi, narzędzia i dane. 

+ [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych. Upewnij się, że konto magazynu znajduje się w tym samym regionie co usługa Azure Wyszukiwanie poznawcze.

+ [Przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) składają się z małego zestawu plików różnych typów. 

+ [Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) , aby użyć go jako środowiska IDE.

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Aby można było korzystać z usługi Azure Wyszukiwanie poznawcze, wymagany jest adres URL usługi i klucz dostępu. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   ![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

Potok wzbogacania ściąga dane ze źródeł danych platformy Azure. Dane źródłowe muszą pochodzić z obsługiwanego typu źródła danych [indeksatora wyszukiwanie poznawcze platformy Azure](search-indexer-overview.md). Na potrzeby tego ćwiczenia będziemy korzystać z usługi Blob Storage, aby zaprezentować wiele typów zawartości.

1. [Zaloguj się do Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , aby zawierał przykładowe dane. Można ustawić poziom dostępu publicznego na dowolną z jego prawidłowych wartości. W tym samouczku przyjęto założenie, że nazwa kontenera to "podstawowa-demonstracyjna-Data-pr".

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń, aby przekazać [przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Po załadowaniu przykładowych plików uzyskaj nazwę kontenera i parametry połączenia dla usługi Blob Storage. Można to zrobić, przechodząc do konta magazynu w Azure Portal, wybierając pozycję **klucze dostępu**, a następnie skopiować pole **Parametry połączenia** .

   Parametry połączenia powinny być adresem URL podobnym do następującego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Istnieją inne sposoby określania parametrów połączenia, takie jak podanie sygnatury dostępu współdzielonego. Aby dowiedzieć się więcej na temat poświadczeń źródła danych, zobacz [Indeksowanie w usłudze Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Zacznij od otwierania programu Visual Studio i tworzenia nowego projektu aplikacji konsolowego, który można uruchomić w środowisku .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

[Zestaw Azure wyszukiwanie poznawcze .NET SDK](https://aka.ms/search-sdk) składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksami, źródłami danych, indeksatorami i umiejętności, a także przekazywaniem i zarządzaniem dokumentami oraz wykonywanie zapytań, bez konieczności zajmowania się szczegółami HTTP i JSON. Te biblioteki klienckie są dystrybuowane jako pakiety NuGet.

W przypadku tego projektu należy zainstalować wersję 9 pakietu NuGet `Microsoft.Azure.Search` i najnowszy `Microsoft.Extensions.Configuration.Json` pakiet NuGet.

Zainstaluj pakiet NuGet `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów w programie Visual Studio. Aby otworzyć konsolę Menedżera pakietów wybierz kolejno pozycje **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów**. Aby uruchomić polecenie, przejdź do [strony pakietu NuGet Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search), wybierz wersję 9 i skopiuj polecenie Menedżera pakietów. W konsoli Menedżera pakietów Uruchom to polecenie.

Aby zainstalować pakiet NuGet `Microsoft.Extensions.Configuration.Json` w programie Visual Studio, wybierz kolejno pozycje **narzędzia** > **menedżer pakietów NuGet** > **Zarządzanie pakietami NuGet dla rozwiązania.** ... Wybierz pozycję Przeglądaj i Wyszukaj pakiet NuGet `Microsoft.Extensions.Configuration.Json`. Po jego znalezieniu wybierz pakiet, wybierz projekt, potwierdź, że wersja to najnowsza stabilna wersja, a następnie wybierz pozycję Zainstaluj.

## <a name="add-azure-cognitive-search-service-information"></a>Dodawanie informacji o usłudze Wyszukiwanie poznawcze platformy Azure

Aby można było nawiązać połączenie z usługą Azure Wyszukiwanie poznawcze, musisz dodać informacje o usłudze wyszukiwania do projektu. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **dodaj** > **nowy element.** ... Nadaj plikowi nazwę `appsettings.json` a następnie wybierz pozycję **Dodaj**. 

Ten plik musi zostać dołączony do katalogu wyjściowego. Aby to zrobić, kliknij prawym przyciskiem myszy `appsettings.json` i wybierz polecenie **Właściwości**. Zmień wartość w polu **Kopiuj do katalogu wyjściowego** na **kopię nowsze**.

Skopiuj poniższy kod JSON do nowego pliku JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Dodaj usługę wyszukiwania i informacje o koncie usługi BLOB Storage.

Informacje o usłudze wyszukiwania można uzyskać na stronie konta wyszukiwania w Azure Portal. Nazwa konta będzie znajdować się na stronie głównej, a klucze można znaleźć, wybierając pozycję **klucze**.

Parametry połączenia obiektów BLOB można uzyskać, przechodząc do konta magazynu w Azure Portal, wybierając pozycję **klucze dostępu**, a następnie kopiując pole **parametrów połączenia** .

## <a name="add-namespaces"></a>Dodaj przestrzenie nazw

Ten samouczek używa wielu różnych typów z różnych przestrzeni nazw. Aby można było używać tych typów, Dodaj następujące elementy do `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Tworzenie klienta

Utwórz wystąpienie klasy `SearchServiceClient`.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` tworzy nowe `SearchServiceClient` przy użyciu wartości, które są przechowywane w pliku konfiguracyjnym aplikacji (appSettings. JSON).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> Klasa `SearchServiceClient` zarządza połączeniami z usługą wyszukiwania. W celu uniknięcia otwarcia zbyt wielu połączeń, należy, w miarę możliwości, udostępnić pojedyncze wystąpienie klasy `SearchServiceClient` w aplikacji. Metody tej klasy są bezpieczne wątkowo, co pozwala na tego rodzaju udostępnianie.
> 
> 

## <a name="create-a-data-source"></a>Tworzenie źródła danych

Utwórz nowe wystąpienie `DataSource`, wywołując `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` wymaga określenia nazwy źródła danych, parametrów połączenia i nazwy kontenera obiektów BLOB.

Chociaż nie jest on używany w tym samouczku, definiowane są również zasady usuwania nietrwałego, które są używane do identyfikowania usuniętych obiektów BLOB na podstawie wartości kolumny usuwania nietrwałego. Następujące zasady traktują obiekt BLOB, który ma zostać usunięty, jeśli ma właściwość metadanych `IsDeleted` z `true`wartością.

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Teraz, po zainicjowaniu obiektu `DataSource`, Utwórz źródło danych. `SearchServiceClient` ma właściwość `DataSources`. Ta właściwość zawiera wszystkie metody potrzebne do tworzenia, wyświetlania, aktualizowania lub usuwania źródeł danych Azure Wyszukiwanie poznawcze.

W przypadku pomyślnego żądania Metoda zwróci źródło danych, które zostało utworzone. Jeśli wystąpi problem z żądaniem, na przykład nieprawidłowym parametrem, Metoda zgłosi wyjątek.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Ponieważ jest to Twoje pierwsze żądanie, sprawdź Azure Portal, aby potwierdzić, że źródło danych zostało utworzone w usłudze Azure Wyszukiwanie poznawcze. Na stronie pulpitu nawigacyjnego usługi wyszukiwania zweryfikuj, czy kafelek Źródła danych zawiera nowy element. Może trzeba będzie zaczekać kilka minut na odświeżenie strony portalu.

  ![Kafelek źródła danych w portalu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kafelek źródła danych w portalu")

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

W tej sekcji definiujesz zestaw kroków wzbogacania, które chcesz zastosować do danych. Każdy krok wzbogacania jest określany jako *umiejętność* i zestaw kroków wzbogacania *zestawu umiejętności*. W tym samouczku są stosowane [wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) do rozpoznawania tekstu napisanego i odręcznego w plikach obrazów.

+ [Łączenie tekstu](cognitive-search-skill-textmerger.md) do konsolidowania tekstu z kolekcji pól w jedno pole.

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) w celu podziału dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania kluczowych fraz i umiejętności rozpoznawania jednostek. Wyodrębnianie kluczowych fraz i rozpoznawanie jednostek akceptuje dane wejściowe z 50 000 znaków lub mniej. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacji z zawartości kontenera obiektów BLOB.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych.

Podczas wstępnego przetwarzania platforma Azure Wyszukiwanie poznawcze pęknięcia każdego dokumentu w celu odczytania zawartości z różnych formatów plików. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. W związku z tym ustaw dane wejściowe jako ```"/document/content"```, aby użyć tego tekstu. 

Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Umiejętność OCR

Umiejętność **OCR** wyodrębnia tekst z obrazów. Ta umiejętność zakłada, że pole normalized_images istnieje. Aby wygenerować to pole, w dalszej części samouczka ustawimy konfigurację ```"imageAction"``` w definicji indeksatora na ```"generateNormalizedImages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>Scal umiejętność

W tej sekcji utworzysz umiejętność **scalania** , która scala pole zawartości dokumentu z tekstem wyprodukowanym przez umiejętność OCR.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>Umiejętność wykrywania języka

Umiejętność **wykrywanie języka** wykrywa język tekstu wejściowego i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Będziemy używać danych wyjściowych **wykrywanie języka** w ramach danych wejściowych do kwalifikacji **tekstu** .

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>Umiejętność podziału tekstu

Poniższa umiejętność **podziału** podzieli tekst na strony i Ogranicz długość strony do 4 000 znaków, mierzoną przez `String.Length`. Algorytm podejmie próbę podzielenia tekstu na fragmenty o rozmiarze poniżej `maximumPageLength`. W takim przypadku algorytm będzie najlepiej wykonywać zdanie na granicy zdania, dzięki czemu rozmiar fragmentu może być nieco mniejszy niż `maximumPageLength`.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>Umiejętność rozpoznawania jednostek

To wystąpienie `EntityRecognitionSkill` jest ustawione na rozpoznawanie typu kategorii `organization`. Umiejętność **rozpoznawania jednostek** może również rozpoznać typy kategorii `person` i `location`.

Zwróć uwagę, że pole "context" jest ustawione na ```"/document/pages/*"``` przy użyciu gwiazdki, co oznacza, że krok wzbogacania jest wywoływany dla każdej strony w obszarze ```"/document/pages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>Umiejętność wyodrębniania kluczowych fraz

Podobnie jak w przypadku wystąpienia `EntityRecognitionSkill`, które zostało właśnie utworzone, **wyodrębnianie kluczowych fraz** umiejętność jest wywoływana dla każdej strony dokumentu.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "keyPhrases",
    targetName: "keyPhrases"));

KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
    description: "Extract the key phrases",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="build-and-create-the-skillset"></a>Kompilowanie i tworzenie zestawu umiejętności

Kompiluj `Skillset`, korzystając z utworzonych umiejętności.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Utwórz zestawu umiejętności w usłudze wyszukiwania.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Tworzenie indeksu

W tej sekcji zdefiniujesz schemat indeksu przez określenie pól do umieszczenia w indeksie z możliwością wyszukiwania oraz atrybutów wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Utwórz klasę DemoIndex

Pola dla tego indeksu są definiowane przy użyciu klasy modelu. Każda właściwość klasy modelu ma atrybuty, które określają związane z wyszukiwaniem zachowania odpowiedniego pola indeksu. 

Dodamy klasę modelu do nowego C# pliku. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **dodaj** > **nowy element...** , wybierz pozycję "Klasa" i Nazwij plik `DemoIndex.cs`, a następnie wybierz pozycję **Dodaj**.

Upewnij się, że chcesz użyć typów z przestrzeni nazw `Microsoft.Azure.Search` i `Microsoft.Azure.Search.Models`.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Dodaj poniższą definicję klasy modelu do `DemoIndex.cs` i Uwzględnij ją w tej samej przestrzeni nazw, w której utworzysz indeks.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
```

Teraz, po zdefiniowaniu klasy modelu, Wróć do `Program.cs` można stosunkowo łatwo utworzyć definicję indeksu. Nazwa tego indeksu będzie "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Podczas testowania może się okazać, że próbujesz utworzyć indeks więcej niż raz. Z tego powodu Sprawdź, czy indeks, który ma zostać utworzony, już istnieje, zanim spróbujesz go utworzyć.

```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [create index (Azure wyszukiwanie POZNAWCZE REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Tworzenie indeksatora, mapowanie pól i wykonywanie przekształceń

Do tej pory utworzono źródło danych, zestaw umiejętności i indeks. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Aby powiązać je razem w indeksatorze, należy zdefiniować mapowania pól.

+ FieldMappings są przetwarzane przed zestawu umiejętności, mapując pola źródłowe ze źródła danych do pól docelowych w indeksie. Jeśli nazwy pól i typy są takie same na obu końcach, mapowanie nie jest wymagane.

+ OutputFieldMappings są przetwarzane po zestawu umiejętności, do których odwołuje się sourceFieldNames, które nie istnieją do momentu utworzenia przez nich pęknięć lub wzbogacania dokumentów. TargetFieldName to pole w indeksie.

Oprócz podłączania danych wejściowych do danych wyjściowych, można również używać mapowań pól do spłaszczania struktur. Aby uzyskać więcej informacji, zobacz [Jak mapować wzbogacone pola na indeks wyszukiwania](cognitive-search-output-field-mapping.md).

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

Indexer indexer = new Indexer(
    name: "demoindexer",
    dataSourceName: dataSource.Name,
    targetIndexName: index.Name,
    description: "Demo Indexer",
    skillsetName: skillSet.Name,
    parameters: new IndexingParameters(
        maxFailedItems: -1,
        maxFailedItemsPerBatch: -1,
        configuration: config),
    fieldMappings: fieldMappings,
    outputFieldMappings: outputMappings);

try
{
    bool exists = serviceClient.Indexers.Exists(indexer.Name);

    if (exists)
    {
        serviceClient.Indexers.Delete(indexer.Name);
    }

    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

Należy oczekiwać, że tworzenie indeksatora trwa trochę czasu. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. Wykonywanie niektórych umiejętności, takich jak analiza obrazu, jest długotrwałe.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie.

### <a name="explore-creating-the-indexer"></a>Eksplorowanie tworzenia indeksatora

Program Code ustawia ```"maxFailedItems"``` na wartość-1, co powoduje ignorowanie błędów podczas importowania danych przez aparat indeksowania. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Zauważ również, że ```"dataToExtract"``` jest ustawiona na ```"contentAndMetadata"```. Ta instrukcja nakazuje indeksatorowi automatyczne wyodrębnianie zawartości z plików w różnych formatach, a także metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. ```"imageAction"``` ustawiona na ```"generateNormalizedImages"``` konfiguracji, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, instruuje indeksator, aby wyodrębnił tekst z obrazów (na przykład słowo "Stop" ze znaku zatrzymania ruchu) i osadzić je jako część pola zawartość. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

## <a name="check-indexer-status"></a>Sprawdzanie stanu indeksatora

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy indeksator nadal działa, użyj metody `GetStatus`.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
            break;
        case IndexerStatus.Running:
            Console.WriteLine("Indexer is running");
            break;
        case IndexerStatus.Unknown:
            Console.WriteLine("Indexer status is unknown");
            break;
        default:
            Console.WriteLine("No indexer information");
            break;
    }
}
catch (Exception e)
{
    // Handle exception
}
```

`IndexerExecutionInfo` reprezentuje bieżący stan i historię wykonywania indeksatora.

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku ostrzeżenia są niegroźne (np. brak tekstowych danych wejściowych dla plików JPEG).
 
## <a name="query-your-index"></a>Tworzenie zapytań względem indeksu

Po zakończeniu indeksowania można uruchamiać zapytania, które zwracają zawartość poszczególnych pól. Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca górne wyniki 50. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [How to Page Results in Azure wyszukiwanie poznawcze](search-pagination-page-layout.md).

W ramach kroku weryfikacji odpytaj indeks o wszystkie pola.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` tworzy nowe `SearchIndexClient` przy użyciu wartości, które są przechowywane w pliku konfiguracyjnym aplikacji (appSettings. JSON). Należy zauważyć, że klucz interfejsu API zapytania usługi wyszukiwania jest używany, a nie klucz administratora.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Dane wyjściowe to schemat indeksu z nazwą, typem i atrybutami każdego pola.

Prześlij drugie zapytanie o element `"*"`, co spowoduje zwrócenie całej zawartości pojedynczego pola, takiego jak `organizations`.

```csharp
SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

Powtórz te czynności w przypadku dodatkowych pól: Content, languageCode, phrase kluczs i organizacji w tym ćwiczeniu. Istnieje możliwość zwrócenia wielu pól za pomocą elementu `$select` używającego listy wartości rozdzielonych przecinkami.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

Na wczesnych eksperymentalnych etapach tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

W tym samouczku zawarto Sprawdzanie istniejących indeksatorów i indeksów oraz usuwanie ich, jeśli już istnieją, aby można było ponownie uruchomić kod.

Możesz również użyć portalu, aby usunąć indeksy, indeksatory i umiejętności.

W miarę rozwoju kodu można udoskonalić strategię odbudowywania. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia przebudowanego potoku indeksowania przez tworzenie części składników: źródła danych, zestawu umiejętności, indeksu i indeksatora.

Wprowadzono [wbudowane umiejętności](cognitive-search-predefined-skills.md) , wraz z definicją zestawu umiejętności oraz Mechanics umiejętności związanych z łańcuchem, a także za pomocą danych wejściowych i wyjściowych. Nauczysz się również, że `outputFieldMappings` w definicji indeksatora jest wymagana do routingu wzbogaconych wartości z potoku do wyszukiwania w indeksie w usłudze Azure Wyszukiwanie poznawcze.

Ponadto przedstawiono sposób testowania wyników i resetowania systemu na potrzeby przyszłych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. Zaprezentowano procedurę sprawdzania stanu indeksatora i usuwania obiektów przed ponownym uruchomieniem potoku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem oczyszczenia po samouczku jest usunięcie grupy zasobów zawierającej usługę Azure Wyszukiwanie poznawcze i Blob service platformy Azure. Zakładając, że obie te usługi są umieszczone w tej samej grupie, usuń teraz tę grupę zasobów, aby trwale usunąć wszystkie jej elementy, w tym usługi i zapisaną zawartość utworzoną na potrzeby tego samouczka. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd każdej usługi.

## <a name="next-steps"></a>Następne kroki

Dostosuj lub rozszerz potok za pomocą umiejętności niestandardowych. Utworzenie umiejętności niestandardowej i dołączenie jej do zestawu umiejętności pozwala na dodanie samodzielnie napisanej analizy tekstu lub obrazu.

> [!div class="nextstepaction"]
> [Przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md)
