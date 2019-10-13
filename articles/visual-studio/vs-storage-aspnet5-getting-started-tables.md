---
title: Wprowadzenie do usługi Table Storage przy użyciu programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć pracę z usługą Azure Table Storage w projekcie ASP.NET Core w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298819"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak rozpocząć pracę z usługą Azure Table Storage i usługami połączonymi programu Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

W tym artykule opisano, jak rozpocząć korzystanie z usługi Azure Table Storage w programie Visual Studio po utworzeniu lub przywoływaniu konta usługi Azure Storage w projekcie ASP.NET Core przy użyciu funkcji **usługi połączone** programu Visual Studio. Operacja **połączone usługi** instaluje odpowiednie pakiety NuGet w celu uzyskania dostępu do usługi Azure Storage w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentację magazynu](https://azure.microsoft.com/documentation/services/storage/) , aby uzyskać ogólne informacje na temat usługi Azure Storage).

Usługa Azure Table Storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa to magazyn danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych. Aby uzyskać więcej ogólnych informacji o korzystaniu z usługi Azure Table Storage, zobacz [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

Aby rozpocząć, najpierw utwórz tabelę na koncie magazynu. W tym artykule pokazano, jak utworzyć tabelę w programie C# oraz jak wykonywać podstawowe operacje tabeli, takie jak dodawanie, modyfikowanie, odczytywanie i usuwanie wpisów w tabeli.  Kod używa biblioteki klienta usługi Azure Storage dla platformy .NET. Aby uzyskać więcej informacji na temat ASP.NET, zobacz [ASP.NET](https://www.asp.net).

Niektóre interfejsy API usługi Azure Storage są asynchroniczne, a w kodzie w tym artykule przyjęto, że metody asynchroniczne są używane. Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie

Aby uzyskać dostęp do tabel w projektach ASP.NET Core, należy dołączyć następujące elementy do wszystkich C# plików źródłowych, które uzyskują dostęp do usługi Azure Table Storage.

1. Dodaj wymagane instrukcje `using`:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Pobierz obiekt `CloudStorageAccount` reprezentujący informacje o koncie magazynu. Użyj poniższego kodu, używając nazwy konta magazynu i klucza konta, które można znaleźć w parametrach połączenia magazynu w pliku appSettings. JSON:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Pobierz obiekt `CloudTableClient` w celu odwoływania się do obiektów tabeli na koncie magazynu:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt odniesienia `CloudTable`, aby odwołać się do określonej tabeli i jednostek:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tworzenie tabeli w kodzie

Aby utworzyć tabelę platformy Azure, Utwórz w niej metodę asynchroniczną, wywołaj `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki o nazwie `CustomerEntity`, która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji.

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

Operacje tabeli obejmujące jednostki używają obiektu `CloudTable` utworzonego wcześniej w [tabelach dostępu w kodzie](#access-tables-in-code). Obiekt `TableOperation` reprezentuje operację, która ma zostać wykonana. Poniższy przykład kodu pokazuje, jak utworzyć obiekt `CloudTable` i obiekt `CustomerEntity`. Aby przygotować operację, zostanie utworzony `TableOperation`, aby wstawić jednostkę klienta do tabeli. Na koniec operacja jest wykonywana przez wywołanie `CloudTable.ExecuteAsync`.

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

Można wstawić wiele jednostek do tabeli w jednej operacji zapisu. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jan Kowalski" i "Ben Kowalski"), dodaje je do obiektu `TableBatchOperation` przy użyciu metody `Insert`, a następnie uruchamia operację, wywołując `CloudTable.ExecuteBatchAsync`.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Aby zbadać tabelę dla wszystkich jednostek w partycji, użyj obiektu `TableQuery`. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

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

## <a name="get-a-single-entity"></a>Pobieranie pojedynczej jednostki

Można napisać zapytanie w celu pobrania pojedynczej określonej jednostki. Poniższy kod używa obiektu `TableOperation`, aby określić klienta o nazwie "Ben Kowalski". Metoda zwraca tylko jedną jednostkę, a nie kolekcję, a zwracana wartość w `TableResult.Result` jest obiektem `CustomerEntity`. Określanie kluczy partycji i wierszy w zapytaniu jest najszybszym sposobem na pobranie pojedynczej jednostki z usługi `Table`.

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

Możesz usunąć jednostkę po jej znalezieniu. Poniższy kod wyszukuje i usuwa jednostkę klienta o nazwie "Ben Kowalski":

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
