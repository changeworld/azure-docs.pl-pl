---
title: Rozpoczynanie pracy z usługą table storage i Visual Studio podłączone usługi (usługi w chmurze) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę, przy użyciu usługi Azure Table storage w projekt usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: cec8ab9d678ff559176580fa8eccc261f449f4c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362003"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure table storage i Visual Studio połączonych usług (usługi w chmurze projektów)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpoczęcia pracy przy użyciu usługi Azure table storage w programie Visual Studio po użytkownik utworzył, lub odwołanie do konta usługi Azure storage w projekt usług w chmurze przy użyciu programu Visual Studio **Dodaj usługi połączone** okna dialogowego. **Dodaj usługi połączone** operacji instaluje odpowiednie pakiety NuGet dostępu do magazynu platformy Azure w swoim projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu.

Usługa Azure Table storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz i na zewnątrz chmury platformy Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

Aby rozpocząć pracę, należy najpierw utworzyć tabelę w ramach konta magazynu. Pokażemy ci, jak utworzyć tabelę platformy Azure w kodzie, a także jak wykonać podstawowe tabeli i operacje jednostki, takie jak dodawanie, modyfikowanie, odczytywania i odczytywania jednostki z tabeli. Przykłady są napisane w języku C\# kod i użyć [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**UWAGA:** Niektóre z interfejsów API, służące do przeprowadzania wywołań się do usługi Azure storage są asynchroniczne. Zobacz [programowanie asynchroniczne z Async i Await](https://msdn.microsoft.com/library/hh191443.aspx) Aby uzyskać więcej informacji. Kod poniżej założono, że są używane metody programowania asynchronicznego.

* Zobacz [Rozpoczynanie pracy z usługą Azure Table storage przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md) więcej informacji na temat programowe operowanie tabel.
* Zobacz [dokumentacja usługi Storage](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje o usłudze Azure Storage.
* Zobacz [dokumentacji usług w chmurze](https://azure.microsoft.com/documentation/services/cloud-services/) ogólne informacje dotyczące usług Azure cloud services.
* Zobacz [ASP.NET](https://www.asp.net) Aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET.

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie
Dostęp do tabel w projekty usługi w chmurze, należy uwzględnić poniższe elementy do plików źródłowych języka C# uzyskujących dostęp do usługi Azure table storage.

1. Upewnij się, obejmują deklaracje przestrzeni nazw w górnej części pliku języka C#, te **przy użyciu** instrukcji.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Używać wszystkich powyższy kod przed kodem w następujących przykładach.
   > 
   > 
3. Pobierz **CloudTableClient** obiekt, aby odwoływać się obiektów tabeli na koncie magazynu.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Pobierz **CloudTable** obiektu odwołania, aby odwoływać się do określonych tabel i jednostek.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Utwórz tabelę w kodzie
Aby utworzyć tabelę platformy Azure, wystarczy dodać wywołanie **CreateIfNotExistsAsync** do po uzyskaniu **CloudTable** obiektu zgodnie z opisem w sekcji "Uzyskiwać dostęp do tabel w kodzie".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki o nazwie **CustomerEntity** , używa imienia klienta jako klucz wiersza i nazwiska jako klucza partycji.

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

Operacje tabeli obejmujące jednostki są wykonywane przy użyciu **CloudTable** obiektu, który został utworzony we wcześniejszej części "Dostęp do tabel w kodzie." **TableOperation** obiekt reprezentuje operację do wykonania. Poniższy przykład kodu pokazuje sposób tworzenia **CloudTable** obiektu i **CustomerEntity** obiektu. Aby przygotować operację, **TableOperation** służy do wstawiania jednostki Klient w tabeli. Na koniec operacja jest wykonywana przez wywołanie metody **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek
Możesz wstawić wiele jednostek do tabeli w operacji zapisu w jednym. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jan Kowalski" i "Ben Smith"), dodanie ich do **TableBatchOperation** obiektu przy użyciu metody Insert, a następnie rozpoczyna operację, wywołując  **CloudTable.ExecuteBatchAsync**.

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
Aby wysłać zapytanie do tabeli dla wszystkich jednostek w partycji, należy użyć **TableQuery** obiektu. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

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


## <a name="get-a-single-entity"></a>Pobieranie pojedynczej jednostki
Można napisać zapytanie w celu uzyskania jednej, określonej jednostki. Poniższy kod używa **TableOperation** obiektu w celu określenia klienta o nazwie "Ben Smith". Metoda ta zwraca tylko jedną jednostkę zamiast kolekcji, a zwrócona wartość w **TableResult.Result** jest **CustomerEntity** obiektu. Określenie kluczy partycji i wiersza w zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z **tabeli** usługi.

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
Po znalezieniu go, można usunąć jednostki. Poniższy kod wyszukuje jednostki klienta o nazwie "Ben Smith", a jeśli go znajdzie, usuwa je.

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

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

