---
title: Wprowadzenie do magazynu tabel przy użyciu programu Visual Studio (usługi w chmurze)
description: Jak rozpocząć korzystanie z magazynu tabel platformy Azure w projekcie usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych z programem Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299992"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do magazynu tabel platformy Azure i usług połączonych z programem Visual Studio (projekty usług w chmurze)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak rozpocząć korzystanie z magazynu tabel platformy Azure w programie Visual Studio po utworzeniu konta magazynu platformy Azure lub odwoływaniu się do niego w projekcie usług w chmurze przy użyciu okna dialogowego **Dodaj połączone usługi** programu Visual Studio. Operacja **Dodaj połączone usługi** instaluje odpowiednie pakiety NuGet, aby uzyskać dostęp do magazynu platformy Azure w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu.

Usługa magazynu tabel platformy Azure umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

Aby rozpocząć, musisz najpierw utworzyć tabelę na koncie magazynu. Pokażemy Ci, jak utworzyć tabelę platformy Azure w kodzie, a także jak wykonywać podstawowe operacje tabeli i jednostki, takie jak dodawanie, modyfikowanie, czytanie i odczytywanie jednostek tabeli. Przykłady są zapisywane\# w kodzie C i używają [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**UWAGA:** Niektóre interfejsy API, które wykonują wywołania do usługi Azure Storage są asynchroniczne. Zobacz [programowanie asynchroniczne z Async i Czekaj, aby](https://msdn.microsoft.com/library/hh191443.aspx) uzyskać więcej informacji. Poniższy kod zakłada, że używane są metody programowania asynchronii.

* Zobacz [Wprowadzenie do usługi Azure Table Storage przy użyciu platformy .NET,](../storage/storage-dotnet-how-to-use-tables.md) aby uzyskać więcej informacji na temat programowego manipulowania tabelami.
* Aby uzyskać ogólne informacje na temat usługi Azure Storage, zobacz [dokumentację magazynu.](https://azure.microsoft.com/documentation/services/storage/)
* Aby uzyskać ogólne informacje na temat usług w chmurze platformy Azure, zobacz [dokumentację usług](https://azure.microsoft.com/documentation/services/cloud-services/) w chmurze.
* Zobacz [ASP.NET,](https://www.asp.net) aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET.

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie
Aby uzyskać dostęp do tabel w projektach usług w chmurze, należy dołączyć następujące elementy do wszystkich plików źródłowych języka C#, które uzyskują dostęp do magazynu tabel platformy Azure.

1. Upewnij się, że deklaracje obszaru nazw w górnej części pliku C# zawierają te **instrukcje.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Użyj wszystkich powyższych kodu przed kodem w poniższych przykładach.
   > 
   > 
3. Pobierz **CloudTableClient** obiektu do odwoływania się do obiektów tabeli na koncie magazynu.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Pobierz **CloudTable** obiektu odwołania do odwołania się do określonej tabeli i jednostek.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Tworzenie tabeli w kodzie
Aby utworzyć tabelę platformy Azure, wystarczy dodać wywołanie **CreateIfNotExistsAsync** po otrzymasz **CloudTable** obiektu, zgodnie z opisem w sekcji "Tabele dostępu w kodzie".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki o nazwie **CustomerEntity,** która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji.

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

Operacje tabeli obejmujące jednostki są wykonywane przy użyciu cloudtable obiektu **utworzonego** wcześniej w "Tabele dostępu w kodzie." **Obiekt TableOperation** reprezentuje operację do wykonania. Poniższy przykład kodu pokazuje, jak utworzyć **obiekt CloudTable** i **CustomerEntity** obiektu. Aby przygotować operację, **tableoperation** jest tworzony w celu wstawienia jednostki klienta do tabeli. Na koniec operacja jest wykonywana przez **wywołanie CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek
Można wstawić wiele elementów do tabeli w jednej operacji zapisu. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jeff Smith" i "Ben Smith"), dodaje je do **TableBatchOperation** obiektu przy użyciu Wstaw metody, a następnie rozpoczyna operację, wywołując **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Pobierz wszystkie jednostki w partycji
Aby zbadać tabelę dla wszystkich jednostek w partycji, należy użyć **Obiektu TableQuery.** Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>Uzyskaj jedną encję
Można napisać kwerendę, aby uzyskać pojedynczą, określoną encję. Poniższy kod używa **TableOperation** obiektu, aby określić klienta o nazwie "Ben Smith". Ta metoda zwraca tylko jedną jednostkę, a nie kolekcję, a zwrócona wartość w **TableResult.Result** jest **customerentity** obiektu. Określanie kluczy partycji i wiersza w kwerendzie jest najszybszym sposobem pobierania pojedynczej jednostki z usługi **tabel.**

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Usuwanie jednostki
Po jej znalezieniu można usunąć encję. Poniższy kod wyszukuje jednostkę klienta o nazwie "Ben Smith", a jeśli ją znajdzie, usuwa ją.

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

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

