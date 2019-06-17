---
title: Jak rozpocząć pracę z usługi table storage i Visual Studio podłączone usługi (systemu Windows platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługą Azure Table storage w projektach programu ASP.NET Core w programie Visual Studio po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 1f90ce71084ba3acbf5a0aec5c7b8e9683323766
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362122"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak rozpocząć pracę z zestawem Azure Table storage i Visual Studio podłączone usługi

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

W tym artykule opisano sposób rozpoczęcia pracy przy użyciu usługi Azure Table storage w programie Visual Studio po użytkownik utworzył, lub odwołanie do konta usługi Azure storage w projektach programu ASP.NET Core przy użyciu programu Visual Studio **podłączone usługi** funkcji. **Podłączone usługi** operacji instaluje odpowiednie pakiety NuGet dostępu do magazynu platformy Azure w swoim projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentacja usługi Storage](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje o usłudze Azure Storage.)

Usługa Azure Table storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz i na zewnątrz chmury platformy Azure. Tabele platformy Azure są idealnym rozwiązaniem do przechowywania strukturalnych danych nierelacyjnych. Aby uzyskać więcej ogólnych informacji o korzystaniu z usługi Azure Table storage, zobacz [Rozpoczynanie pracy z usługą Azure Table storage przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

Aby rozpocząć pracę, należy najpierw utworzyć tabelę, w ramach konta magazynu. Następnie w tym artykule pokazano, jak utworzyć tabelę w języku C# oraz jak wykonywać operacje tabeli podstawowej, takie jak dodawanie, modyfikowanie, odczytywanie i usuwanie wpisów tabeli.  Kod używa biblioteki klienta usługi Azure Storage dla platformy .NET. Aby uzyskać więcej informacji na temat platformy ASP.NET, zobacz [ASP.NET](https://www.asp.net).

Niektóre z interfejsów API usługi Azure Storage są asynchroniczne i kodu w tym artykule przyjęto założenie, że metody asynchroniczne są używane. Zobacz [programowania asynchronicznego](https://docs.microsoft.com/dotnet/csharp/async) Aby uzyskać więcej informacji.

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie

Dostęp do tabel w projektach ASP.NET Core, należy uwzględnić poniższe elementy do plików źródłowych języka C# uzyskujących dostęp do usługi Azure table storage.

1. Dodaj niezbędne `using` instrukcji:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje dane konta magazynu. Użyj poniższego kodu, przy użyciu nazwy konta magazynu oraz klucz konta, który można znaleźć w parametrach połączenia magazynu w pliku appSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Pobierz `CloudTableClient` obiekt, aby odwoływać się obiektów tabeli na koncie magazynu:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz `CloudTable` obiektu odwołania, aby odwoływać się do określonej tabeli i jednostki:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Utwórz tabelę w kodzie

Aby utworzyć tabelę platformy Azure, tworzenia metody asynchronicznej i znajdujący się w nim, należy wywołać `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki o nazwie `CustomerEntity` , używa imienia klienta jako klucz wiersza i nazwiska jako klucza partycji.

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

Operacje dotyczące użycia jednostek tabeli `CloudTable` obiekt utworzony wcześniej w procedurze [dostęp do tabel w kodzie](#access-tables-in-code). `TableOperation` Obiekt reprezentuje operację do wykonania. Poniższy przykład kodu pokazuje sposób tworzenia `CloudTable` obiektu i `CustomerEntity` obiektu. Aby przygotować operację, `TableOperation` służy do wstawiania jednostki Klient w tabeli. Na koniec operacja jest wykonywana przez wywołanie metody `CloudTable.ExecuteAsync`.

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

Możesz wstawić wiele jednostek do tabeli w operacji zapisu w jednym. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jan Kowalski" i "Ben Smith"), dodanie ich do `TableBatchOperation` przy użyciu `Insert` metody, a następnie uruchamia operację, wywołując `CloudTable.ExecuteBatchAsync`.

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

Aby wysłać zapytanie do tabeli dla wszystkich jednostek w partycji, należy użyć `TableQuery` obiektu. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

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

Można napisać zapytanie w celu uzyskania jednej, określonej jednostki. Poniższy kod używa `TableOperation` obiektu w celu określenia klienta o nazwie "Ben Smith". Metoda ta zwraca tylko jedną jednostkę zamiast kolekcji, a zwrócona wartość w `TableResult.Result` jest `CustomerEntity` obiektu. Określenie kluczy partycji i wiersza w zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z `Table` usługi.

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

Po znalezieniu go, można usunąć jednostki. Poniższy kod wyszukuje i usunięcie jednostki klienta "Jan Kowalski":

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

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
