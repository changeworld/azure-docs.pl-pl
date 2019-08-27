---
title: Tworzenie aplikacji konsolowej .NET do zarządzania Azure Cosmos DB zasobów interfejsu API SQL
description: Dowiedz się, jak utworzyć aplikację konsolową .NET, aby zarządzać Azure Cosmos DB zasobów konta interfejsu API SQL.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: cbf039a932c16269f703818e9f0ffef4ce852686
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018739"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Szybki start: Tworzenie aplikacji konsolowej .NET do zarządzania Azure Cosmos DB zasobów interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Wprowadzenie do Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET. Wykonaj kroki opisane w tym dokumencie, aby zainstalować pakiet .NET, skompilować aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w Azure Cosmos DB. 

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Za pomocą Azure Cosmos DB można szybko tworzyć i badać bazy danych kluczy/wartości, dokumentów i grafów. Użyj Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET, aby:

* Tworzenie bazy danych i kontenera usługi Azure Cosmos
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | Pakiet[kodu](https://github.com/Azure/azure-cosmos-dotnet-v3)[](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) źródłowego biblioteki dokumentacji interfejsu API (NuGet) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/) lub [wypróbuj bezpłatnie bezpłatne Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. 
* [Zestaw .NET Core 2,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja zawiera szczegółowe instrukcje dotyczące tworzenia konta usługi Azure Cosmos oraz konfigurowania projektu korzystającego z Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym artykule tworzy `FamilyDatabase` bazę danych i członków rodziny (każdy członek rodziny jest elementem) w tej bazie danych. Każdy członek rodziny ma właściwości, takie `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`jak. `LastName` Właściwość jest używana jako klucz partycji dla kontenera. 

### <a id="create-account"></a>Utwórz konto usługi Azure Cosmos

Poniższy kod utworzy konto usługi Azure Cosmos z spójnością sesji. Konto jest replikowane w `South Central US` i. `North Central US` Wybierz przycisk **Wypróbuj** i wklej kod, aby uruchomić go w usłudze Azure Cloud Shell. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

### <a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji platformy .NET

Utwórz nową aplikację platformy .NET w preferowanym edytorze lub środowisku IDE. W oknie konsoli Uruchom następujące polecenie dotnet New, aby utworzyć nową aplikację o nazwie `todo`.

```console
dotnet new console --langVersion 7.1 -n todo
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
cd todo
dotnet build
```

Oczekiwane dane wyjściowe kompilacji powinny wyglądać następująco:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Instalowanie pakietu Azure Cosmos DB

Nadal w katalogu aplikacji Zainstaluj Azure Cosmos DBą bibliotekę kliencką dla platformy .NET Core za pomocą polecenia dotnet Add Package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Skopiuj poświadczenia konta usługi Azure Cosmos z Azure Portal

Przykładowa aplikacja wymaga uwierzytelnienia na koncie usługi Azure Cosmos. Aby przeprowadzić uwierzytelnianie, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos. 

1. Otwórz okienko **klucze** i skopiuj **Identyfikator URI** i **klucz podstawowy** konta. W następnym kroku dodasz wartości identyfikatora URI i kluczy do zmiennej środowiskowej.

### <a name="set-the-environment-variables"></a>Ustawianie zmiennych środowiskowych

Po skopiowaniu **identyfikatora URI** i **klucza podstawowego** konta Zapisz je w nowej zmiennej środowiskowej na maszynie lokalnej, na której działa aplikacja. Aby ustawić zmienną środowiskową, Otwórz okno konsoli i uruchom następujące polecenie. Pamiętaj, aby zastąpić `<Your_Azure_Cosmos_account_URI>` wartości `<Your_Azure_Cosmos_account_PRIMARY_KEY>` i.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Model obiektów

Przed rozpoczęciem tworzenia aplikacji Przyjrzyjmy się hierarchii zasobów w Azure Cosmos DB i modelu obiektów używanym do tworzenia tych zasobów i uzyskiwania do nich dostępu. Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Containers 
* Elementy

Aby dowiedzieć się więcej o hierarchii różnych jednostek, zobacz [Praca z bazami danych, kontenerami i elementami w Azure Cosmos DB](databases-containers-items.md) artykule. Do korzystania z tych zasobów będziesz używać następujących klas platformy .NET:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) — Ta klasa udostępnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos DB. Obiekt klienta służy do konfigurowania i wykonywania żądań dotyczących usługi.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczną. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)— ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) kontener jako operację asynchroniczną. Możesz sprawdzić kod stanu z odpowiedzi, aby określić, czy kontener został nowo utworzony (201) czy został zwrócony istniejący kontener (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) — ta metoda tworzy element w kontenerze. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje, lub zastępuje element, jeśli już istnieje. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) — ta metoda tworzy zapytanie dla elementów w kontenerze w bazie danych Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowane. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync`Metoda usuwa tylko bazę danych. `Cosmosclient` Usuwanie wystąpienia powinno nastąpić oddzielnie (które wykonuje Metoda DeleteDatabaseAndCleanupAsync. 

 ## <a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy bazę danych rodziny w Azure Cosmos DB. Baza danych rodziny zawiera szczegóły rodziny, takie jak nazwa, adres, lokalizacja, skojarzone elementy nadrzędne, dzieci i zwierzęta domowe. Przed zapełnieniem danych na koncie usługi Azure Cosmos Zdefiniuj właściwości elementu rodziny. Utwórz nową klasę o nazwie `Family.cs` na poziomie głównym aplikacji przykładowej i Dodaj do niej następujący kod:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie dyrektyw using & Definiowanie obiektu klienta

W katalogu projektu Otwórz `Program.cs` plik w edytorze i Dodaj następujące dyrektywy using w górnej części aplikacji:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

`program.cs file`Do, Dodaj kod, aby odczytać zmienne środowiskowe, które zostały ustawione w poprzednim kroku. Zdefiniuj obiekty, `Database`i. `CosmosClient` `Container` Następnie Dodaj kod do metody Main, która wywołuje metodę `GetStartedDemoAsync` służącą do zarządzania zasobami kont usługi Azure Cosmos. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Tworzenie bazy danych 

`CreateDatabaseAsync` Zdefiniuj metodę`program.cs` w klasie. Ta metoda tworzy, `FamilyDatabase` Jeśli jeszcze nie istnieje.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tworzenie kontenera

`CreateContainerAsync` Zdefiniuj metodę`program.cs` w klasie. Ta metoda tworzy, `FamilyContainer` Jeśli jeszcze nie istnieje. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Utwórz element

Utwórz element rodziny poprzez dodanie `AddItemsToContainerAsync` metody do następującego kodu. Możesz użyć metod lub `CreateItemAsync` `UpsertItemAsync` , aby utworzyć element:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
 };

try
{
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Zbadaj elementy

Po wstawieniu elementu można uruchomić zapytanie w celu uzyskania szczegółów rodziny "Andersen". Poniższy kod przedstawia sposób wykonywania zapytania bezpośrednio przy użyciu zapytania SQL. Zapytanie SQL, aby uzyskać szczegółowe informacje dotyczące rodziny "Anderson": `SELECT * FROM c WHERE c.LastName = 'Andersen'`. `QueryItemsAsync` Zdefiniuj metodę`program.cs` w klasie i Dodaj do niej następujący kod:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Usuwanie bazy danych 

Na koniec można usunąć bazę danych, dodając `DeleteDatabaseAndCleanupAsync` do niej następujący kod:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Wykonywanie operacji CRUD

Po zdefiniowaniu wszystkich wymaganych metod wykonaj je za pomocą `GetStartedDemoAsync` metody. `DeleteDatabaseAndCleanupAsync` Metoda z komentarzem w tym kodzie, ponieważ nie będą wyświetlane żadne zasoby, jeśli ta metoda jest wykonywana. Po zweryfikowaniu, że Azure Cosmos DB zasoby zostały utworzone w Azure Portal, można usunąć z niego komentarz. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
    await this.ReplaceFamilyItemAsync();
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
}
```

Po dodaniu wszystkich wymaganych metod Zapisz `Program.cs` plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następnie Skompiluj i uruchom aplikację, aby utworzyć zasoby Azure Cosmos DB. Upewnij się, że otwarto nowe okno wiersza polecenia, nie używaj tego samego wystąpienia, które było używane do ustawiania zmiennych środowiskowych. Ponieważ zmienne środowiskowe nie są ustawione w bieżącym otwartym oknie. Aby wyświetlić aktualizacje, należy otworzyć nowy wiersz polecenia. 

```console
dotnet build
```

```console
dotnet run
```

Po uruchomieniu aplikacji generowane są następujące dane wyjściowe. Możesz również zalogować się do Azure Portal i sprawdzić, czy zasoby są tworzone:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Możesz sprawdzić, czy dane zostały utworzone, logując się do Azure Portal i zobacz wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć konto usługi Azure Cosmos i odpowiednią grupę zasobów. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos, tworzenia bazy danych i kontenera przy użyciu aplikacji platformy .NET Core. Teraz możesz zaimportować dodatkowe dane do konta usługi Azure Cosmos za pomocą instrukcji int w następującym artykule. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
