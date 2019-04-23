---
title: Rozpoczynanie pracy z usługą Azure Cosmos DB interfejsu API tabel przy użyciu standardowego zestawu SDK platformy .NET
description: Store ustrukturyzowanych danych w chmurze przy użyciu interfejsu API tabeli usługi Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 03/11/2019
ms.openlocfilehash: f2f207b62522ceef9fe72d47026f4c2f8ed02e3b
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149601"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB i Azure Table storage przy użyciu zestawu .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Za pomocą interfejsu API tabeli usługi Azure Cosmos DB lub Azure Table storage do przechowywania strukturalnych NoSQL, przechowywania danych w chmurze, zapewniając klucz atrybut, ze schematem mniej projektu. Ponieważ interfejsu API tabeli usługi Azure Cosmos DB i magazynu Table storage schematu mniej, jest można łatwo zaadaptować dane aplikacji rozwijających się potrzeb. Interfejs API usługi Azure Cosmos DB Table lub Table storage umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji sieci web, książek adresowych, informacji o urządzeniu lub inne typy metadanych, których wymaga Twoja usługa. 

W tym samouczku opisano przykład pokazujący, jak używać [Microsoft Azure Cosmos DB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) interfejsu API tabeli usługi Azure kosmetyczka DB i Azure Table storage używany w scenariuszach. Należy użyć połączenia specyficzne dla usługi platformy Azure. Te scenariusze są przedstawione przy użyciu C# przykłady ilustrujące sposób tworzenia tabel, wstawiania / aktualizowania danych, wysyłania zapytań dotyczących danych i usuwania tabel.

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia pracy z przykładem wymagane są następujące elementy:

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure cosmos DB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) — Ta biblioteka jest obecnie dostępna dla platformy .NET Standard i .NET framework. 

* [Konto usługi Azure Cosmos DB — interfejs API tabel](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Utwórz projekt konsoli .NET

W programie Visual Studio Utwórz nową aplikację konsoli .NET. W poniższej procedurze pokazano, jak utworzyć aplikację konsolową w programie Visual Studio 2017. Procedura jest podobna w innych wersjach programu Visual Studio. Można użyć biblioteki tabeli usługi Azure Cosmos DB w rodzaju aplikacji .NET, w tym platformy Azure w chmurze usługi lub aplikacji sieci web, a aplikacje klasyczne i mobilne. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

1. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.

1. Wybierz **zainstalowane** > **Visual C#**   >  **konsoli aplikacji (.NET Core)**.

1. W **nazwa** wprowadź nazwę aplikacji, na przykład **CosmosTableSamples** (możesz podać inną nazwę zgodnie z potrzebami).

1. Kliknij przycisk **OK**.

Wszystkie przykłady kodu, w tym przykładzie można dodać do metody Main() całej aplikacji konsolowej **Program.cs** pliku.

## <a name="install-the-required-nuget-package"></a>Instalowanie wymaganych pakietów NuGet

Aby uzyskać pakiet NuGet, wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wyszukaj w trybie online `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` i wybierz **zainstalować** zainstalować biblioteki Microsoft Azure Cosmos DB Table.

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

1. Z [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **parametry połączenia**. Użyj przycisku kopiowania po prawej stronie okna, aby skopiować **PODSTAWOWE PARAMETRY POŁĄCZENIA**.

   ![Wyświetlanie i kopiowanie PODSTAWOWYCH PARAMETRÓW POŁĄCZENIA w okienku parametrów połączenia](./media/create-table-dotnet/connection-string.png)
   
1. Aby skonfigurować parametry połączenia, z programu visual studio kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**.

1. Wybierz **Dodaj** i następnie **nowy element**. Utwórz nowy plik **Settings.json** z typem pliku jako **konfiguracji JSON TypeScript** pliku. 

1. Zastąp kod w pliku Settings.json następującym kodem i przypisać swoje podstawowe parametry połączenia:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kliknij prawym przyciskiem myszy nad projektem **CosmosTableSamples**. Wybierz **Dodaj**, **nowy element** i Dodaj klasę o nazwie **AppSettings.cs**.

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

## <a name="parse-and-validate-the-connection-details"></a>Analizowanie i zweryfikować informacje dotyczące połączenia 

1. Kliknij prawym przyciskiem myszy nad projektem **CosmosTableSamples**. Wybierz **Dodaj**, **nowy element** i Dodaj klasę o nazwie **Common.cs**. Możesz napisać kod, aby sprawdzić szczegóły połączenia i utworzenia tabeli w ramach tej klasy.

1. Definiowanie metody `CreateStorageAccountFromConnectionString` jak pokazano poniżej. Ta metoda będzie analizować szczegóły parametrów połączenia i Zweryfikuj, że nazwa konta i szczegóły klucza konta z pliku "Settings.json" są prawidłowe. 

   ```csharp
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
   ```


## <a name="create-a-table"></a>Tworzenie tabeli 

Klasa [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.cloudtableclient?redirectedfrom=MSDN&view=azure-dotnet) umożliwia pobieranie tabel i jednostek przechowywanych w usłudze Table Storage. Ponieważ firma Microsoft nie ma żadnych tabel w ramach konta interfejsu API tabeli usługi Cosmos DB, możemy dodać `CreateTableAsync` metody **Common.cs** klasy w celu utworzenia tabeli:

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

## <a name="define-the-entity"></a>Zdefiniuj jednostki 

Jednostki są mapowane na C# obiektów przy użyciu niestandardowej klasy pochodne [TableEntity](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx). Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki.

Kliknij prawym przyciskiem myszy nad projektem **CosmosTableSamples**. Wybierz **Dodaj**, **nowy Folder** i nadaj mu jako **modelu**. W folderze modelu należy dodać klasę o nazwie **CustomerEntity.cs** i Dodaj następujący kod do niego.

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

Ten kod definiuje klasę jednostki, która używa imienia klienta jako klucz wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, ale użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Jednostki, które mają być przechowywane w tabelach muszą być obsługiwanego typu, na przykład pochodną [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableentity?redirectedfrom=MSDN&view=azure-dotnet) klasy. Właściwości jednostki, które zamierzasz przechowywać w tabeli, muszą być publicznymi właściwościami typu i obsługiwać zarówno pobieranie, jak i ustawianie wartości. Ponadto typ jednostki musi ujawniać konstruktor bez parametrów.

## <a name="insert-or-merge-an-entity"></a>Wstawianie lub scalić jednostki

Poniższy przykład kodu tworzy obiekt jednostki i dodaje go do tabeli. Metoda InsertOrMerge w ramach [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableoperation?redirectedfrom=MSDN&view=azure-dotnet) klasa służy do wstawiania lub scalić jednostki. [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.cloudtable.executeasync?view=azure-dotnet) metoda jest wywoływana, aby wykonać operację. 

Kliknij prawym przyciskiem myszy nad projektem **CosmosTableSamples**. Wybierz **Dodaj**, **nowy element** i Dodaj klasę o nazwie **SamplesUtils.cs**. Ta klasa przechowuje całego kodu, które są wymagane do wykonywania operacji CRUD na jednostkach. 

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
        
        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
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

### <a name="get-an-entity-from-a-partition"></a>Pobieranie jednostki z partycji

Jednostki można uzyskać z partycji, przy użyciu metody pobierania w obszarze [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableoperation?redirectedfrom=MSDN&view=azure-dotnet) klasy. Poniższy przykładowy kod pobiera klucz wiersz klucza partycji, poczty e-mail i numer telefonu jednostki klient. W tym przykładzie drukuje również limit jednostek żądania, używane do wykonywania zapytań dla tej jednostki. Aby wysłać zapytanie do jednostki, Dołącz następujący kod, aby **SamplesUtils.cs** pliku: 

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

Można z łatwością usunąć jednostkę po jej pobraniu, korzystając z tego samego wzorca co w przypadku aktualizowania jednostki. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta. Aby usunąć jednostkę, należy dołączyć następujący kod, aby **SamplesUtils.cs** pliku: 

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

Po zdefiniowaniu metody tworzenia jednostki z tabeli, wstawianie lub scalania, należy uruchomić te metody na przykładowych danych. Aby to zrobić, kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz **Dodaj**, **nowy element** i Dodaj klasę o nazwie **plik BasicSamples.cs** i Dodaj następujący kod do niego. Ten kod tworzy tabelę, dodaje jednostki do niego. Jeśli chcesz usunąć jednostkę i tabeli na końcu projektu Usuń komentarze z `table.DeleteIfExistsAsync()` i `SamplesUtils.DeleteEntityAsync(table, customer)` metody z następującego kodu:

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

Powyższy kod tworzy tabelę, która rozpoczyna się od "pokaz" i generowanym identyfikatorze GUID jest dołączana do nazwy tabeli. Go następnie doda jednostki klienta za pomocą imienia i nazwiska jako "Waltera Harpa", a później aktualizuje numer telefonu użytkownika. 

W ramach tego samouczka skompilowane jest kod, aby wykonywać podstawowe operacje CRUD na danych przechowywanych na koncie usługi interfejsu API tabel. Można również wykonywać zaawansowane operacje takie jak — dane Wstawianie wsadowe, zapytania wszystkich danych na partycji zapytania szeroką gamę danych na partycji, listy tabel na koncie, których nazwy zaczynają się od określonego prefiksu. Możesz pobrać kompletny przykładowy formularz [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) repozytorium GitHub. [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) klasa ma więcej operacji, które można wykonać na danych.  

## <a name="run-the-project"></a>Uruchamianie projektu

Teraz skompilować rozwiązanie i naciśnij klawisz F5, aby uruchomić projekt. Po uruchomieniu projektu, zobaczysz następujące dane wyjściowe w wierszu polecenia:

![Dane wyjściowe z wiersza polecenia](./media/tutorial-develop-table-standard/output-from-sample.png)

Jeśli otrzymasz komunikat o błędzie, informujący, że nie można odnaleźć pliku Settings.json, podczas uruchamiania projektu, można rozwiązać go, dodając następujący wpis XML do ustawień projektu. Kliknij prawym przyciskiem myszy CosmosTableSamples, wybierz pozycję Edytuj CosmosTableSamples.csproj i Dodaj następujący element itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Teraz możesz zalogować się do witryny Azure portal i sprawdź, czy dane istnieje w tabeli. 

![Wyniki w portalu](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Kolejne kroki

Możesz teraz przejść do następnego samouczka i dowiedzieć się, jak przeprowadzić migrację danych do konta interfejsu API tabeli usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak wykonywać zapytania o dane](../cosmos-db/table-import.md)
