---
title: C#Samouczek dotyczący wywoływanie interfejsów API usług Cognitive Services w potoku indeksowania — usługa Azure Search
description: W tym samouczku wykonasz kroki przykładu wyodrębniania danych oraz przetwarzania języka naturalnego i obrazów za pomocą funkcji sztucznej inteligencji w ramach indeksowania w usłudze Azure Search na potrzeby wyodrębniania i przekształcania danych.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 4e6f0317df2f0f631d2c8d3f8e5cefba06e154fd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026837"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Samouczek: Wywołania interfejsów API usług Cognitive Services w usłudze Azure Search indeksowanie potoku

Za pomocą tego samouczka poznasz mechanizm programistycznego wzbogacania danych w usłudze Azure Search przy użyciu *umiejętności poznawczych*. Umiejętności są wspierane przez możliwości analizy obrazu, w usługach Cognitive Services i przetwarzania języka naturalnego (NLP). Za pomocą zestawu umiejętności tworzenia i konfiguracji można wyodrębnić tekst i reprezentacja tekstowa obrazu lub pliku skanowanego dokumentu. Może także wykryć język, jednostki, kluczowe frazy i. Wynik końcowy to zaawansowane dodatkowej zawartości do indeksu usługi Azure Search, utworzone przez potokiem indeksowania bazujących na sztucznej Inteligencji.

W tym samouczku używasz zestawu SDK platformy .NET do wykonywania następujących zadań:

> [!div class="checklist"]
> * Utworzenie potoku indeksowania, który wzbogaca przykładowe dane w drodze do indeksu
> * Stosowanie wbudowanych umiejętności: optyczne rozpoznawanie znaków, funkcja scalająca tekst, wykrywanie języka, dzielenie tekstu, rozpoznawanie jednostek, wyodrębnianie kluczowych fraz
> * Poznanie sposobu łączenia umiejętności w łańcuch przez mapowanie danych wejściowych na dane wyjściowe w zestawie umiejętności
> * Wykonywanie żądań i przeglądanie wyników
> * Resetowanie indeksu i indeksatorów w celu kontynuowania opracowywania

Dane wyjściowe stanowią indeks z możliwością wyszukiwania pełnotekstowego w usłudze Azure Search. Indeks możesz rozszerzyć za pomocą innych standardowych możliwości, takich jak [synonimy](search-synonyms.md), [profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analizatory](search-analyzers.md) i [filtry](search-filters.md).

W tym samouczku jest uruchamiany bezpłatnej usługi, ale liczba bezpłatnych transakcji jest ograniczona do 20 dokumentów na dzień. Jeśli chcesz uruchomić w tym samouczku więcej niż jeden raz w ciągu tego samego dnia, należy użyć mniejszy plik ustawione, tak więc mieści się w dodatkowych uruchomień.

> [!NOTE]
> Ponieważ zakres jest rozwiniesz przez zwiększenie częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, należy dołączyć płatnych zasobu usług Cognitive Services. Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie za istniejącą [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/) . Cennik wyodrębniania obraz został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi, narzędzia i dane są używane w ramach tego samouczka. 

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

[Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych.

[Przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) zawiera zestaw mały plik o różnych typach. 

[Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) do użycia jako IDE.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Aby korzystać z usługi Azure Search należy adres URL usługi i klucza dostępu. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

   ![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

Potok wzbogacania ściąga dane ze źródeł danych platformy Azure. Dane muszą pochodzić ze źródła danych, którego typ jest obsługiwany przez [indeksator usługi Azure Search](search-indexer-overview.md). Usługa Azure Table Storage nie jest obsługiwana dla usłudze wyszukiwania poznawczego. Na potrzeby tego ćwiczenia będziemy korzystać z usługi Blob Storage, aby zaprezentować wiele typów zawartości.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) zawiera przykładowe dane. Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości. Ten samouczek zakłada, że nazwa kontenera jest "basic-demo-data-pr".

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń, aby przekazać [przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Po załadowaniu przykładowych plików uzyskaj nazwę kontenera i parametry połączenia dla usługi Blob Storage. Można to zrobić przejdź do swojego konta magazynu w witrynie Azure portal, wybierając **klucze dostępu**, a następnie skopiuj **parametry połączenia** pola.

   Parametry połączenia powinny być adresem URL podobnym do następującego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Istnieją inne sposoby określania parametrów połączenia, takie jak podanie sygnatury dostępu współdzielonego. Aby dowiedzieć się więcej na temat poświadczeń źródła danych, zobacz [Indeksowanie w usłudze Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Rozpocznij, otwierając program Visual Studio i tworzenia nowego projektu aplikacji konsoli, która może działać na platformie .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

[Zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk) składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksów, źródła danych, indeksatorów i dokładniejsze, jak również przekazywanie i zarządzania dokumentami i wykonywania zapytań, wszystko to bez konieczności szczegółowe informacje o HTTP i JSON. Tych bibliotek klienckich są dystrybuowane jako pakiety NuGet.

Dla tego projektu, musisz zainstalować wersję zapoznawczą 7.x.x `Microsoft.Azure.Search` NuGet pakietu i najnowsze `Microsoft.Extensions.Configuration.Json` pakietu NuGet.

Zainstaluj `Microsoft.Azure.Search` pakietu NuGet za pomocą konsoli Menedżera pakietów w programie Visual Studio. Aby otworzyć wybierz pozycję Konsola Menedżera pakietów **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**. Aby uzyskać polecenie do uruchomienia, przejdź do [strona pakietu Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search), a następnie wybierz wersję 7.x.x-preview i skopiuj polecenie Menedżera pakietów. W konsoli Menedżera pakietów Uruchom to polecenie.

Aby zainstalować `Microsoft.Extensions.Configuration.Json` pakietu NuGet w programie Visual Studio, wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Zarządzaj pakietami NuGet dla rozwiązania...** . Wybierz pozycję Przeglądaj, a następnie wyszukaj `Microsoft.Extensions.Configuration.Json` pakietu NuGet. Po znalezieniu go, wybierz pakiet, wybierz swój projekt, upewnij się, że wersja jest najnowsza stabilna wersja, a następnie wybierz opcję instalacji.

## <a name="add-azure-search-service-information"></a>Dodaj informacje o usłudze Azure Search

Aby można było nawiązać połączenie z usługi Azure Search należy dodać informacji o usłudze wyszukiwania do projektu. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz **Dodaj** > **nowy element...**  . Nadaj plikowi nazwę `appsettings.json` i wybierz **Dodaj**. 

Ten plik będzie musiał zostać uwzględnione w katalogu danych wyjściowych. Aby to zrobić, kliknij prawym przyciskiem myszy `appsettings.json` i wybierz **właściwości**. Zmień wartość właściwości **Kopiuj do katalogu wyjściowego** do **nowszą kopię**.

Kopiuj poniższe dane JSON do nowego pliku JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Dodaj wyszukiwanie usług i obiektów blob magazynu informacje o Twoim koncie.

Twoje informacje o usłudze wyszukiwania można uzyskać ze strony swojego konta wyszukiwania w witrynie Azure portal. Nazwa konta będzie się na stronie głównej i można je znaleźć, wybierając **klucze**.

Parametry połączenia obiektu blob można uzyskać, przechodząc do swojego konta magazynu w witrynie Azure portal, wybierając **klucze dostępu**i następnie kopiowanie **parametry połączenia** pola.

## <a name="add-namespaces"></a>Dodaj obszar nazw

Ten samouczek używa wiele różnych typów z różnych przestrzeni nazw. Aby można było używać tych typów, Dodaj następujący kod do `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Tworzenie klienta

Utwórz wystąpienie obiektu `SearchServiceClient` klasy przy użyciu informacji o dodaniu do `appsettings.json`.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

> [!NOTE]
> Klasa `SearchServiceClient` zarządza połączeniami z usługą wyszukiwania. W celu uniknięcia otwarcia zbyt wielu połączeń, należy, w miarę możliwości, udostępnić pojedyncze wystąpienie klasy `SearchServiceClient` w aplikacji. Metody tej klasy są bezpieczne wątkowo, co pozwala na tego rodzaju udostępnianie.
> 
> 

## <a name="create-a-data-source"></a>Tworzenie źródła danych

Utwórz nową `DataSource` wystąpienia, wywołując `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` wymaga się, że podajesz nazwę źródła danych, parametry połączenia i nazwa kontenera obiektów blob.

Chociaż nie jest używany w tym samouczku zasad usuwania nietrwałego jest również definiowany, który jest używany do identyfikowania usunięte obiekty BLOB na podstawie wartości kolumny usuwania nietrwałego. Następujące zasady uwzględnia obiektu blob do usunięcia, jeśli ma ona właściwość metadanych `IsDeleted` wartością `true`.

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

Skoro już zainicjować `DataSource` obiektów, Utwórz źródło danych. `SearchServiceClient` ma właściwość `DataSources`. Ta właściwość zapewnia wszystkie metody, należy utworzyć listę, aktualizowanie lub usuwanie źródeł danych usługi Azure Search.

Dla żądania zakończonego powodzeniem metoda zwróci źródła danych, który został utworzony. W przypadku problemu z żądaniem, takie jak nieprawidłowy parametr, metoda zgłosi wyjątek.

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

Ponieważ jest to pierwsze żądanie, sprawdź w witrynie Azure Portal, czy źródło danych zostało utworzone w usłudze Azure Search. Na stronie pulpitu nawigacyjnego usługi wyszukiwania zweryfikuj, czy kafelek Źródła danych zawiera nowy element. Może trzeba będzie zaczekać kilka minut na odświeżenie strony portalu.

  ![Kafelek Źródła danych w portalu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kafelek Źródła danych w portalu")

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

W tej sekcji można zdefiniować zestaw wzbogacania kroków, które mają być stosowane do danych. Każdy krok wzbogacania jest nazywany *umiejętności* oraz zestawów kroków wzbogacania *zestawu umiejętności*. W ramach tego samouczka są używane [wstępnie zdefiniowane umiejętności poznawcze](cognitive-search-predefined-skills.md) z następujących zestawów umiejętności:

+ [Optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) rozpoznawanie tekstu drukowanego i pisma odręcznego w plikach obrazu.

+ [Funkcja scalająca tekst](cognitive-search-skill-textmerger.md) skonsolidować tekst z kolekcji pól do pojedynczego pola.

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) przerwanie dużej ilości treści na mniejsze części przed wywołaniem umiejętności wyodrębnianie kluczowych fraz kwalifikacjami zawodowymi rozpoznawanie podmiotów. Wyodrębnianie kluczowych fraz i rozpoznawanie jednostek znaku akceptują dane wejściowe niż 50 000 znaków. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Rozpoznawanie jednostek znaku](cognitive-search-skill-named-entity-recognition.md) — wyodrębnianie nazw organizacji z zawartości w kontenerze obiektów blob.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych.

Podczas początkowego przetwarzania w usłudze Azure Search pęknięcia każdy dokument na odczyt zawartości z innych formatów plików. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. W efekcie zestawu danych wejściowych jako ```"/document/content"``` używanie tego tekstu. 

Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Optyczne rozpoznawanie znaków umiejętności

**Optyczne rozpoznawanie znaków** umiejętności umożliwia wyodrębnianie tekstu z obrazów. To umiejętności przyjęto założenie, że pole normalized_images istnieje. Do wygenerowania tego pola w dalszej części samouczka ustawimy ```"imageAction"``` konfiguracji w definicji indeksatora ```"generateNormalizedImages"```.

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
    description: "Extract text (plain and structured) from image).",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>Scal umiejętności

W tej sekcji utworzysz **scalania** umiejętności, które scala pola zawartość dokumentu, tekst, który został utworzony przez umiejętności optyczne rozpoznawanie znaków.

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

### <a name="language-detection-skill"></a>Umiejętności wykrywanie języka

**Wykrywanie języka** umiejętności wykrywa język tekstu wejściowego i Raportowanie kodu jeden język dla każdego dokumentu na żądanie. Użyjemy danych wyjściowych **wykrywanie języka** umiejętności w ramach danych wejściowych do **dzielenie tekstu** umiejętności.

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
    description: "Language detection skill",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>Podziel umiejętności tekstu

Poniżej **podziału** umiejętności będzie dzielenie tekstu przez strony i ogranicza strony do 4000 znaków, gdyż jest mierzone przez `String.Length`. Algorytm będzie próbował podzielić tekst na fragmenty, które są co najwyżej `maximumPageLength` rozmiar. W tym przypadku algorytm będzie wykonywać doskonale na Podziel zdania na granicy zdania, dlatego rozmiar fragmentu może być nieco mniej niż `maximumPageLength`.

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

### <a name="named-entity-recognition-skill"></a>Umiejętności rozpoznawania nazwanych jednostek

To `NamedEntityRecognitionSkill` wystąpienia jest równa rozpoznaje typ kategorii `organization`. **Rozpoznawania jednostki o nazwie** umiejętności mogą także rozpoznaje typy kategorii `person` i `location`.

Należy zauważyć, że pole "kontekst" jest ustawione na ```"/document/pages/*"``` z gwiazdką, co oznacza kroku wzbogacania jest wywoływana dla każdej strony w obszarze ```"/document/pages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<NamedEntityCategory> namedEntityCategory = new List<NamedEntityCategory>();
namedEntityCategory.Add(NamedEntityCategory.Organization);
    
NamedEntityRecognitionSkill namedEntityRecognition = new NamedEntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: namedEntityCategory,
    defaultLanguageCode: NamedEntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>Umiejętności wyodrębnianie kluczowych fraz

Podobnie jak `NamedEntityRecognitionSkill` wystąpienia, która właśnie została utworzona, **klucz frazy** umiejętności jest wywoływana dla każdej strony dokumentu.

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

### <a name="build-and-create-the-skillset"></a>Tworzenie i tworzenie zestawu umiejętności

Tworzenie `SkillSet` z wykorzystaniem umiejętności tworzenia.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(namedEntityRecognition);
skills.Add(keyPhraseExtractionSkill);

Skillset skillSet = new Skillset(
    name: "demoskillset",
    description: "Demo Skillset",
    skills: skills);
```

Tworzenie zestawu umiejętności w usłudze wyszukiwania.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillSet);
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

Klasa modelu polega na dodaniu do nowego C# pliku. Kliknij prawym przyciskiem myszy na projekt i wybierz **Dodaj** > **nowy element...** , wybierz pozycję "Class" i nazwij plik `DemoIndex.cs`, a następnie wybierz **Dodaj**.

Upewnij się wskazać, że chcesz użyć typów z `Microsoft.Azure.Search` i `Microsoft.Azure.Search.Models` przestrzeni nazw.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Dodaj poniższe definicji klasy modelu, aby `DemoIndex.cs` i uwzględnić go w tej samej przestrzeni nazw, gdy użytkownik utworzy indeks.

```csharp
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
```

Skoro zdefiniowano klasę modelu, ponownie do niej `Program.cs` można dość łatwo utworzyć definicję indeksu. Nazwa tego indeksu będzie "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Podczas testowania może się okazać, że próbuje utworzyć indeks więcej niż jeden raz. W związku z tym Sprawdź, czy indeks, który masz zamiar utworzyć, już istnieje przed próbą utworzenia go. 

```csharp
bool exists = serviceClient.Indexes.Exists(index.Name);
if (exists)
{
    serviceClient.Indexes.Delete(index.Name);
}

serviceClient.Indexes.Create(index);
```

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [Tworzenie indeksu (interfejs API REST usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Tworzenie indeksatora, mapowanie pól i wykonywanie przekształceń

Do tej pory utworzono źródło danych, zestaw umiejętności i indeks. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Aby powiązać je razem w indeksatorze, należy zdefiniować mapowania pól.

+ FieldMappings są przetwarzane przed zestawu umiejętności, mapowanie pola źródłowego ze źródła danych do docelowej pól w indeksie. Jeśli nazwy pól i typów są takie same na obu końcach, żadne mapowanie jest wymagana.

+ OutputFieldMappings są przetwarzane od zestawu umiejętności, odwołuje się do sourceFieldNames, które nie istnieją aż łamania dokumentów lub wzbogacania ich tworzenia. TargetFieldName jest polem w indeksie.

Oprócz Podłączanie dane wejściowe, aby dane wyjściowe, umożliwia także mapowania pól do spłaszczenia struktur danych. Aby uzyskać więcej informacji, zobacz [sposób mapowania pól wzbogaconego na indeks z możliwością wyszukiwania](cognitive-search-output-field-mapping.md).

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

bool exists = serviceClient.Indexers.Exists(indexer.Name);
if (exists)
{
    serviceClient.Indexers.Delete(indexer.Name);
}

try
{
    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

Oczekują, że utworzenie indeksatora zajmie trochę czasu, aby zakończyć. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. Wykonywanie niektórych umiejętności, takich jak analiza obrazu, jest długotrwałe.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie.

### <a name="explore-creating-the-indexer"></a>Tematem jest tworzenie indeksatora

Zestawy kodów ```"maxFailedItems"``` na -1, która powoduje, że aparat indeksowania ignorować błędy podczas importowania danych. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Należy również zauważyć ```"dataToExtract"``` ustawiono ```"contentAndMetadata"```. Ta instrukcja nakazuje indeksatorowi automatyczne wyodrębnianie zawartości z plików w różnych formatach, a także metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. ```"imageAction"``` Równa ```"generateNormalizedImages"``` konfiguracji, w połączeniu z umiejętności optyczne rozpoznawanie znaków i umiejętności scalania tekstu informuje indeksator do wyodrębniania tekstu z obrazów (na przykład słowo "stop" od ruchu znak) i osadzenie go jako część zawartości pola. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

## <a name="check-indexer-status"></a>Sprawdzanie stanu indeksatora

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy indeksatora jest nadal uruchomiona, należy użyć `GetStatus` metody.

```csharp
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
        Console.WriteLine("No indexer status information");
        break;
}
```

`IndexerExecutionInfo` reprezentuje bieżący stan i wykonywanie historii indeksatora.

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku ostrzeżenia są niegroźne (np. brak tekstowych danych wejściowych dla plików JPEG).
 
## <a name="verify-content"></a>Weryfikowanie zawartości

Po zakończeniu indeksowania można uruchomić zapytania, które zwracają wartości poszczególnych pól. Domyślnie usługa Azure Search zwraca 50 najlepszych wyników. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [Jak wyświetlać strony wyników w usłudze Azure Search](search-pagination-page-layout.md).

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

Powtórz tę procedurę dla pola dodatkowe: zawartość, languageCode, keyPhrases i organizacji, w tym ćwiczeniu. Istnieje możliwość zwrócenia wielu pól za pomocą elementu `$select` używającego listy wartości rozdzielonych przecinkami.

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>Dostęp do wzbogaconego dokumentu

Funkcja wyszukiwania poznawczego umożliwia wyświetlenie struktury wzbogaconego dokumentu. Wzbogacone dokumenty to tymczasowe struktury tworzone podczas wzbogacania, a następnie usuwane po zakończeniu procesu.

Aby przechwycić migawkę wzbogaconego dokumentu utworzoną podczas indeksowania, dodaj pole o nazwie ```enriched``` do indeksu. Indeksator automatycznie zrzuca do tego pola ciąg będący reprezentacją wszystkich wzbogaceń dokumentu.

Pole ```enriched``` będzie zawierać ciąg, który jest logiczną reprezentacją wzbogaconego dokumentu w pamięci w formacie JSON.  Wartość pola jest jednak prawidłowym dokumentem w formacie JSON. Cudzysłowy są umieszczane w sekwencji ucieczki, więc musisz zastąpić ciąg `\"` znakiem `"`, aby wyświetlić dokument w postaci sformatowanego kodu JSON.  

Pole ```enriched``` jest przeznaczone do debugowania i ma za zadanie ułatwić zrozumienie logicznego kształtu zawartości, względem której wyrażenia są oceniane. Może to być przydatne narzędzie do poznawania i debugowania zestawu umiejętności.

Powtórz poprzednie ćwiczenie, dołączając pole `enriched` w celu przechwycenia zawartości wzbogaconego dokumentu:

### <a name="request-body-syntax"></a>Składnia treść żądania
```csharp
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

    public string Enriched { get; set; }
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalne rozwoju najbardziej praktyczne podejście do projektowania iteracji jest usunięcie obiektów z usługi Azure Search i kodu ponownie skompilować je. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy. 

W tym samouczku trwało obsługuje sprawdzanie istniejących indeksatory i indeksy i ich usuwania, jeśli są one już istnieje, dzięki czemu możesz ponownie uruchomić kod.

Portal umożliwia również usunięcie indeksów, indeksatorów i dokładniejsze.

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
