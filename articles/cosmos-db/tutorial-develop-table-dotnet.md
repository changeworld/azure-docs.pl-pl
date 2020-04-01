---
title: Interfejs API tabeli usługi Azure Cosmos DB przy użyciu standardowego sdk platformy .NET
description: Dowiedz się, jak przechowywać i wysyłać zapytania do danych strukturalnych na koncie interfejsu API tabeli usługi Azure Cosmos DB
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238454"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Rozpoczynanie pracy z interfejsem API tabel usługi Azure Cosmos DB oraz usługą Azure Table Storage przy użyciu zestawu SDK platformy .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Za pomocą interfejsu API tabel usługi Azure Cosmos DB lub magazynu tabel platformy Azure można przechowywać uporządkowane dane NoSQL w chmurze, zapewniając magazyn kluczy/atrybutów ze schematem o mniejszym projekcie. Ponieważ interfejs API tabeli usługi Azure Cosmos DB i magazyn tabel są mniej schematu, jest łatwo dostosować dane w miarę rozwoju potrzeb aplikacji. Za pomocą interfejsu API tabel usługi Azure Cosmos DB lub magazynu tabel można przechowywać elastyczne zestawy danych, takie jak dane użytkownika dla aplikacji sieci web, książki adresowe, informacje o urządzeniu lub inne typy metadanych wymaganych przez usługę. 

W tym samouczku opisano przykład, który pokazuje, jak korzystać z [biblioteki tabel usługi Microsoft Azure Cosmos DB dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) z interfejsem API tabeli usługi Azure Cosmos DB i scenariuszami magazynu tabel platformy Azure. Należy użyć połączenia specyficzne dla usługi platformy Azure. Te scenariusze są badane przy użyciu przykładów języka C#, które ilustrują sposób tworzenia tabel, wstawiania/ aktualizowania danych, zapytań o dane i usuwania tabel.

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia pracy z przykładem wymagane są następujące elementy:

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Biblioteka tabel usługi Microsoft Azure CosmosDB dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) — ta biblioteka jest obecnie dostępna dla platformy .NET Standard i .NET Framework. 

* [Konto interfejsu API tabeli usługi Azure Cosmos DB](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Tworzenie projektu konsoli .NET

W programie Visual Studio utwórz nową aplikację konsoli .NET. Poniższe kroki pokazują, jak utworzyć aplikację konsoli w programie Visual Studio 2019. Biblioteka tabel usługi Azure Cosmos DB można używać w dowolnej aplikacji typu .NET, w tym w usłudze w chmurze platformy Azure lub aplikacji sieci Web oraz w aplikacjach klasycznych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

1. Wybierz **pozycję Plik** > **nowy** > **projekt**.

1. Wybierz pozycję **Aplikacja konsoli (.NET Core),** a następnie wybierz pozycję **Dalej**.

1. W polu **Nazwa projektu** wprowadź nazwę aplikacji, taką jak **CosmosTableSamples**. (W razie potrzeby można podać inną nazwę).

1. Wybierz **pozycję Utwórz**.

Wszystkie przykłady kodu w tym przykładzie można dodać do Main() metody aplikacji konsoli **Program.cs** pliku.

## <a name="install-the-required-nuget-package"></a>Zainstaluj wymagany pakiet NuGet

Aby uzyskać pakiet NuGet, wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wyszukaj [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)w [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) trybie online [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), i wybierz pozycję **Zainstaluj,** aby zainstalować bibliotekę tabel Usługi Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

1. Z [witryny Azure Portal](https://portal.azure.com/)przejdź do konta usługi Azure Cosmos lub usługi Usługi Magazyn tabel. 

1. Otwórz **okienko Ciąg połączenia** lub **Klawisze dostępu.** Użyj przycisku kopiowania po prawej stronie okna, aby skopiować **PODSTAWOWE PARAMETRY POŁĄCZENIA**.

   ![Wyświetlanie i kopiowanie PODSTAWOWYCH PARAMETRÓW POŁĄCZENIA w okienku parametrów połączenia](./media/create-table-dotnet/connection-string.png)
   
1. Aby skonfigurować parametry połączenia, z programu Visual Studio kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**.

1. Wybierz **pozycję Dodaj,** a następnie **pozycję Nowy element**. Utwórz nowy plik **Settings.json** z typem pliku jako **TypeScript JSON Configuration** File. 

1. Zastąp kod w pliku Settings.json następującym kodem i przypisz podstawowy ciąg połączenia:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kliknij prawym przyciskiem myszy swój projekt **CosmosTableSamples**. Wybierz **pozycję Dodaj**, Nowy **element** i dodaj klasę o nazwie **AppSettings.cs**.

1. Dodaj następujący kod do pliku AppSettings.cs. Ten plik odczytuje parametry połączenia z pliku Settings.json i przypisuje go do parametru konfiguracji:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analizuj i sprawdzaj poprawność szczegółów połączenia 

1. Kliknij prawym przyciskiem myszy swój projekt **CosmosTableSamples**. Wybierz **pozycję Dodaj**, Nowy **element** i dodaj klasę o nazwie **Common.cs**. Zostaniesz wpisanym kod, aby sprawdzić poprawność szczegółów połączenia i utworzyć tabelę w tej klasie.

1. Zdefiniuj metodę, `CreateStorageAccountFromConnectionString` jak pokazano poniżej. Ta metoda będzie analizować szczegóły ciągu połączenia i sprawdzić, czy nazwa konta i szczegóły klucza konta podane w pliku "Settings.json" są prawidłowe. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Tworzenie tabeli 

Klasa [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) umożliwia pobieranie tabel i jednostek przechowywanych w usłudze Table Storage. Ponieważ nie mamy żadnych tabel na koncie interfejsu API tabeli usługi `CreateTableAsync` Cosmos DB, dodajmy metodę do klasy **Common.cs,** aby utworzyć tabelę:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

Jeśli zostanie wyświetlony błąd "Wyjątek niedostępny dla usługi 503", możliwe, że wymagane porty dla trybu łączności są blokowane przez zaporę. Aby rozwiązać ten problem, otwórz wymagane porty lub użyj łączności w trybie bramy, jak pokazano w poniższym kodzie:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Zdefiniuj encję 

Jednostki mapują do obiektów języka C# przy użyciu niestandardowej klasy uzyskanej od [tableentity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki.

Kliknij prawym przyciskiem myszy swój projekt **CosmosTableSamples**. Wybierz **pozycję Dodaj**, Nowy **folder** i nazwij go jako **Model**. W folderze Model dodaj klasę o nazwie **CustomerEntity.cs** i dodaj do niej następujący kod.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Ten kod definiuje klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwisko jako klucz partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji można wyszukiwać szybciej niż jednostki z różnymi kluczami partycji, ale przy użyciu różnych kluczy partycji pozwala na większą skalowalność operacji równoległych. Jednostki, które mają być przechowywane w tabelach, muszą być obsługiwanego typu, na przykład muszą pochodzić od klasy [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Właściwości jednostki, które zamierzasz przechowywać w tabeli, muszą być publicznymi właściwościami typu i obsługiwać zarówno pobieranie, jak i ustawianie wartości. Ponadto typ jednostki musi ujawniać konstruktor bez parametrów.

## <a name="insert-or-merge-an-entity"></a>Wstawianie lub scalanie elementu

Poniższy przykład kodu tworzy obiekt jednostki i dodaje go do tabeli. InsertOrMerge Metoda w [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) klasy służy do wstawiania lub scalania jednostki. [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) metoda jest wywoływana do wykonania operacji. 

Kliknij prawym przyciskiem myszy swój projekt **CosmosTableSamples**. Wybierz **pozycję Dodaj**, Nowy **element** i dodaj klasę o nazwie **SamplesUtils.cs**. Ta klasa przechowuje cały kod wymagany do wykonywania operacji CRUD na jednostkach. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Pobierz jednostkę z partycji

Jednostki można pobrać z partycji przy użyciu Retrieve metody w [tableoperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) klasy. Poniższy przykład kodu pobiera klucz wiersza klucza partycji, adres e-mail i numer telefonu jednostki klienta. W tym przykładzie również wydrukuje jednostek żądania używane do kwerendy dla jednostki. Aby zbadać encję, dołącz następujący kod, aby **SamplesUtils.cs** plik: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Można z łatwością usunąć jednostkę po jej pobraniu, korzystając z tego samego wzorca co w przypadku aktualizowania jednostki. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta. Aby usunąć encję, dołącz następujący kod, aby **SamplesUtils.cs** plik: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Wykonywanie operacji CRUD na przykładowych danych

Po zdefiniowaniu metod tworzenia tabeli, wstawiania lub scalania jednostek uruchom te metody na przykładowych danych. Aby to zrobić, kliknij prawym przyciskiem myszy swój projekt **CosmosTableSamples**. Wybierz **pozycję Dodaj**, Nowy **element** i dodaj klasę o nazwie **BasicSamples.cs** i dodaj do niej następujący kod. Ten kod tworzy tabelę, dodaje do niej jednostki. Jeśli chcesz usunąć jednostkę i tabelę na końcu projektu, usuń komentarze `table.DeleteIfExistsAsync()` i `SamplesUtils.DeleteEntityAsync(table, customer)` metody z następującego kodu:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Poprzedni kod tworzy tabelę, która zaczyna się od "demo", a wygenerowany identyfikator GUID jest dołączany do nazwy tabeli. Następnie dodaje jednostkę klienta z imieniem i nazwiskiem jako "Harp Walter", a następnie aktualizuje numer telefonu tego użytkownika. 

W tym samouczku został utworzony kod do wykonywania podstawowych operacji CRUD na danych przechowywanych na koncie interfejsu API tabeli. Można również wykonać zaawansowane operacje, takie jak — wsadowe wstawianie danych, zapytanie wszystkich danych w obrębie partycji, kwerenda zakres danych w ramach partycji, Listy tabel na koncie, którego nazwy zaczynają się od określonego prefiksu. Możesz pobrać kompletny przykładowy formularz [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) repozytorium GitHub. Klasa [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) ma więcej operacji, które można wykonać na danych.  

## <a name="run-the-project"></a>Uruchamianie projektu

Z projektu **CosmosTableSamples**. Otwórz klasę o nazwie **Program.cs** i dodaj do niej następujący kod do wywoływania BasicSamples po uruchomieniu projektu.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Teraz skompiluj rozwiązanie i naciśnij klawisz F5, aby uruchomić projekt. Po uruchomieniu projektu w wierszu polecenia zostaną wyświetlne następujące dane wyjściowe:

![Wyjście z wiersza polecenia](./media/tutorial-develop-table-standard/output-from-sample.png)

Jeśli podczas uruchamiania projektu zostanie wyświetlony komunikat o błędzie pliku Settings.json, można go rozwiązać, dodając do ustawień projektu następujący wpis XML. Kliknij prawym przyciskiem myszy cosmostablepróby, wybierz pozycję Edytuj CosmosTableSamples.csproj i dodaj następujący elementGrupa: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Teraz możesz zalogować się do witryny Azure portal i sprawdzić, czy dane istnieją w tabeli. 

![Wyniki w portalu](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Następne kroki

Teraz możesz przejść do następnego samouczka i dowiedzieć się, jak przeprowadzić migrację danych do konta interfejsu API tabeli usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak zbadać dane](../cosmos-db/table-import.md)
