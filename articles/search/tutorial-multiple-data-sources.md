---
title: 'C# Samouczek: Indeksowanie wielu źródeł danych'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak importować dane z wielu źródeł danych do jednego indeksu usługi Azure Cognitive Search przy użyciu indeksatorów. Ten samouczek i przykładowy kod są w języku C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271482"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Samouczek: Indeksowanie danych z wielu źródeł danych w języku C #

Usługa Azure Cognitive Search może importować, analizować i indeksować dane z wielu źródeł danych do jednego indeksu wyszukiwania konsolidowania. Obsługuje to sytuacje, w których dane strukturalne są agregowane z mniej ustrukturyzowanymi lub nawet zwykłymi danymi tekstowymi z innych źródeł, takimi jak tekst, HTML lub dokumenty JSON.

W tym samouczku opisano sposób indeksowania danych hoteli ze źródła danych usługi Azure Cosmos DB i scalania ich ze szczegółami pokoju hotelowego pobranymi z dokumentów usługi Azure Blob Storage. Rezultatem będzie połączony indeks wyszukiwania hoteli zawierający złożone typy danych.

W tym samouczku użyto języka C# i [zestawu .NET SDK](https://aka.ms/search-sdk). W tym samouczku wykonaj następujące zadania:

> [!div class="checklist"]
> * Przekazywanie przykładowych danych i tworzenie źródeł danych
> * Identyfikowanie klucza dokumentu
> * Definiowanie i tworzenie indeksu
> * Indeksuj dane hoteli z usługi Azure Cosmos DB
> * Scalanie danych pokoju hotelowego z magazynu obiektów blob

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Tworzenie](search-create-service-portal.md) lub [znajdowanie istniejącej usługi wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby zaakceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

Kod źródłowy tego samouczka znajduje się w repozytorium GitHub w repozytorium github w folderze [wielu źródeł danych.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="1---create-services"></a>1 - Tworzenie usług

W tym samouczku użyto usługi Azure Cognitive Search do indeksowania i kwerend, usługi Azure Cosmos DB dla jednego zestawu danych i magazynu obiektów Blob platformy Azure dla drugiego zestawu danych. 

Jeśli to możliwe, utwórz wszystkie usługi w tym samym regionie i grupie zasobów dla bliskości i łatwości zarządzania. W praktyce usługi mogą znajdować się w dowolnym regionie.

W tym przykładzie użyto dwóch małych zestawów danych opisujących siedem fikcyjnych hoteli. Jeden zestaw opisuje same hotele i zostanie załadowany do bazy danych usługi Azure Cosmos DB. Drugi zestaw zawiera szczegóły pokoju hotelowego i jest dostarczany jako siedem oddzielnych plików JSON do przekazania do usługi Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Zacznij od cosmosu DB

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com), a następnie przejdź do strony Omówienie konta usługi Azure Cosmos DB.

1. Wybierz **Pozycję Eksplorator danych,** a następnie wybierz pozycję **Nowa baza danych**.

   ![Tworzenie nowej bazy danych](media/tutorial-multiple-data-sources/cosmos-newdb.png "Tworzenie nowej bazy danych")

1. Wprowadź nazwę **hotel-pokoje-db**. Zaakceptuj wartości domyślne dla pozostałych ustawień.

   ![Konfigurowanie bazy danych](media/tutorial-multiple-data-sources/cosmos-dbname.png "Konfigurowanie bazy danych")

1. Utwórz nowy kontener. Użyj istniejącej bazy danych, która została właśnie utworzona. Wprowadź **hotele** dla nazwy kontenera i użyj **/HotelId** dla klucza partycji.

   ![Dodawanie kontenera](media/tutorial-multiple-data-sources/cosmos-add-container.png "Dodawanie kontenera")

1. Wybierz **pozycję Elementy** w obszarze **hotele**, a następnie kliknij pozycję **Przekaż element** na pasku poleceń. Przejdź do folderu projektu, a następnie wybierz plik **cosmosdb/HotelsDataSubset_CosmosDb.json.**

   ![Przekaż do kolekcji usługi Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Przekaż do kolekcji usługi Cosmos DB")

1. Użyj przycisku Odśwież, aby odświeżyć widok przedmiotów w kolekcji hoteli. Na liście powinno być widocznych siedem nowych dokumentów bazy danych.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com), przejdź do konta magazynu platformy Azure, kliknij pozycję **Obiekty Blobs**, a następnie kliknij przycisk **+ Kontener**.

1. [Utwórz kontener obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) o nazwie **pokoje hotelowe** do przechowywania przykładowych plików JSON pokoju hotelowego. Można ustawić publiczny poziom dostępu na dowolną z jego prawidłowych wartości.

   ![Tworzenie kontenera obiektów blob](media/tutorial-multiple-data-sources/blob-add-container.png "Tworzenie kontenera obiektów blob")

1. Po utworzeniu kontenera otwórz go i wybierz **pozycję Przekaż** na pasku poleceń. Przejdź do folderu zawierającego przykładowe pliki. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   ![Przekazywanie plików](media/tutorial-multiple-data-sources/blob-upload.png "Przekazywanie plików")

Po zakończeniu przekazywania pliki powinny pojawić się na liście kontenera danych.

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

1. Uruchom program Visual Studio 2019 i w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet,** a następnie **zarządzaj pakietami NuGet dla rozwiązania...**. 

1. Na karcie **Przeglądaj** znajdź, a następnie zainstaluj **usługę Microsoft.Azure.Search** (wersja 9.0.1 lub nowsza). Aby ukończyć instalację, należy kliknąć dodatkowe okna dialogowe.

    ![Dodawanie bibliotek platformy Azure za pomocą nuget](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Wyszukaj pakiet **Microsoft.Extensions.Configuration.Json** NuGet i zainstaluj go również.

1. Otwórz plik rozwiązania **AzureSearchMultipleDataSources.sln**.

1. W Eksploratorze rozwiązań edytuj plik **appsettings.json,** aby dodać informacje o połączeniu.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Pierwsze dwa wpisy używają kluczy adresu URL i administratora usługi Azure Cognitive Search. Biorąc pod uwagę `https://mydemo.search.windows.net`punkt końcowy , na przykład, nazwa usługi do dostarczenia jest `mydemo`.

Następne wpisy określają nazwy kont i informacje o ciągu połączenia dla źródeł danych usługi Azure Blob Storage i Usługi Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 - Mapa pól kluczy

Scalanie zawartości wymaga, aby oba strumienie danych były przeznaczone dla tych samych dokumentów w indeksie wyszukiwania. 

W usłudze Azure Cognitive Search pole klucza jednoznacznie identyfikuje każdy dokument. Każdy indeks wyszukiwania musi mieć dokładnie `Edm.String`jedno kluczowe pole typu . To pole klucza musi być obecne dla każdego dokumentu w źródle danych, który jest dodawany do indeksu. (W rzeczywistości jest to jedyne wymagane pole.)

Podczas indeksowania danych z wielu źródeł danych upewnij się, że każdy przychodzący wiersz lub dokument zawiera wspólny klucz dokumentu do scalenia danych z dwóch fizycznie odrębnych dokumentów źródłowych do nowego dokumentu wyszukiwania w połączonym indeksie. 

Często wymaga pewnego planowania z góry, aby zidentyfikować znaczący klucz dokumentu dla indeksu i upewnij się, że istnieje w obu źródłach danych. W tym demo `HotelId` klucz dla każdego hotelu w Cosmos DB jest również obecny w pokojach JSON obiektów blob w magazynie obiektów Blob.

Indeksatory usługi Azure Cognitive Search mogą używać mapowań pól do zmiany nazwy, a nawet formatowania pól danych podczas procesu indeksowania, dzięki czemu dane źródłowe mogą być kierowane do właściwego pola indeksu. Na przykład w usłudze Cosmos DB **`HotelId`** identyfikator hotelu jest wywoływany . Ale w plikach blob JSON dla pokoi hotelowych **`Id`** identyfikator hotelu jest nazwany . Program obsługuje to, mapując **`Id`** pole z obiektów **`HotelId`** blob do pola klucza w indeksie.

> [!NOTE]
> W większości przypadków automatycznie generowane klucze dokumentów, takie jak te utworzone domyślnie przez niektóre indeksatory, nie są dobre klucze dokumentu dla indeksów połączonych. Ogólnie rzecz biorąc, należy użyć znaczącej, unikatowej wartości klucza, która już istnieje w źródłach danych lub można ją łatwo dodać do nich.

## <a name="4---explore-the-code"></a>4 - Zapoznaj się z kodem

Po wprowadzeniu ustawień danych i konfiguracji przykładowy program w **witrynie AzureSearchMultipleDataSources.sln** powinien być gotowy do tworzenia i uruchamiania.

Ta prosta aplikacja konsoli C#/.NET wykonuje następujące zadania:

* Tworzy nowy indeks na podstawie struktury danych klasy Hotel języka C# (która również odwołuje się do address i room klas).
* Tworzy nowe źródło danych i indeksator, który mapuje dane usługi Azure Cosmos DB do pól indeksu. Są to oba obiekty w usłudze Azure Cognitive Search.
* Uruchamia indeksator, aby załadować dane hotelu z usługi Cosmos DB.
* Tworzy drugie źródło danych i indeksator, który mapuje dane obiektów blob JSON do pól indeksu.
* Uruchamia drugi indeksator, aby załadować dane pokoje z magazynu obiektów Blob.

 Przed uruchomieniem programu należy poświęcić chwilę na zbadanie kodu i definicji indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **Hotel.cs** zawiera schemat definiujący indeks
  + **Program.cs** zawiera funkcje, które tworzą indeks usługi Azure Cognitive Search, źródła danych i indeksatory i ładują połączone wyniki do indeksu.

### <a name="create-an-index"></a>Tworzenie indeksu

Ten przykładowy program używa pliku .NET SDK do definiowania i tworzenia indeksu usługi Azure Cognitive Search. Korzysta z [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) klasy do generowania struktury indeksu z klasy modelu danych Języka C#.

Model danych jest zdefiniowany przez hotel klasy, która zawiera również odwołania do adres i pokój klas. FieldBuilder przechodzi do szczegółów za pomocą wielu definicji klas, aby wygenerować złożoną strukturę danych dla indeksu. Znaczniki metadanych służą do definiowania atrybutów każdego pola, na przykład tego, czy można go przeszukiwać, czy sortować.

Następujące fragmenty kodu z pliku **Hotel.cs** pokazują, jak można określić pojedyncze pole i odwołanie do innej klasy modelu danych.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

W pliku **Program.cs** indeks jest definiowany z nazwą i kolekcją `FieldBuilder.BuildForType<Hotel>()` pól wygenerowaną przez metodę, a następnie tworzony w następujący sposób:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Tworzenie źródła danych i indeksatora usługi Azure Cosmos DB

Następnie główny program zawiera logikę do tworzenia źródła danych usługi Azure Cosmos DB dla danych hoteli.

Najpierw łączy nazwę bazy danych usługi Azure Cosmos DB z ciągiem połączenia. Następnie definiuje obiekt źródła danych, w tym ustawienia specyficzne dla źródeł usługi Azure Cosmos DB, takich jak [useChangeDetection] właściwość.

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Po utworzeniu źródła danych program konfiguruje indeksator bazy danych Usługi Azure Cosmos o nazwie **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Program usunie wszystkie istniejące indeksatory o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż jeden raz.

W tym przykładzie definiuje harmonogram dla indeksatora, tak aby będzie działać raz dziennie. Można usunąć właściwości harmonogram z tego wywołania, jeśli nie chcesz, aby indeksator automatycznie uruchomić ponownie w przyszłości.

### <a name="index-azure-cosmos-db-data"></a>Indeksuj dane usługi Azure Cosmos DB

Po utworzeniu źródła danych i indeksatora kod uruchamiający indeksator jest krótki:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

W tym przykładzie znajduje się prosty blok try-catch do zgłaszania błędów, które mogą wystąpić podczas wykonywania.

Po uruchomieniu indeksatora usługi Azure Cosmos DB indeks wyszukiwania będzie zawierał pełny zestaw przykładowych dokumentów hotelowych. Jednak pole pokoje dla każdego hotelu będzie pustą tablicą, ponieważ źródło danych usługi Azure Cosmos DB nie zawierało żadnych szczegółów pomieszczenia. Następnie program będzie pobierać z magazynu obiektów Blob, aby załadować i scalić dane pomieszczenia.

### <a name="create-blob-storage-data-source-and-indexer"></a>Tworzenie źródła danych magazynu obiektów blob i indeksatora

Aby uzyskać szczegóły pokoju program najpierw konfiguruje źródło danych magazynu obiektów Blob, aby odwoływać się do zestawu pojedynczych plików obiektów blob JSON.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Po utworzeniu źródła danych program konfiguruje indeksator obiektów blob o nazwie **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Obiekty BLOB JSON zawierają **`Id`** pole klucza o nazwie zamiast **`HotelId`**. Kod używa klasy, `FieldMapping` aby poinformować indeksatora, aby skierował wartość **`Id`** pola do klucza **`HotelId`** dokumentu w indeksie.

Indeksatory magazynu obiektów blob można użyć parametrów, które identyfikują tryb analizy, który ma być używany. Tryb analizowania różni się w przypadku obiektów blob, które reprezentują pojedynczy dokument lub wiele dokumentów w tym samym obiekcie blob. W tym przykładzie każdy obiekt blob reprezentuje pojedynczy dokument `IndexingParameters.ParseJson()` indeksu, więc kod używa parametru.

Aby uzyskać więcej informacji na temat analizowania parametrów indeksatora dla obiektów blob JSON, zobacz [Indeksowanie obiektów blob JSON](search-howto-index-json-blobs.md). Aby uzyskać więcej informacji na temat określania tych parametrów przy użyciu zestawu .NET SDK, zobacz [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) klasy.

Program usunie wszystkie istniejące indeksatory o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż jeden raz.

W tym przykładzie definiuje harmonogram dla indeksatora, tak aby będzie działać raz dziennie. Można usunąć właściwości harmonogram z tego wywołania, jeśli nie chcesz, aby indeksator automatycznie uruchomić ponownie w przyszłości.

### <a name="index-blob-data"></a>Indeksuj dane obiektu blob

Po utworzeniu źródła danych magazynu obiektów blob i indeksatora, kod, który uruchamia indeksator jest proste:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Ponieważ indeks został już wypełniony danymi hoteli z bazy danych usługi Azure Cosmos DB, indeksator obiektów blob aktualizuje istniejące dokumenty w indeksie i dodaje szczegóły pokoju.

> [!NOTE]
> Jeśli masz te same pola inne niż klucz w obu źródłach danych, a dane w tych polach nie są zgodne, indeks będzie zawierał wartości z dowolnego indeksatora, który został ostatnio uruchomiony. W naszym przykładzie oba źródła danych zawierają pole **HotelName.** Jeśli z jakiegoś powodu dane w tym polu są różne, w przypadku dokumentów o tej samej wartości klucza, dane **HotelName** ze źródła danych, które zostały ostatnio zindeksowane, będą wartością przechowywaną w indeksie.

## <a name="5---search"></a>5 - Szukaj

Po uruchomieniu programu można eksplorować indeks wyszukiwania wypełnionego za pomocą [**Eksploratora wyszukiwania**](search-explorer.md) w portalu.

W witrynie Azure portal otwórz stronę **Przegląd** usługi wyszukiwania i znajdź indeks **przykładowy pokoje hotelowe** na liście **Indeksy.**

  ![Lista indeksów usługi Azure Cognitive Search](media/tutorial-multiple-data-sources/index-list.png "Lista indeksów usługi Azure Cognitive Search")

Kliknij na indeks hotel-pokoje próbki na liście. Zostanie wyświetlony interfejs Eksploratora wyszukiwania dla indeksu. Wprowadź kwerendę dla terminu takiego jak "Luksus". Powinien być widoczny co najmniej jeden dokument w wynikach, a ten dokument powinien wyświetlać listę obiektów pomieszczeń w tablicy pomieszczeń.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

We wczesnych etapach eksperymentalnych rozwoju najbardziej praktyczne podejście do iteracji projektowania jest usunięcie obiektów z usługi Azure Cognitive Search i umożliwić kod, aby je odbudować. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące obiekty i usuwa je, dzięki czemu można ponownie uruchomić kod.

Można również użyć portalu do usuwania indeksów, indeksatorów i źródeł danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas pracy we własnej subskrypcji, na końcu projektu, to dobry pomysł, aby usunąć zasoby, które nie są już potrzebne. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza Wszystkie zasoby lub Grupy zasobów w lewym okienku nawigacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz pojęcie pozyskiwania danych z wielu źródeł, przyjrzyjmy się bliżej konfiguracji indeksatora, począwszy od usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksatora usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)