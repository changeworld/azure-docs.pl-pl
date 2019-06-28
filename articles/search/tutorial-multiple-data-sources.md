---
title: 'Samouczek: Indeks z wieloma źródłami danych — usługa Azure Search'
description: Dowiedz się, jak importować dane z wielu źródeł danych do jednego indeksu usługi Azure Search.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 4186c422836771de4f8a283616d77214b91bfc02
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462702"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Samouczek: Połącz dane z wielu źródeł danych w jednym indeksie usługi Azure Search

Usługa Azure Search można zaimportować, analizowania i indeksowanie danych z wielu źródeł danych w jeden połączone indeks wyszukiwania. Obejmuje to obsługę w sytuacjach, gdzie strukturalne dane są agregowane z danymi ze strukturą mniej, a nawet zwykły tekst z innych źródeł, takich jak tekst, HTML, lub dokumenty JSON.

W tym samouczku opisano sposób indeksowanie hotelu danych ze źródła danych usługi Azure Cosmos DB i scalania, ze szczegółami pokoju hotelu otrzymane dokumentacji usługi Azure Blob Storage. Wynik będzie indeksu wyszukiwania połączone hotelu zawierający złożonych typów danych.

W tym samouczku C#, zestawu SDK .NET dla usługi Azure Search i witryny Azure portal wykonać następujące zadania:

> [!div class="checklist"]
> * Przekazywanie przykładowych danych i tworzenia źródeł danych
> * Zidentyfikuj klucz dokumentu
> * Definiowanie i tworzenie indeksu
> * Indeks hotelu dane z bazy danych cosmos DB
> * Scal dane pokoju w hotelu z magazynu obiektów blob

## <a name="prerequisites"></a>Wymagania wstępne

Następujących usług, narzędzi i danych są używane w tym przewodniku Szybki Start. 

- [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

- [Tworzenie konta usługi Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych hotelu.

- [Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) przykładowym danym JSON do przechowywania danych obiektu blob.

- [Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) do użycia jako IDE.

### <a name="install-the-project-from-github"></a>Instalowanie projektu z usługi GitHub

1. Znajdź przykładowe repozytorium w witrynie GitHub: [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Wybierz **klonowania lub pobierania** i prywatne lokalnej kopii repozytorium.
1. Otwórz program Visual Studio i zainstaluj pakiet NuGet platformy Microsoft Azure Search, jeśli nie jest jeszcze zainstalowana. W **narzędzia** menu, wybierz opcję **Menedżera pakietów NuGet** i następnie **Zarządzaj pakietami NuGet dla rozwiązania...** . Wybierz **Przeglądaj** kartę, a następnie w polu wyszukiwania wpisz "Usługi Azure Search". Zainstaluj **Microsoft.Azure.Search** , gdy pojawia się na liście (w wersji 9.0.1, lub nowszej). Trzeba będzie przejść przez wyświetlanie dodatkowych okien dialogowych, aby ukończyć instalację.

    ![Aby dodać bibliotek platformy Azure za pomocą narzędzia NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Za pomocą programu Visual Studio, przejdź do repozytorium lokalnego i Otwórz plik rozwiązania **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Do interakcji z usługi Azure Search, konieczne jest adres URL usługi i klucza dostępu. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania, na podstawie danego żądania między aplikacją wysyłającą żądanie i usługi, która je obsługuje.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Przygotowanie przykładowych danych usługi Azure Cosmos DB

W przykładzie użyto dwa zestawy małych dane, które opisują siedem fikcyjnej hotels. Jeden zestaw opisuje hotele, samodzielnie i zostaną załadowane do bazy danych Azure Cosmos DB. Drugi zestaw zawiera szczegóły pokoju w hotelu i jest dostarczana jako siedmiu osobnych plikach JSON do przekazania do usługi Azure Blob Storage.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), a następnie przejdź strony Przegląd konta usługi Azure Cosmos DB.

1. Na pasku menu kliknij pozycję Dodaj kontener. Określ "Utwórz nową bazę danych", a następnie użyj nazwy **hotelu pokojów db**. Wprowadź **pokojach hotelowych** nazwy kolekcji i **/HotelId** dla klucza partycji. Kliknij przycisk **OK** do utworzenia bazy danych i kontenera.

   ![Kontener usługi Azure Cosmos DB Dodaj](media/tutorial-multiple-data-sources/cosmos-add-container.png "Dodaj kontener usługi Azure Cosmos DB")

1. Przejdź do Eksploratora danych Cosmos DB i wybierz pozycję **elementów** pod **hotele** kontener w ramach **hotelu pokojów db** bazy danych. Następnie kliknij przycisk **element Przekaż** na pasku poleceń.

   ![Przekaż do kolekcji usługi Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "przekazać do kolekcji usługi Cosmos DB")

1. W panelu przekazywania, kliknij przycisk folder, a następnie przejdź do pliku **cosmosdb/HotelsDataSubset_CosmosDb.json** w folderze projektu. Kliknij przycisk **OK** aby rozpocząć przekazywanie.

   ![Wybierz plik do przekazania](media/tutorial-multiple-data-sources/cosmos-upload2.png "wybierz plik do przekazania")

1. Użyj przycisku Odśwież, aby odświeżyć widoku elementów w kolekcji hotels. Powinien zostać wyświetlony na liście siedem nowych dokumentów bazy danych.

## <a name="prepare-sample-blob-data"></a>Przygotowanie przykładowych danych obiektów blob

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com), przejdź do swojego konta usługi Azure storage, kliknij przycisk **obiektów blob**, a następnie kliknij przycisk **+ kontener**.

1. [Utwórz kontener obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) o nazwie **pokojach hotelowych** do przechowywania plików JSON pokoju w hotelu przykładowe. Można ustawić poziom dostępu publicznego do dowolnego z jego prawidłowe wartości.

   ![Utwórz kontener obiektów blob](media/tutorial-multiple-data-sources/blob-add-container.png "Utwórz kontener obiektów blob")

1. Po utworzeniu kontenera otwórz go i wybierz **przekazywanie** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "przekazywania na pasku poleceń")

1. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **przekazywanie**.

   ![Przekazywanie plików](media/tutorial-multiple-data-sources/blob-upload.png "przekazywanie plików")

Po zakończeniu przekazywania pliki powinny pojawić się na liście dla kontenera danych.

## <a name="set-up-connections"></a>Konfigurowanie połączeń

Informacje o połączeniu dla usługi wyszukiwania i źródła danych jest określona w **appsettings.json** plików w rozwiązaniu. 

1. W programie Visual Studio, otwórz **AzureSearchMultipleDataSources.sln** pliku.

1. W Eksploratorze rozwiązań, Edytuj **appsettings.json** pliku.  

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

Pierwsze dwie pozycje klawisze administrator i adres URL dla usługi Azure Search. Podany punkt końcowy `https://mydemo.search.windows.net`, na przykład to nazwa usługi w celu zapewnienia `mydemo`.

Następnej pozycji określić nazwy konta i informacje o parametrach połączenia dla usługi Azure Blob Storage i źródeł danych usługi Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Zidentyfikuj klucz dokumentu

W usłudze Azure Search pola klucza jednoznacznie identyfikuje każdy dokument w indeksie. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String`. Pole klucza musi być obecny dla każdego dokumentu w źródle danych, który jest dodawany do indeksu. (W rzeczywistości to jedyne wymagane pole.)

Gdy indeksowanie danych z wielu źródeł danych, wszystkie wartości klucza źródła danych musi być mapowane do tego samego pola klucza w indeksie połączone. Wymaga często, niektóre z góry planowania zidentyfikować klucz dokumentu istotnych dla indeksu i upewnij się, że znajduje się w każdym źródle danych.

Indeksatorów usługi Azure Search służy do zmiany nazwy, a nawet formatowania pól danych w trakcie indeksowania mapowań pól, tak, że źródło danych może zostać skierowany do pola prawidłowy indeks.

Na przykład w naszych przykładowych danych bazy danych cosmos DB, identyfikator hotelu nosi nazwę **HotelId**. Ale w plikach obiektów blob JSON pokojów hotelu, nosi nazwę identyfikatora hotelu **identyfikator**. Program obsługuje to przez mapowanie **identyfikator** pola z obiektów blob do **HotelId** pola klucza w indeksie.

> [!NOTE]
> W większości przypadków klucze generowane automatycznie dokumentu, takich jak te utworzone domyślnie przez niektóre indeksatory nie należy wprowadzać klucze dokumentu dobre dla indeksów połączone. Ogólnie rzecz biorąc chcesz użyć zrozumiałe, unikatowych wartości klucza, który już istnieje w, lub można łatwo dodać do źródła danych.

## <a name="understand-the-code"></a>Zrozumienie kodu

Po danych i ustawień konfiguracji znajdują się w miejscu, próbki programu **AzureSearchMultipleDataSources.sln** powinno być gotowe do kompilowania i uruchamiania.

Prosty C#/Aplikacja konsoli .NET wykonuje następujące zadania:
* Tworzy indeks usługi Azure Search oparty na strukturze danych C# klasy hotelu, (który również odwołuje się do klas adres i miejsca).
* Tworzy źródła danych usługi Azure Cosmos DB i działanie indeksatora, który mapuje dane usługi Azure Cosmos DB na pola indeksu.
* Jest uruchamiany indeksator bazy danych cosmos DB można załadować danych hotelu.
* Tworzy źródła danych usługi Azure Blob Storage i działanie indeksatora, który mapuje dane obiektu JSOn Blob na pola indeksu.
* Jest uruchamiany indeksator usługi Azure blob storage do ładowania danych pokoje.

 Przed uruchomieniem programu należy poświęcić chwilę na zapoznają się z kodem i definicjami indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **Hotel.cs** zawiera schemat definiujący indeks
  + **Plik program.cs** zawiera funkcje, które utworzyć indeks usługi Azure Search, źródła danych i indeksatorów i załadować łączne wyniki do indeksu.

### <a name="define-the-index"></a>Definiowanie indeksu

Ten przykładowy program używa zestawu .NET SDK do definiowania i tworzenie indeksu usługi Azure Search. Wykorzystuje ona [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) klasy do generowania struktury indeksu z C# klasy modelu danych.

Model danych jest definiowany przez klasę hotelu, która również zawiera odwołania do klasy adres i miejsca. FieldBuilder rozwija przez wiele definicji klasy do generowania strukturę złożoną danych dla indeksu. Tagi metadanych są używane do definiowania atrybuty każdego pola, na przykład tego, czy jest wyszukiwanie i sortowanie.

Poniższe fragmenty kodu z **Hotel.cs** pliku pokazują, jak można określić jedno pole i odwołania do innej klasy modelu danych.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

W **Program.cs** pliku indeksu jest zdefiniowana za pomocą nazwy i kolekcji pól generowanych przez `FieldBuilder.BuildForType<Hotel>()` metody, a następnie utworzono w następujący sposób:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Tworzenie źródła danych usługi Azure Cosmos DB i indeksatora

Obok główny program obejmuje logiki, aby utworzyć źródło danych usługi Azure Cosmos DB dla danych hotels.

Najpierw łączy nazwę bazy danych Azure Cosmos DB parametry połączenia. Następnie definiuje obiekt źródła danych, w tym ustawienia specyficzne dla usługi Azure Cosmos DB źródeł, takie jak właściwości [useChangeDetection].

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

Po utworzeniu źródła danych, program konfiguruje indeksator usługi Azure Cosmos DB, o nazwie **hotelu pokojów cosmos indeksator**.

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
Wszelkie istniejące indeksatory o takiej samej nazwie spowoduje usunięcie przed utworzeniem nowego, w przypadku, gdy użytkownik chce uruchomić więcej niż jeden raz w tym przykładzie.

W tym przykładzie definiuje harmonogram dla indeksatora, tak aby było uruchamiane raz dziennie. Jeśli nie chcesz indeksatora, aby automatycznie uruchomić ponownie w przyszłości, możesz usunąć właściwość harmonogram tego wywołania.

### <a name="index-azure-cosmos-db-data"></a>Dane do indeksu usługi Azure Cosmos DB

Po utworzeniu źródła danych i indeksatora kod, który jest uruchamiany indeksator jest krótki opis:

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

W tym przykładzie zawiera blok try-catch — prosty do zgłaszania błędów, które mogą wystąpić podczas wykonywania.

Po uruchomieniu indeksatora usługi Azure Cosmos DB, indeks wyszukiwania będzie zawierać pełny zestaw przykładowych hotelu dokumentów. Jednak pole pokojów dla każdego hotelu będzie pusta tablica, ponieważ źródło danych usługi Azure Cosmos DB zawiera Brak szczegółów miejsca. Następnie program będzie pobierać z usługi Blob storage do ładowania i scalanie danych w pomieszczeniu.

### <a name="create-blob-storage-data-source-and-indexer"></a>Tworzenie źródła danych magazynu obiektów Blob i indeksatora

Aby uzyskać szczegółowe informacje pokoju program najpierw konfiguruje źródła danych magazynu obiektów Blob można się odwoływać do zestawu pojedynczych plików obiektów blob JSON.

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

Po utworzeniu źródła danych, program konfiguruje indeksatora obiektów blob o nazwie **hotelu pokojów indeksatora obiektów blob —** .

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

Obiekty BLOB JSON zawierają pola klucza o nazwie **identyfikator** zamiast **HotelId**. Kod używa `FieldMapping` klasy, aby poinformować indeksatora w celu kierowania **identyfikator** pola wartość **HotelId** klucz dokumentu w indeksie.

Indeksatory magazynu obiektów blob, można użyć parametrów, które identyfikują podczas analizowania trybu ma być używany. Podczas analizowania trybu różni się dla obiektów blob, które reprezentują pojedynczego dokumentu lub wiele dokumentów w ramach tego samego obiektu blob. W tym przykładzie każdy obiekt blob reprezentuje dokumentu jednego indeksu, dzięki czemu kod używa `IndexingParameters.ParseJson()` parametru.

Aby uzyskać więcej informacji na temat analizowania parametrów dla obiektów blob JSON indeksatora, zobacz [obiektów blob JSON indeksu](search-howto-index-json-blobs.md). Aby uzyskać więcej informacji na temat określania tych parametrów przy użyciu zestawu .NET SDK, zobacz [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) klasy.

Wszelkie istniejące indeksatory o takiej samej nazwie spowoduje usunięcie przed utworzeniem nowego, w przypadku, gdy użytkownik chce uruchomić więcej niż jeden raz w tym przykładzie.

W tym przykładzie definiuje harmonogram dla indeksatora, tak aby było uruchamiane raz dziennie. Jeśli nie chcesz indeksatora, aby automatycznie uruchomić ponownie w przyszłości, możesz usunąć właściwość harmonogram tego wywołania.

### <a name="index-blob-data"></a>Dane obiektu blob indeksu

Po utworzeniu źródła danych magazynu obiektów Blob i indeksatora kod, który jest uruchamiany indeksator jest prosty:

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

Ponieważ indeks zawiera już wypełnione hotelu danymi z bazy danych Azure Cosmos DB, indeksatora obiektów blob aktualizacje istniejących dokumentów w indeksie i dodaje szczegóły miejsca.

> [!NOTE]
> Jeśli masz te same pola-key w źródłach danych i danych w ramach tych pól jest niezgodny, indeksem będzie zawierać wartości z indeksatora niezależnie od ostatniego uruchomienia. W tym przykładzie oba źródła danych zawierają **HotelName** pola. Jeśli dowolnej przyczyny dane w to pole jest inna, dla dokumentów za pomocą tej samej wartości klucza, a następnie **HotelName** dane ze źródła danych, który został zindeksowany ostatnio będą wartości zapisane w indeksie.

## <a name="search-your-json-files"></a>Przeszukiwanie plików JSON

Możesz zapoznać się z indeksu wyszukiwania wypełniania po uruchomieniu programu, za pomocą [ **Eksploratora wyszukiwania** ](search-explorer.md) w portalu.

W witrynie Azure portal Otwórz usługę wyszukiwania **Przegląd** strony i Znajdź **hotelu pokojów próby** indeks w **indeksy** listy.

  ![Listy indeksów usługi Azure Search](media/tutorial-multiple-data-sources/index-list.png "indeksów wykaz usługi Azure Search")

Kliknij pozycję indeksu hotelu pokojów próbki na liście. Zostanie wyświetlony interfejs eksploratora wyszukiwania do indeksu. Wprowadź zapytanie dla okresu, takich jak "Luksusowe". Powinien zostać wyświetlony co najmniej jednego dokumentu w wynikach, a w tym dokumencie powinny pokazywać listę obiektów miejsca jego tablica pomieszczeniach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. W portalu nazwę grupy zasobów jest na stronie Przegląd usługi Azure Search.

## <a name="next-steps"></a>Kolejne kroki

Istnieje kilka metod i wiele opcji indeksowanie obiektów blob JSON. Jeśli dane źródłowe zawiera zawartość JSON, możesz przejrzeć te opcje, aby zobaczyć, co sprawdza się najlepiej w przypadku danego scenariusza.

> [!div class="nextstepaction"]
> [Jak indeksować obiektów blob JSON za pomocą indeksatora obiektów Blob platformy Azure Search](search-howto-index-json-blobs.md)

Można rozszerzyć dane strukturalne indeksu z jednego źródła danych cognitively wzbogaconego danymi z obiektów blob bez struktury lub zawartości pełnego tekstu. Następującego samouczka pokazuje, jak używać usług Cognitive Services wraz z usługi Azure Search przy użyciu zestawu .NET SDK.

> [!div class="nextstepaction"]
> [Wywołania interfejsów API usług Cognitive Services w usłudze Azure Search indeksowanie potoku](cognitive-search-tutorial-blob-dotnet.md)
