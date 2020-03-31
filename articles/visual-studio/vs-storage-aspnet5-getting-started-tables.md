---
title: Wprowadzenie do magazynu tabel przy użyciu programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć pracę z magazynem tabel platformy Azure w projekcie ASP.NET Core w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych z programem Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298819"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak rozpocząć pracę z usługą Azure Table Storage i usługami połączonymi z programem Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

W tym artykule opisano, jak rozpocząć korzystanie z magazynu tabel platformy Azure w programie Visual Studio po utworzeniu konta magazynu platformy Azure lub odwoływaniu się do niego w projekcie ASP.NET Core przy użyciu funkcji **Połączonych usług** programu Visual Studio. **Operacja Połączone usługi** instaluje odpowiednie pakiety NuGet, aby uzyskać dostęp do magazynu platformy Azure w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentację magazynu, aby](https://azure.microsoft.com/documentation/services/storage/) uzyskać ogólne informacje na temat usługi Azure Storage).

Usługa magazynu tabel platformy Azure umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych. Aby uzyskać bardziej ogólne informacje dotyczące korzystania z magazynu tabel platformy Azure, zobacz [Wprowadzenie do magazynu tabel platformy Azure przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

Aby rozpocząć, najpierw utwórz tabelę na koncie magazynu. W tym artykule pokazano, jak utworzyć tabelę w języku C# i jak wykonywać podstawowe operacje tabeli, takie jak dodawanie, modyfikowanie, czytanie i usuwanie wpisów tabeli.  Kod używa biblioteki klienta usługi Azure Storage dla platformy .NET. Aby uzyskać więcej informacji na temat ASP.NET, zobacz [ASP.NET](https://www.asp.net).

Niektóre interfejsy API usługi Azure Storage są asynchroniczne, a kod w tym artykule zakłada, że używane są metody asynchroniczne. Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie

Aby uzyskać dostęp do tabel w projektach ASP.NET Core, należy dołączyć następujące elementy do dowolnych plików źródłowych języka C#, które uzyskują dostęp do magazynu tabel platformy Azure.

1. Dodaj niezbędne `using` instrukcje:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, używając nazwy konta magazynu i klucza konta, który można znaleźć w ciągu połączenia magazynu w appSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Pobierz `CloudTableClient` obiekt, aby odwoływać się do obiektów tabeli na koncie magazynu:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz `CloudTable` obiekt odwołania do określonej tabeli i jednostek:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tworzenie tabeli w kodzie

Aby utworzyć tabelę platformy Azure, utwórz metodę `CreateIfNotExistsAsync()`asynchronizową i w niej wywołaj:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać encję do tabeli, należy utworzyć klasę definiującej właściwości encji. Poniższy kod definiuje klasę `CustomerEntity` jednostki o nazwie, która używa imienia klienta jako klucza wiersza i nazwisko jako klucz partycji.

```csharp
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

Operacje tabeli obejmujące `CloudTable` jednostki używają obiektu utworzonego wcześniej w [tabelach programu Access w kodzie](#access-tables-in-code). Obiekt `TableOperation` reprezentuje operację do wykonania. Poniższy przykład kodu pokazuje, `CloudTable` jak `CustomerEntity` utworzyć obiekt i obiekt. Aby przygotować operację, `TableOperation` tworzony jest w celu wstawienia jednostki odbiorcy do tabeli. Na koniec operacja jest wykonywana `CloudTable.ExecuteAsync`przez wywołanie .

```csharp
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

Można wstawić wiele elementów do tabeli w jednej operacji zapisu. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jeff Smith" `TableBatchOperation` i `Insert` "Ben Smith"), dodaje je `CloudTable.ExecuteBatchAsync`do obiektu przy użyciu metody, a następnie rozpoczyna operację przez wywołanie .

```csharp
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

## <a name="get-all-of-the-entities-in-a-partition"></a>Pobierz wszystkie jednostki w partycji

Aby zbadać tabelę dla wszystkich jednostek w `TableQuery` partycji, należy użyć obiektu. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

```csharp
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

## <a name="get-a-single-entity"></a>Uzyskaj jedną encję

Można napisać kwerendę, aby uzyskać pojedynczą, określoną encję. Poniższy kod używa `TableOperation` obiektu do określenia klienta o nazwie "Ben Smith". Metoda zwraca tylko jedną jednostkę, a nie kolekcję, a zwrócona wartość w `TableResult.Result` jest obiektem. `CustomerEntity` Określanie kluczy partycji i wiersza w kwerendzie jest najszybszym `Table` sposobem pobrania pojedynczej jednostki z usługi.

```csharp
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

Po jej znalezieniu można usunąć encję. Poniższy kod wyszukuje i usuwa jednostkę klienta o nazwie "Ben Smith":

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

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

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
