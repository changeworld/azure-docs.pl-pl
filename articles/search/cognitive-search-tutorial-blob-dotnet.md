---
title: 'Samouczek: C# i AI nad obiektami blob platformy Azure'
titleSuffix: Azure Cognitive Search
description: Krok po przykładzie wyodrębniania tekstu i przetwarzania języka naturalnego za pośrednictwem zawartości w magazynie obiektów Blob przy użyciu języka C# i azure cognitive search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 169a33d12e98235dcb4e4f317dbb8d91eb7446a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851139"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Samouczek: Generowanie zawartości z przeszukiwaniem z obiektów blob platformy Azure za pomocą języka C# i AI

Jeśli masz nieustrukturyzowanego tekstu lub obrazów w magazynie obiektów Blob platformy Azure potok [wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md) można wyodrębnić informacje i utworzyć nową zawartość, która jest przydatna w przypadku wyszukiwania pełnotekstowego lub eksploracji wiedzy scenariuszy. W tym samouczku języka C# zastosuj optyczne rozpoznawanie znaków (OCR) na obrazach i wykonaj przetwarzanie języka naturalnego, aby utworzyć nowe pola, które można wykorzystać w kwerendach, aspektach i filtrach.

W tym samouczku użyto języka C# i [zestawu .NET SDK](https://aka.ms/search-sdk) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zacznij od plików aplikacji i obrazów w magazynie obiektów blob platformy Azure.
> * Zdefiniuj potok, aby dodać OCR, wyodrębnianie tekstu, wykrywanie języka, rozpoznawanie encji i fraz kluczowych.
> * Zdefiniuj indeks do przechowywania danych wyjściowych (zawartość nieprzetworzona oraz pary nazwa-wartość wygenerowana przez potok).
> * Wykonaj potok, aby rozpocząć transformacje i analizy oraz utworzyć i załadować indeks.
> * Eksploruj wyniki przy użyciu wyszukiwania pełnotekstowego i składni kwerendy rozszerzonej.

Jeśli nie masz subskrypcji platformy Azure, otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Tworzenie](search-create-service-portal.md) lub [znajdowanie istniejącej usługi wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby zaakceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

1. Otwórz ten [folder usługi OneDrive,](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a w lewym górnym rogu kliknij pozycję **Pobierz,** aby skopiować pliki na komputer. 

1. Kliknij prawym przyciskiem myszy plik zip i wybierz polecenie **Wyodrębnij wszystkie**. Istnieje 14 plików różnych typów. Użyj ich wszystkich w tym samouczku.

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

1. Kliknij **+ Kontener,** aby utworzyć kontener i nazwać go *basic-demo-data-pr*.

1. Wybierz *opcję Basic-demo-data-pr,* a następnie kliknij przycisk **Przekaż,** aby otworzyć folder, w którym zostały zapisane pliki do pobrania. Zaznacz wszystkie czternaście plików i kliknij przycisk **OK,** aby je przesłać.

   ![Przekazywanie przykładowych plików](media/cognitive-search-quickstart-blob/sample-data.png "Przekazywanie przykładowych plików")

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

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz api administratora i adres URL usługi Azure Cognitive Search

Do interakcji z usługą Azure Cognitive Search trzeba będzie adres URL usługi i klucz dostępu. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

   Pobierz klucz zapytania, jak również. Jest najlepszym rozwiązaniem do wystawiania żądań zapytań z dostępem tylko do odczytu.

   ![Pobierz nazwę usługi i klucze administratora i zapytania](media/search-get-started-nodejs/service-name-and-keys.png)

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-your-environment"></a>2 - Skonfiguruj środowisko

Rozpocznij od otwarcia programu Visual Studio i utworzenia nowego projektu aplikacji konsoli, który można uruchomić w programie .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

[Zestaw SDK usługi Azure Cognitive Search .NET](https://aka.ms/search-sdk) składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksami, źródłami danych, indeksatorami i zestawami umiejętności, a także przekazywanie dokumentów i wykonywanie zapytań oraz zarządzanie nimi, a wszystko to bez konieczności zajmowania się szczegółami protokołu HTTP i JSON. Wszystkie te biblioteki klienckie są dystrybuowane jako pakiety NuGet.

W tym projekcie zainstaluj wersję 9 `Microsoft.Azure.Search` lub nowszą pakietu NuGet.

1. Otwórz konsolę Menedżera pakietów. Wybierz **opcję Konsola** > Menedżera**pakietów Menedżera** > **pakietów**Narzędzia NuGet . 

1. Przejdź do [strony pakietu Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Wybierz najnowszą wersję (9 lub nowszą).

1. Skopiuj polecenie Menedżer pakietów.

1. Wróć do konsoli Menedżera pakietów i uruchom polecenie skopiowane w poprzednim kroku.

Następnie zainstaluj najnowszy `Microsoft.Extensions.Configuration.Json` pakiet NuGet.

1. Wybierz **narzędzia** > **NuGet Package Manager** > **Zarządzaj pakietami NuGet dla rozwiązania...**. 

1. Kliknij **przycisk Przeglądaj** `Microsoft.Extensions.Configuration.Json` i wyszukaj pakiet NuGet. 

1. Wybierz pakiet, wybierz projekt, potwierdź, że wersja jest najnowszą stabilną wersją, a następnie kliknij przycisk **Zainstaluj**.

### <a name="add-service-connection-information"></a>Dodawanie informacji o połączeniu usługi

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Dodaj** > **nowy element...** . 

1. Nazwij `appsettings.json` plik i wybierz pozycję **Dodaj**. 

1. Dołącz ten plik do katalogu wyjściowego.
    1. Kliknij prawym `appsettings.json` przyciskiem myszy i wybierz polecenie **Właściwości**. 
    1. Zmień wartość **kopiuj na Katalog wyjściowy** na **Kopiuj, jeśli jest nowszy**.

1. Skopiuj poniższy plik JSON do nowego pliku JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Dodaj informacje o koncie usługi wyszukiwania i magazynu obiektów blob. Przypomnijmy, że te informacje można uzyskać z kroków inicjowania obsługi administracyjnej usługi wskazanych w poprzedniej sekcji.

### <a name="add-namespaces"></a>Dodawanie obszarów nazw

W `Program.cs`obszarze dodaj następujące przestrzenie nazw.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Tworzenie klienta

Utwórz wystąpienie `SearchServiceClient` klasy `Main`w obszarze .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`tworzy nowy `SearchServiceClient` przy użyciu wartości, które są przechowywane w pliku konfiguracyjnym aplikacji (appsettings.json).

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

### <a name="add-function-to-exit-the-program-during-failure"></a>Dodaj funkcję, aby zamknąć program podczas awarii

Ten samouczek ma na celu pomóc zrozumieć każdy krok potoku indeksowania. Jeśli istnieje krytyczny problem, który uniemożliwia programowi tworzenie źródła danych, skillset, indeks lub indeksatora program będzie wyprowadzać komunikat o błędzie i wyjść, tak aby problem można było zrozumieć i rozwiązać.

Dodaj `ExitProgram` `Main` do obsługi scenariuszy, które wymagają programu do zakończenia.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - Tworzenie potoku

W usłudze Azure Cognitive Search przetwarzanie sztucznej inteligencji występuje podczas indeksowania (lub pozyskiwania danych). Ta część przewodnika tworzy cztery obiekty: źródło danych, definicja indeksu, skillset, indeksator. 

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

`SearchServiceClient` ma właściwość `DataSources`. Ta właściwość zawiera wszystkie metody potrzebne do tworzenia, listy, aktualizacji lub usuwania źródeł danych usługi Azure Cognitive Search.

Utwórz `DataSource` nowe wystąpienie, wywołując program `serviceClient.DataSources.CreateOrUpdate(dataSource)`. `DataSource.AzureBlobStorage`wymaga określenia nazwy źródła danych, parametry połączenia i nazwy kontenera obiektów blob.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

W przypadku pomyślnego żądania metoda zwróci źródło danych, które zostało utworzone. Jeśli występuje problem z żądaniem, takich jak nieprawidłowy parametr, metoda zgłosić wyjątek.

Teraz dodaj `Main` wiersz, aby `CreateOrUpdateDataSource` wywołać funkcję, którą właśnie dodałeś.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
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

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Skompiluj i uruchom rozwiązanie. Ponieważ jest to twoje pierwsze żądanie, sprawdź witrynę Azure portal, aby potwierdzić, że źródło danych zostało utworzone w usłudze Azure Cognitive Search. Na stronie pulpitu nawigacyjnego usługi wyszukiwania zweryfikuj, czy kafelek Źródła danych zawiera nowy element. Może trzeba będzie zaczekać kilka minut na odświeżenie strony portalu.

  ![Kafelek źródeł danych w portalu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kafelek źródeł danych w portalu")

### <a name="step-2-create-a-skillset"></a>Krok 2: Tworzenie umiejętności

W tej sekcji można zdefiniować zestaw kroków wzbogacania, które chcesz zastosować do danych. Każdy krok wzbogacenia nazywa się *umiejętnością,* a zestaw kroków wzbogacenia *jest zestawem umiejętności.* W tym samouczku użyto [wbudowanych umiejętności poznawczych](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) do rozpoznawania drukowanego i odręcznego tekstu w plikach obrazów.

+ [Połączenie tekstu w](cognitive-search-skill-textmerger.md) celu skonsolidowania tekstu z kolekcji pól w jedno pole.

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Tekst Split,](cognitive-search-skill-textsplit.md) aby podzielić dużą zawartość na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania fraz kluczowych i umiejętności rozpoznawania jednostek. Wyodrębnianie fraz kluczowych i rozpoznawanie encji akceptują dane wejściowe o wartości co najmniej 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacji z zawartości w kontenerze obiektów blob.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych.

Podczas wstępnego przetwarzania usługa Azure Cognitive Search pęka każdy dokument, aby odczytać zawartość z różnych formatów plików. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. W związku z tym ```"/document/content"``` należy ustawić dane wejściowe, aby użyć tego tekstu. 

Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Umiejętność OCR

Umiejętność **OCR** wyodrębnia tekst z obrazów. Ta umiejętność zakłada, że istnieje pole normalized_images. Aby wygenerować to pole, w ```"imageAction"``` dalszej części samouczka ```"generateNormalizedImages"```ustawimy konfigurację w definicji indeksatora na .

```csharp
private static OcrSkill CreateOcrSkill()
{
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

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Scalanie umiejętności

W tej sekcji utworzysz **umiejętność Scalanie,** która scala pole zawartości dokumentu z tekstem utworzonym przez umiejętność OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
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

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Umiejętność wykrywania języka

**Umiejętność wykrywania języka** wykrywa język tekstu wejściowego i zgłasza jeden kod języka dla każdego dokumentu przesłanego na żądanie. Użyjemy danych wyjściowych umiejętności **Wykrywanie języka** jako część wprowadzania do **umiejętności Podział tekstu.**

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
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

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Umiejętność podziału tekstu

Poniższa umiejętność **Podziału** dzieli tekst według stron i ogranicza długość strony do `String.Length`4000 znaków mierzonych za pomocą . Algorytm spróbuje podzielić tekst na fragmenty, `maximumPageLength` które mają co najwyżej rozmiar. W takim przypadku algorytm dołoży wszelkich starań, aby złamać zdanie na granicy zdania, więc `maximumPageLength`rozmiar fragmentu może być nieco mniejszy niż .

```csharp
private static SplitSkill CreateSplitSkill()
{
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

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Umiejętność rozpoznawania jednostek

To `EntityRecognitionSkill` wystąpienie jest ustawione `organization`na rozpoznawanie typu kategorii . Umiejętność **Rozpoznawanie jednostek** może `person` `location`również rozpoznawać typy kategorii i .

Należy zauważyć, że pole "kontekst" jest ustawione ```"/document/pages/*"``` z gwiazdką, co oznacza, ```"/document/pages"```że krok wzbogacania jest wywoływany dla każdej strony w obszarze .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
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

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Umiejętność ekstrakcji fraz kluczowych

Podobnie `EntityRecognitionSkill` jak wystąpienie, które zostało właśnie utworzone, **klucz wyrażenie wyodrębnianie** umiejętności jest wywoływana dla każdej strony dokumentu.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
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

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Tworzenie i tworzenie umiejętności

Twórz `Skillset` umiejętności, które stworzyłeś.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Dodaj następujące wiersze do . `Main`

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Krok 3: Tworzenie indeksu

W tej sekcji zdefiniujesz schemat indeksu przez określenie pól do umieszczenia w indeksie z możliwością wyszukiwania oraz atrybutów wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Tworzenie klasy DemoIndex

Pola dla tego indeksu są definiowane przy użyciu klasy modelu. Każda właściwość klasy modelu ma atrybuty, które określają związane z wyszukiwaniem zachowania odpowiedniego pola indeksu. 

Dodamy klasę modelu do nowego pliku języka C#. Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj** > nowy `DemoIndex.cs`**element...** wybierz "Klasa" i nazwij plik , a następnie wybierz pozycję **Dodaj**.

Upewnij się, że chcesz używać typów `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` z i obszarów nazw.

Dodaj poniższą definicję `DemoIndex.cs` klasy modelu i dołącz ją w tej samej przestrzeni nazw, w której utworzysz indeks.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
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
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

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
``` -->

Po zdefiniowaniu klasy modelu można `Program.cs` dość łatwo utworzyć definicję indeksu. Nazwa tego indeksu `demoindex`będzie . Jeśli indeks już istnieje o tej nazwie, zostanie usunięty.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

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
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Podczas testowania może się okazać, że próbujesz utworzyć indeks więcej niż jeden raz. Z tego powodu sprawdź, czy indeks, który zamierzasz utworzyć, już istnieje przed podjęciem próby jego utworzenia.

Dodaj następujące wiersze do . `Main`

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
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
 -->

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [Tworzenie indeksu (interfejsu API REST usługi Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Tworzenie i uruchamianie indeksatora

Do tej pory utworzono źródło danych, zestaw umiejętności i indeks. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Aby powiązać je razem w indeksatorze, należy zdefiniować mapowania pól.

+ FieldMappings są przetwarzane przed zestawem umiejętności, mapowanie pól źródłowych ze źródła danych do pól docelowych w indeksie. Jeśli nazwy pól i typy są takie same na obu końcach, nie jest wymagane mapowanie.

+ OutputFieldMappings są przetwarzane po skillset, odwołując się sourceFieldNames, które nie istnieją, dopóki dokument pękanie lub wzbogacanie tworzy je. TargetFieldName jest polem w indeksie.

Oprócz podłączania danych wejściowych do wyjść można również użyć mapowań pól, aby spłaszczyć struktury danych. Aby uzyskać więcej informacji, zobacz [Jak mapować wzbogacone pola do indeksu z wyszukanego](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
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
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Dodaj następujące wiersze do . `Main`

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Spodziewaj się, że utworzenie indeksatora zajmie trochę czasu, aby zakończyć. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. Wykonywanie niektórych umiejętności, takich jak analiza obrazu, jest długotrwałe.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie.

### <a name="explore-creating-the-indexer"></a>Eksplorowanie tworzenia indeksatora

Kod ustawia ```"maxFailedItems"``` się na -1, co nakazuje aparatowi indeksowania ignorowanie błędów podczas importowania danych. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Zwróć również ```"dataToExtract"``` uwagę ```"contentAndMetadata"```na ustawienie . Ta instrukcja nakazuje indeksatorowi automatyczne wyodrębnianie zawartości z plików w różnych formatach, a także metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. Zestaw ```"imageAction"``` do ```"generateNormalizedImages"``` konfiguracji, w połączeniu z umiejętnością Skill I Text Merge OCR, nakazuje indeksatorowi wyodrębnienie tekstu z obrazów (na przykład słowo "stop" ze znaku zatrzymania ruchu) i osadzenie go jako część pola zawartości. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorowanie indeksowania

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy indeksator `GetStatus` jest nadal uruchomiony, należy użyć metody.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
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
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo`reprezentuje bieżący stan i historię wykonywania indeksatora.

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku ostrzeżenia są niegroźne (np. brak tekstowych danych wejściowych dla plików JPEG).

Dodaj następujące wiersze do . `Main`

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Szukaj

Po zakończeniu indeksowania można uruchamiać kwerendy zwracające zawartość poszczególnych pól. Domyślnie usługa Azure Cognitive Search zwraca 50 najlepszych wyników. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [Jak wyświetlić wyniki strony w usłudze Azure Cognitive Search](search-pagination-page-layout.md).

W ramach kroku weryfikacji odpytaj indeks o wszystkie pola.

Dodaj następujące wiersze do . `Main`

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

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

`CreateSearchIndexClient`tworzy nowy `SearchIndexClient` przy użyciu wartości, które są przechowywane w pliku konfiguracyjnym aplikacji (appsettings.json). Należy zauważyć, że klucz interfejsu API kwerendy usługi wyszukiwania jest używany, a nie klucz administratora.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Dodaj następujący kod `Main`do pliku . Pierwszy try-catch zwraca definicję indeksu, z nazwą, typem i atrybutami każdego pola. Drugi to kwerenda sparametryzowana, w której `Select` określa, `organizations`które pola mają być uwzględniane w wynikach, na przykład . Ciąg wyszukiwania `"*"` zwraca całą zawartość pojedynczego pola.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Powtórz dla dodatkowych pól: zawartość, languageCode, keyPhrases i organizacji w tym ćwiczeniu. Wiele pól można zwrócić za pośrednictwem [select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) właściwości przy użyciu listy rozdzielane przecinkami.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalnych rozwoju najbardziej praktyczne podejście do iteracji projektowania jest usunięcie obiektów z usługi Azure Cognitive Search i umożliwić kod, aby je odbudować. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące obiekty i usuwa je, dzięki czemu można ponownie uruchomić kod.

Można również użyć portalu do usuwania indeksów, indeksatorów, źródeł danych i skillsets.

## <a name="takeaways"></a>Wnioski

W tym samouczku pokazano podstawowe kroki tworzenia potoku indeksowania wzbogaconego poprzez tworzenie części składowych: źródło danych, zestaw umiejętności, indeks i indeksator.

[Wprowadzono wbudowane umiejętności,](cognitive-search-predefined-skills.md) wraz z definicją umiejętności i mechaniką łączenia umiejętności poprzez wejścia i wyjścia. Dowiedziałeś się `outputFieldMappings` również, że w definicji indeksatora jest wymagane do routingu wzbogacone wartości z potoku do indeksu z wyszukujalny w usłudze Azure Cognitive Search.

Ponadto przedstawiono sposób testowania wyników i resetowania systemu na potrzeby przyszłych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. Zaprezentowano procedurę sprawdzania stanu indeksatora i usuwania obiektów przed ponownym uruchomieniem potoku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza Wszystkie zasoby lub Grupy zasobów w lewym okienku nawigacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz wszystkie obiekty w potoku wzbogacania SI, przyjrzyjmy się bliżej definicjom zestawów umiejętności i indywidualnym umiejętnościom.

> [!div class="nextstepaction"]
> [Jak utworzyć zestaw umiejętności](cognitive-search-defining-skillset.md)
