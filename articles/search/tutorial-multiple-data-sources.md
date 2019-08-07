---
title: C#Ręczny Indeksowanie wielu źródeł danych — Azure Search
description: Dowiedz się, jak importować dane z wielu źródeł danych do jednego indeksu Azure Search.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: heidist
ms.openlocfilehash: 2776bcd095c570be500fa6ddb2675820b2259716
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840678"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Ręczny Łączenie danych z wielu źródeł danych w jednym indeksie Azure Search

Azure Search umożliwia importowanie, analizowanie i indeksowanie danych z wielu źródeł danych w jednym połączonym indeksie wyszukiwania. Obsługuje to sytuacje, w których dane strukturalne są agregowane przy użyciu mniej strukturalnych lub nawet danych zwykłego tekstu z innych źródeł, takich jak tekst, HTML lub dokumenty JSON.

W tym samouczku opisano sposób indeksowania danych hotelu ze źródła danych Azure Cosmos DB i scalania z informacjami o pokoju hotelowym pobranymi z dokumentów Blob Storage platformy Azure. Wynik będzie połączonym indeksem wyszukiwania hotelowego zawierającym złożone typy danych.

Ten samouczek używa C#programu, zestawu .NET SDK dla Azure Search i Azure Portal do wykonywania następujących zadań:

> [!div class="checklist"]
> * Przekazywanie przykładowych danych i tworzenie źródeł danych
> * Identyfikowanie klucza dokumentu
> * Definiowanie i tworzenie indeksu
> * Indeksuj dane hotelu z Azure Cosmos DB
> * Scalanie danych pokoju hotelowego z magazynu obiektów BLOB

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki Start są używane następujące usługi, narzędzia i dane. 

- [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi.

- [Utwórz konto Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych hotelowych.

- [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) na potrzeby przechowywania danych przykładowych obiektów BLOB JSON.

- [Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) , aby użyć go jako środowiska IDE.

### <a name="install-the-project-from-github"></a>Instalowanie projektu z usługi GitHub

1. Znajdź przykładowe repozytorium w usłudze GitHub: [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Wybierz pozycję **Klonuj lub Pobierz** , aby utworzyć prywatną kopię lokalną repozytorium.
1. Otwórz program Visual Studio i zainstaluj pakiet NuGet wyszukiwania Microsoft Azure, jeśli nie został jeszcze zainstalowany. W menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet** , a następnie **Zarządzaj pakietami NuGet dla rozwiązania..** .. Wybierz kartę **Przeglądaj** , a następnie wpisz ciąg "Azure Search" w polu wyszukiwania. Zainstaluj **program Microsoft. Azure. Search** , gdy zostanie wyświetlony na liście (w wersji 9.0.1 lub nowszej). Aby ukończyć instalację, trzeba będzie kliknąć dodatkowe okna dialogowe.

    ![Dodawanie bibliotek platformy Azure przy użyciu narzędzia NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Korzystając z programu Visual Studio, przejdź do lokalnego repozytorium i Otwórz plik rozwiązania **AzureSearchMultipleDataSources. sln**.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Aby można było korzystać z usługi Azure Search, wymagany jest adres URL usługi i klucz dostępu. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego http i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego http i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia zaufanie dla każdego żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Przygotowywanie przykładowych danych Azure Cosmos DB

Ten przykład używa dwóch małych zestawów danych, które opisują siedem fikcyjnych hoteli. Jeden zestaw opisuje same hotele i zostanie załadowany do bazy danych Azure Cosmos DB. Drugi zestaw zawiera szczegółowe informacje o pokoju hotelowym i jest dostarczany jako siedem oddzielnych plików JSON do przekazania do Blob Storage platformy Azure.

1. [Zaloguj się do Azure Portal](https://portal.azure.com), a następnie przejdź do strony przegląd konta Azure Cosmos DB.

1. Na pasku menu kliknij pozycję Dodaj kontener. Określ wartość "Utwórz nową bazę danych" i użyj nazwy " **pokoje hotelowe-DB**". Wprowadź **pokoje hotelowe** dla nazwy kolekcji i **/HotelId** dla klucza partycji. Kliknij przycisk **OK** , aby utworzyć bazę danych i kontener.

   ![Dodaj kontener Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-add-container.png "Dodawanie kontenera Azure Cosmos DB")

1. Przejdź do Eksplorator danych Cosmos DB i wybierz element **Items** w kontenerze **Hotele** w bazie danych **pokojów hotelowych** . Następnie kliknij przycisk **Przekaż element** na pasku poleceń.

   ![Przekaż do kolekcji Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Przekaż do kolekcji Cosmos DB")

1. W panelu przekazywania kliknij przycisk folder, a następnie przejdź do pliku **cosmosdb/HotelsDataSubset_CosmosDb. JSON** w folderze projektu. Kliknij przycisk **OK** , aby rozpocząć przekazywanie.

   ![Wybierz plik do przekazania](media/tutorial-multiple-data-sources/cosmos-upload2.png "Wybierz plik do przekazania")

1. Użyj przycisku Odśwież, aby odświeżyć widok elementów w kolekcji hoteli. Powinien być widoczny na liście siedem nowych dokumentów bazy danych.

## <a name="prepare-sample-blob-data"></a>Przygotowywanie przykładowych danych obiektów BLOB

1. [Zaloguj się do Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) o nazwie **pokoje hotelowe** , aby zapisać przykładowe pliki JSON pokoju hotelowego. Można ustawić poziom dostępu publicznego na dowolną z jego prawidłowych wartości.

   ![Tworzenie kontenera obiektów BLOB](media/tutorial-multiple-data-sources/blob-add-container.png "Tworzenie kontenera obiektów BLOB")

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń.

   ![Przekaż na pasku poleceń](media/search-semi-structured-data/upload-command-bar.png "Przekaż na pasku poleceń")

1. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   ![Przekaż pliki](media/tutorial-multiple-data-sources/blob-upload.png "Przekaż pliki")

Po zakończeniu przekazywania pliki powinny pojawić się na liście kontenera danych.

## <a name="set-up-connections"></a>Konfigurowanie połączeń

Informacje o połączeniu dla usługi wyszukiwania i źródeł danych są określone w pliku **appSettings. JSON** w rozwiązaniu. 

1. W programie Visual Studio Otwórz plik **AzureSearchMultipleDataSources. sln** .

1. W Eksplorator rozwiązań Edytuj plik **appSettings. JSON** .  

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

Pierwsze dwa wpisy używają adresu URL i kluczy administracyjnych dla usługi Azure Search. Załóżmy na przykład, że nazwa usługi jest `mydemo`określona. `https://mydemo.search.windows.net`

Następne wpisy określają nazwy kont i informacje o parametrach połączenia dla Blob Storage platformy Azure i Azure Cosmos DB źródeł danych.

### <a name="identify-the-document-key"></a>Identyfikowanie klucza dokumentu

W Azure Search pole klucza jednoznacznie identyfikuje każdy dokument w indeksie. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String`. Pole klucza musi być obecne dla każdego dokumentu w źródle danych, które jest dodawane do indeksu. (W rzeczywistości jest to jedyne pole wymagane).

Podczas indeksowania danych z wielu źródeł danych każda wartość klucza źródła danych musi być mapowana na to samo pole klucza w połączonym indeksie. Często wymaga to pewnej planowania z góry, aby zidentyfikować istotny klucz dokumentu dla indeksu, i upewnić się, że istnieje w każdym źródle danych.

Azure Search indeksatorów mogą używać mapowań pól do zmiany nazw i nawet ponownego formatowania pól danych podczas procesu indeksowania, dzięki czemu dane źródłowe mogą być kierowane do poprawnego pola indeksu.

Na przykład w naszym przykładzie Azure Cosmos DB dane, identyfikator hotelu ma nazwę **HotelId**. Jednak w plikach obiektów BLOB JSON dla pokojów hotelowych identyfikator hotelu ma nazwę **ID**. Program obsługuje to przez mapowanie pola **identyfikatora** z obiektów BLOB do pola klucza **HotelId** w indeksie.

> [!NOTE]
> W większości przypadków klucze dokumentu generowane automatycznie, takie jak te utworzone domyślnie przez niektórych indeksatorów, nie należy tworzyć prawidłowych kluczy dokumentów dla połączonych indeksów. Ogólnie rzecz biorąc, należy użyć znaczącej, unikatowej wartości klucza, która już istnieje w programie, lub można ją łatwo dodać do źródła danych.

## <a name="understand-the-code"></a>Zrozumienie kodu

Po wprowadzeniu ustawień danych i konfiguracji, przykładowy program w **AzureSearchMultipleDataSources. sln** powinien być gotowy do kompilowania i uruchamiania.

Ta prosta C#Aplikacja konsolowa/.NET wykonuje następujące zadania:
* Tworzy nowy indeks Azure Search na podstawie struktury danych klasy C# hotelu (która odwołuje się również do klas adresów i pokojów).
* Tworzy Azure Cosmos DB źródło danych i indeksator, który mapuje Azure Cosmos DB dane na pola indeksu.
* Uruchamia indeksator Azure Cosmos DB w celu załadowania danych hotelu.
* Tworzy źródło danych usługi Azure Blob Storage i indeksator, który mapuje dane obiektów BLOB JSON do pól indeksu.
* Uruchamia indeksator usługi Azure Blob Storage w celu załadowania danych pokojów.

 Przed uruchomieniem programu Poświęć minutę na przeanalizowanie kodu i definicji indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **Hotel.cs** zawiera schemat definiujący indeks
  + **Program.cs** zawiera funkcje, które tworzą indeks Azure Search, źródła danych i indeksatory, a następnie Ładuj połączone wyniki do indeksu.

### <a name="define-the-index"></a>Definiowanie indeksu

Ten przykładowy program używa zestawu .NET SDK do definiowania i tworzenia indeksu Azure Search. Wykorzystuje klasę [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) , aby wygenerować strukturę indeksu z klasy modelu C# danych.

Model danych jest definiowany przez klasę hotelu, która również zawiera odwołania do klas Address i Room. FieldBuilder przechodzi przez wiele definicji klas w celu wygenerowania złożonej struktury danych dla indeksu. Tagi metadanych są używane do definiowania atrybutów poszczególnych pól, na przykład czy można je przeszukiwać czy sortować.

Poniższe fragmenty kodu z pliku **Hotel.cs** pokazują, jak można określić jedno pole i odwołanie do innej klasy modelu danych.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

W pliku **program.cs** indeks jest zdefiniowany przy użyciu nazwy i kolekcji pól wygenerowanej przez `FieldBuilder.BuildForType<Hotel>()` metodę, a następnie utworzony w następujący sposób:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Tworzenie Azure Cosmos DB źródła danych i indeksatora

Następny program główny zawiera logikę, aby utworzyć Azure Cosmos DB źródło danych dla danych hoteli.

Najpierw łączy nazwę bazy danych Azure Cosmos DB z parametrami połączenia. Następnie definiuje obiekt źródła danych, w tym ustawienia specyficzne dla źródeł Azure Cosmos DB, takich jak Właściwość [useChangeDetection].

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

Po utworzeniu źródła danych program skonfiguruje Azure Cosmos DB indeksatora o nazwie **pokoje hotelowe-Cosmos-Indexer**.

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
Program usunie wszystkie istniejące indeksatory o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż raz.

Ten przykład definiuje harmonogram dla indeksatora, tak aby był uruchamiany raz dziennie. Możesz usunąć Właściwość Schedule z tego wywołania, jeśli nie chcesz, aby indeksator został automatycznie uruchomiony ponownie w przyszłości.

### <a name="index-azure-cosmos-db-data"></a>Indeksuj Azure Cosmos DB dane

Po utworzeniu źródła danych i indeksatora kod, który uruchamia indeksator, jest krótki:

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

Ten przykład zawiera prosty blok try-catch do zgłaszania błędów, które mogą wystąpić podczas wykonywania.

Po uruchomieniu indeksatora Azure Cosmos DB indeks wyszukiwania będzie zawierał pełny zestaw przykładowych dokumentów hotelowych. Jednak pole pokojów dla każdego hotelu będzie pustą tablicę, ponieważ źródło danych Azure Cosmos DB nie zawierało szczegółów pokoju. Następnie program pobierze z usługi BLOB Storage w celu załadowania i scalenia danych pokoju.

### <a name="create-blob-storage-data-source-and-indexer"></a>Tworzenie źródła danych i indeksatora magazynu obiektów BLOB

Aby uzyskać szczegółowe informacje o pokoju, program najpierw konfiguruje źródło danych magazynu obiektów blob, aby odwołać się do zestawu poszczególnych plików obiektów BLOB JSON.

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

Po utworzeniu źródła danych program skonfiguruje obiekt BLOB indeksatora o nazwie **sale**-Indexer.

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

Obiekty blob JSON zawierają pole klucza o nazwie **ID** zamiast **HotelId**. Kod używa klasy, `FieldMapping` aby określić indeksator do skierowania wartości pola **ID** do klucza dokumentu **HotelId** w indeksie.

Indeksatory magazynu obiektów BLOB mogą używać parametrów, które identyfikują tryb analizowania, który ma być używany. Tryb analizy różni się dla obiektów BLOB reprezentujących pojedynczy dokument lub wiele dokumentów w ramach tego samego obiektu BLOB. W tym przykładzie każdy obiekt BLOB reprezentuje pojedynczy dokument indeksu, więc kod używa `IndexingParameters.ParseJson()` parametru.

Aby uzyskać więcej informacji na temat parametrów analizy indeksatora dla obiektów BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md). Aby uzyskać więcej informacji na temat określania tych parametrów przy użyciu zestawu .NET SDK, zobacz Klasa [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

Program usunie wszystkie istniejące indeksatory o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż raz.

Ten przykład definiuje harmonogram dla indeksatora, tak aby był uruchamiany raz dziennie. Możesz usunąć Właściwość Schedule z tego wywołania, jeśli nie chcesz, aby indeksator został automatycznie uruchomiony ponownie w przyszłości.

### <a name="index-blob-data"></a>Indeksuj dane obiektów BLOB

Po utworzeniu źródła danych i indeksatora magazynu obiektów BLOB kod, który uruchamia indeksator, jest prosty:

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

Ponieważ indeks został już wypełniony danymi hotelu z bazy danych Azure Cosmos DB, indeksator obiektu BLOB aktualizuje istniejące dokumenty w indeksie i dodaje szczegóły pokoju.

> [!NOTE]
> Jeśli masz te same pola, które nie są kluczami w obu źródłach danych, a dane w tych polach nie są zgodne, indeks będzie zawierał wartości z dowolnego indeksatora. W naszym przykładzie oba źródła danych zawierają pole **hotelname** . Jeśli z jakiegoś powodu dane w tym polu różnią się w przypadku dokumentów o tej samej wartości klucza, dane **hoteluname** ze źródła danych, które było ostatnio indeksowane, będą wartościami przechowywanymi w indeksie.

## <a name="search-your-json-files"></a>Przeszukiwanie plików JSON

Można eksplorować wypełniony indeks wyszukiwania po uruchomieniu programu przy użyciu [**Eksploratora wyszukiwania**](search-explorer.md) w portalu.

W Azure Portal Otwórz stronę **Przegląd** usługi wyszukiwania i Znajdź na liście **indeksów** pozycję **Pokój** z przemieszczeń hotelowych.

  ![Lista indeksów Azure Search](media/tutorial-multiple-data-sources/index-list.png "Lista indeksów Azure Search")

Na liście kliknij indeks pokojów hotelowych — przykład. Zostanie wyświetlony interfejs Eksploratora wyszukiwania dla indeksu. Wprowadź zapytanie dla terminu takiego jak "możliwość zaprojektowania". W wynikach powinien być widoczny co najmniej jeden dokument, a ten dokument powinien zawierać listę obiektów Room w tablicy pokojów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. Nazwa grupy zasobów w portalu znajduje się na stronie Przegląd usługi Azure Search.

## <a name="next-steps"></a>Następne kroki

Istnieje kilka metod i wiele opcji indeksowania obiektów BLOB JSON. Jeśli dane źródłowe zawierają zawartość JSON, możesz przejrzeć te opcje, aby zobaczyć, co najlepiej sprawdza się w danym scenariuszu.

> [!div class="nextstepaction"]
> [Jak indeksować obiekty blob JSON przy użyciu Azure Search indeksatora obiektów BLOB](search-howto-index-json-blobs.md)

Możesz chcieć rozszerzyć strukturalne dane indeksów z jednego źródła danych, dzięki czemu dane są wzbogacone z nieuporządkowanymi obiektami BLOB lub z treści pełnotekstowych. W poniższym samouczku pokazano, jak używać Cognitive Services razem z Azure Search przy użyciu zestawu .NET SDK.

> [!div class="nextstepaction"]
> [Wywołaj interfejsy API usług Cognitive Services w potoku indeksowania Azure Search](cognitive-search-tutorial-blob-dotnet.md)
