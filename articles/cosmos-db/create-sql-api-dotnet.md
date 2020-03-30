---
title: Szybki start — tworzenie aplikacji konsoli platformy .NET do zarządzania zasobami interfejsu SQL usługi Azure Cosmos DB DB
description: Dowiedz się, jak utworzyć aplikację konsoli platformy .NET do zarządzania zasobami konta interfejsu API SQL usługi Azure Cosmos DB w tym przewodniku Szybki start.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240415"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Szybki start: tworzenie aplikacji konsoli platformy .NET do zarządzania zasobami interfejsu SQL usługi Azure Cosmos DB DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Wprowadzenie do biblioteki klienta interfejsu API sql usługi Azure Cosmos DB dla platformy .NET. Wykonaj kroki opisane w tym doc, aby zainstalować pakiet .NET, utworzyć aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w usłudze Azure Cosmos DB. 

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Usługi Azure Cosmos DB można używać do szybkiego tworzenia i wykonywania zapytań o bazy danych klucz/wartość, dokumenty i wykresy. Użyj biblioteki klienta interfejsu SQL usługi Azure Cosmos DB dla platformy .NET, aby:

* Tworzenie bazy danych usługi Azure Cosmos i kontenera
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[Dokumentacja](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | dokumentacji interfejsu API[Pakiet źródłowy kodu źródłowego biblioteki](https://github.com/Azure/azure-cosmos-dotnet-v3) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz ją bezpłatnie](https://azure.microsoft.com/free/) lub wypróbuj [usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) bez bezpłatnej subskrypcji platformy Azure i zobowiązań. 
* [SDK .NET Core 2.1 lub nowszym](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji można przejść przez tworzenie konta usługi Azure Cosmos i konfigurowanie projektu, który używa biblioteki klienta interfejsu API SQL usługi Azure Cosmos DB dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym `FamilyDatabase` artykule tworzy bazę danych i członków rodziny (każdy członek rodziny jest elementem) w tej bazie danych. Każdy członek rodziny ma `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`właściwości, takie jak . Właściwość `LastName` jest używana jako klucz partycji dla kontenera. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Tworzenie konta usługi Azure Cosmos

Jeśli używasz [opcji Wypróbuj usługę Azure Cosmos DB dla bezpłatnej](https://azure.microsoft.com/try/cosmosdb/) opcji tworzenia konta usługi Azure Cosmos, musisz utworzyć konto usługi Azure Cosmos DB typu SQL **API**. Konto testowe usługi Azure Cosmos DB jest już utworzone dla Ciebie. Nie musisz jawnie tworzyć konta, więc możesz pominąć tę sekcję i przejść do następnej sekcji.

Jeśli masz własną subskrypcję platformy Azure lub utworzyłeś subskrypcję za darmo, należy utworzyć konto usługi Azure Cosmos jawnie. Poniższy kod utworzy konto usługi Azure Cosmos z spójnością sesji. Konto jest replikowane `South Central US` `North Central US`w i .  

Za pomocą usługi Azure Cloud Shell można utworzyć konto usługi Azure Cosmos. Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. W przypadku tego szybkiego startu wybierz tryb **Bash.** Usługa Azure Cloud Shell wymaga również konta magazynu, można je utworzyć po wyświetleniu monitu.

Wybierz przycisk **Wypróbuj** obok następującego kodu, wybierz tryb **Bash,** wybierz **pozycję utwórz konto magazynu** i zaloguj się do usługi Cloud Shell. Następnie skopiuj i wklej następujący kod do powłoki chmury platformy Azure i uruchom go. Nazwa konta usługi Azure Cosmos musi być unikatowa globalnie, upewnij się, że `mysqlapicosmosdb` aktualizuje wartość przed uruchomieniem polecenia.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

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

Utworzenie konta usługi Azure Cosmos zajmuje trochę czasu, gdy operacja zakończy się pomyślnie, można zobaczyć dane wyjściowe potwierdzenia. Po pomyślnym zakończeniu polecenia zaloguj się do [witryny Azure portal](https://portal.azure.com/) i sprawdź, czy istnieje konto usługi Azure Cosmos o określonej nazwie. Okno usługi Azure Cloud Shell można zamknąć po utworzeniu zasobu. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji .NET

Utwórz nową aplikację platformy .NET w preferowanym edytorze lub ide. Otwórz wiersz polecenia systemu Windows lub okno terminala na komputerze lokalnym. Wszystkie polecenia w następnych sekcjach zostaną uruchomione w wierszu polecenia lub terminalu.  Uruchom następujące polecenie dotnet new, aby utworzyć `todo`nową aplikację o nazwie . Parametr --langVersion ustawia właściwość LangVersion w utworzonym pliku projektu.

```console
dotnet new console --langVersion 7.1 -n todo
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```console
cd todo
dotnet build
```

Oczekiwane dane wyjściowe z kompilacji powinny wyglądać mniej więcej tak:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalowanie pakietu usługi Azure Cosmos DB

Będąc jeszcze w katalogu aplikacji, zainstaluj bibliotekę klienta usługi Azure Cosmos DB dla platformy .NET Core przy użyciu polecenia dotnet add package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń konta usługi Azure Cosmos z witryny Azure portal

Przykładowa aplikacja musi uwierzytelnić się na koncie usługi Azure Cosmos. Aby uwierzytelnić, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Przejdź do konta usługi Azure Cosmos.

1. Otwórz okienko **Klucze** i skopiuj **identyfikator URI** i **klucz podstawowy** konta. Wartości identyfikatora URI i kluczy zostaną dodane do zmiennej środowiskowej w następnym kroku.

### <a name="set-the-environment-variables"></a>Ustawianie zmiennych środowiskowych

Po skopiowaniu **identyfikatora URI** i **klucza podstawowego** konta zapisz je na nowej zmiennej środowiskowej na komputerze lokalnym z uruchomieniem aplikacji. Aby ustawić zmienną środowiskową, otwórz okno konsoli i uruchom następujące polecenie. Upewnij się, `<Your_Azure_Cosmos_account_URI>` `<Your_Azure_Cosmos_account_PRIMARY_KEY>` że należy zastąpić i wartości.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Macos**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Model obiektu

Przed rozpoczęciem tworzenia aplikacji przyjrzyjmy się hierarchii zasobów w usłudze Azure Cosmos DB i modelu obiektów używanego do tworzenia tych zasobów i uzyskiwania do nich dostępu. Usługa Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Kontenery 
* Items

Aby dowiedzieć się więcej na temat hierarchii różnych jednostek, zobacz [pracy z bazami danych, kontenerów i elementów w usłudze Azure Cosmos DB](databases-containers-items.md) artykułu. Do interakcji z tymi zasobami użyjesz następujących klas .NET:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) — ta klasa zapewnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos DB. Obiekt klienta jest używany do konfigurowania i wykonywania żądań względem usługi.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczna. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)— ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) kontener jako operację asynchroniczna. Można sprawdzić kod stanu z odpowiedzi, aby ustalić, czy kontener został nowo utworzony (201) lub istniejący kontener został zwrócony (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) — ta metoda tworzy element w kontenerze. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje lub zastępuje element, jeśli już istnieje. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) — ta metoda tworzy kwerendę dla elementów w kontenerze w bazie danych usługi Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowanymi. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync`metoda usuwa tylko bazę danych. Usuwanie `Cosmosclient` wystąpienia powinno odbywać się oddzielnie (co robi w DeleteDatabaseAndCleanupAsync metody. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy rodzinną bazę danych w usłudze Azure Cosmos DB. Rodzinna baza danych zawiera szczegóły rodziny, takie jak imię i nazwisko, adres, lokalizacja, skojarzone z nimi elementy rodziców, dzieci i zwierzęta domowe. Przed zapełnieniem danych do konta usługi Azure Cosmos należy zdefiniować właściwości elementu rodziny. Utwórz nową `Family.cs` klasę o nazwie na poziomie głównym przykładowej aplikacji i dodaj do niej następujący kod:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie używanych dyrektyw & definiowanie obiektu klienta

W katalogu projektu otwórz `Program.cs` plik w edytorze i dodaj następujące elementy, korzystając z dyrektyw u góry aplikacji:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Do pliku **Program.cs** dodaj kod, aby odczytać zmienne środowiskowe ustawione w poprzednim kroku. Zdefiniuj `Database` `Container` obiekt i obiekty. `CosmosClient` Następnie dodaj kod do głównej `GetStartedDemoAsync` metody, która wywołuje metodę, w której zarządzasz zasobami konta usługi Azure Cosmos. 

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

`CreateDatabaseAsync` Zdefiniuj `program.cs` metodę w klasie. Ta metoda `FamilyDatabase` tworzy, jeśli jeszcze nie istnieje.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tworzenie kontenera

`CreateContainerAsync` Zdefiniuj `program.cs` metodę w klasie. Ta metoda `FamilyContainer` tworzy, jeśli jeszcze nie istnieje. 

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

### <a name="create-an-item"></a>Tworzenie elementu

Utwórz element rodziny, `AddItemsToContainerAsync` dodając metodę z następującym kodem. Do utworzenia `CreateItemAsync` `UpsertItemAsync` elementu można użyć metody lub:

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
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Kwerenda elementów

Po wstawieniu elementu można uruchomić kwerendę, aby uzyskać szczegółowe informacje o rodzinie "Andersen". Poniższy kod pokazuje, jak wykonać kwerendę bezpośrednio przy użyciu kwerendy SQL. Zapytanie SQL, aby uzyskać "Anderson" `SELECT * FROM c WHERE c.LastName = 'Andersen'`szczegóły rodziny jest: . `QueryItemsAsync` Zdefiniuj `program.cs` metodę w klasie i dodaj do niej następujący kod:


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

Na koniec można usunąć bazę danych dodając `DeleteDatabaseAndCleanupAsync` metodę z następującym kodem:

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

Po zdefiniowaniu wszystkich wymaganych metod, należy `GetStartedDemoAsync` wykonać je w metodzie. Metoda `DeleteDatabaseAndCleanupAsync` skomentowana w tym kodzie, ponieważ nie zobaczysz żadnych zasobów, jeśli ta metoda jest wykonywana. Możesz go odkomentować po sprawdzeniu, czy zasoby usługi Azure Cosmos DB zostały utworzone w witrynie Azure portal. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Po dodaniu wszystkich wymaganych metod `Program.cs` zapisz plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następna kompilacja i uruchom aplikację, aby utworzyć zasoby usługi Azure Cosmos DB. Upewnij się, aby otworzyć nowe okno wiersza polecenia, nie używaj tego samego wystąpienia, które zostały użyte do ustawienia zmiennych środowiskowych. Ponieważ zmienne środowiskowe nie są ustawione w bieżącym otwartym oknie. Aby wyświetlić aktualizacje, należy otworzyć nowy wiersz polecenia. 

```console
dotnet build
```

```console
dotnet run
```

Następujące dane wyjściowe są generowane po uruchomieniu aplikacji. Można również zalogować się do witryny Azure portal i sprawdzić, czy zasoby są tworzone:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Można sprawdzić, czy dane są tworzone przez zalogowanie się do witryny Azure portal i zobacz wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby usunąć konto usługi Azure Cosmos i odpowiednią grupę zasobów. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos, utworzyć bazę danych i kontener przy użyciu aplikacji .NET Core. Teraz można zaimportować dodatkowe dane do konta usługi Azure Cosmos z instrukcjami int poniższy artykuł. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
