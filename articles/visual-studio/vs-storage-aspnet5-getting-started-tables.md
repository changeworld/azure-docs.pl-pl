---
title: Jak rozpocząć pracę z magazynu tabel i Visual Studio połączone usługi (platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z magazynem tabel Azure w projekcie platformy ASP.NET Core w programie Visual Studio po połączeniu z kontem magazynu za pomocą programu Visual Studio połączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e53e8ed27cfc048f24bda4ef92fcd2a50a85ed07
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak rozpocząć pracę z magazynem tabel Azure i programu Visual Studio połączone usługi

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

W tym artykule opisano sposób rozpoczęcie pracy z magazynem tabel Azure w programie Visual Studio po utworzony lub odwołanie do konta magazynu Azure w projekcie platformy ASP.NET Core za pomocą programu Visual Studio **usług połączonych** funkcji. **Usług połączonych** operacji instaluje odpowiednie pakiety NuGet dostęp do magazynu Azure do projektu i dodaje ten ciąg połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentacji magazynu](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje na temat usługi Azure Storage.)

Usługa Azure Table storage umożliwia przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz lub na zewnątrz w chmurze Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych. Aby uzyskać więcej ogólnych informacji o korzystaniu z magazynem tabel Azure, zobacz [Rozpoczynanie pracy z magazynem tabel Azure przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

Aby rozpocząć, najpierw utwórz tabelę na koncie magazynu. W tym artykule przedstawiono następnie sposobu tworzenia tabel w języku C# i wykonywanie operacji tabeli podstawowej, takich jak dodawanie, modyfikowanie, Odczyt i usuwanie wpisów tabeli.  W kodzie użyto biblioteki klienta magazynu Azure dla platformy .NET. Aby uzyskać więcej informacji na temat platformy ASP.NET, zobacz [ASP.NET](http://www.asp.net).

Niektóre z interfejsów API usługi magazynu Azure są asynchroniczne, a kod w tym artykule przyjęto założenie, że metody asynchroniczne są używane. Zobacz [programowanie asynchroniczne](https://docs.microsoft.com/dotnet/csharp/async) Aby uzyskać więcej informacji.

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie

Aby uzyskać dostęp do tabel w projektach platformy ASP.NET Core, musisz obejmują następujące elementy do plików źródłowych C# które uzyskują dostęp do magazynu tabel platformy Azure.

1. Dodaj niezbędne `using` instrukcji:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje informacje o koncie magazynu. Można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure, należy użyć poniższego kodu:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz `CloudTableClient` obiekt, aby odwoływać się do obiektów tabeli na koncie magazynu:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz `CloudTable` obiektu odwołania odwołanie określonej tabeli i jednostki do:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Utwórz tabelę w kodzie

Aby utworzyć tabeli platformy Azure, należy wywołać "CreateIfNotExistsAsync()":

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki nazywane `CustomerEntity` używającej imienia klienta jako klucza wiersza i nazwiska jako klucza partycji.

```cs
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Operacje dotyczące użycia jednostek tabeli `CloudTable` obiekt utworzoną wcześniej w [dostęp do tabel w kodzie](#access-tables-in-code). `TableOperation` Obiekt reprezentuje operacji do wykonania. W poniższym przykładzie przedstawiono sposób tworzenia `CloudTable` obiektu i `CustomerEntity` obiektu. Aby przygotować operację, `TableOperation` służy do wstawiania jednostek klienta w tabeli. Na koniec operacji jest wykonywana przez wywołanie `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek

Wiele jednostek można wstawiać do tabeli w operacji zapisu pojedynczego. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jan Kowalski" i "Ben Smith"), dodanie ich do `TableBatchOperation` przy użyciu `Insert` metody, a następnie uruchamia działanie przez wywołanie metody `CloudTable.ExecuteBatchAsync`.

```cs
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Aby sprawdzić tabeli dla wszystkich jednostek w partycji, użyj `TableQuery` obiektu. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Pobierz pojedynczy element

Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod używa `TableOperation` obiekt, aby określić klienta o nazwie "Ben Smith". Metoda zwraca tylko jedną jednostkę zamiast kolekcji, a zwrócona wartość w `TableResult.Result` jest `CustomerEntity` obiektu. Określenie kluczy partycji i wiersza w zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z `Table` usługi.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Po możesz znaleźć, można usunąć jednostki. Poniższy kod wyszukuje i usunięcie jednostki klienta o nazwie "Ben Smith":

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
