---
title: 'Samouczek: Tworzenie aplikacji .NET Core do zarządzania danymi zapisanymi na koncie interfejsu API SQL w usłudze Azure Cosmos DB'
description: W tym samouczku pokazano tworzenie bazy danych w trybie online i aplikacji konsolowej C# przy użyciu zestawu SDK dla platformy .NET Core interfejsu API SQL usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: fdd66a28ea38e7ae34ce70958ac57f29bc66ceb7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969244"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Samouczek: Tworzenie aplikacji .NET Core do zarządzania danymi zapisanymi na koncie interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (wersja zapoznawcza)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (wersja zapoznawcza)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako deweloper być może masz aplikacje, które używają danych dokumentów NoSQL. Konto interfejsu API SQL w usłudze Azure Cosmos DB umożliwia przechowywanie tych danych dokumentów i uzyskiwanie do nich dostępu. Ten samouczek przedstawia sposób kompilowania aplikacji .NET Core w celu tworzenia danych zapisanych na koncie interfejsu API SQL usługi Azure Cosmos DB i wykonywania względem nich zapytań. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos i łączenie się z nim
> * Konfigurowanie rozwiązania Visual Studio
> * Tworzenie bazy danych w trybie online
> * Tworzenie kolekcji
> * Tworzenie dokumentów JSON
> * Wykonywanie operacji CRUD względem elementów, kontenera i bazy danych

Nie masz czasu na utworzenie aplikacji? Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Pobierz bezpłatny program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i korzystaj z niego. W przypadku opracowywania aplikacji platformy uniwersalnej systemu Windows należy użyć programu **Visual Studio 2017 w wersji 15.4** lub nowszej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

    * W przypadku komputerów z systemem MacOS lub Linux możesz opracowywać aplikacje platformy .NET Core z poziomu wiersza polecenia, instalując [zestaw SDK platformy .NET Core](https://www.microsoft.com/net/core#macos) dla wybranej platformy. 

    * W przypadku komputerów z systemem Windows możesz opracowywać aplikacje platformy .NET Core z poziomu wiersza polecenia, instalując [zestaw SDK platformy .NET Core](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Tworzenie konta usługi Azure Cosmos

Aby utworzyć konto usługi Azure Cosmos, wykonaj następujące czynności:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Konfigurowanie rozwiązania programu Visual Studio

1. Otwórz program **Visual Studio 2017** na komputerze.

2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.

3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Szablony** > **Visual C#** > **.NET Core** > **Aplikacja konsolowa (.NET Core)**, nadaj projektowi nazwę **DocumentDBGettingStarted**, a następnie wybierz przycisk **OK**.

   ![Zrzut ekranu przedstawiający okno Nowy projekt](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **DocumentDBGettingStarted**.

5. W tym samym menu wybierz pozycję **Zarządzaj pakietami NuGet**.

   ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Na karcie **NuGet** wybierz pozycję **Przeglądaj** w górnej części okna i wpisz ciąg **azure documentdb** w polu wyszukiwania. Upewnij się, że **Uwzględnij wersję wstępną** zaznaczono pole wyboru.

7. W wynikach znajdź pozycję **Microsoft.Azure.DocumentDB.Core**, a następnie wybierz przycisk **Zainstaluj**.

   Identyfikator pakietu biblioteki to [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Jeśli elementem docelowym jest wersja programu .NET Framework (na przykład net461), która nie jest obsługiwana przez ten pakiet .NET Core NuGet, użyj elementu [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) obsługującego wszystkie wersje programu .NET Framework, począwszy od wersji .NET Framework 4.5.

8. Po wyświetleniu monitu zaakceptuj instalacje pakietów NuGet i umowę licencyjną.

Teraz, po ukończeniu konfigurowania, zacznijmy pisać kod. Ukończony projekt kodu z tego samouczka można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Łączenie się z kontem usługi Azure Cosmos

Połącz się z kontem usługi Azure Cosmos przez zaimportowanie wymaganych zależności. Aby zaimportować zależności, dodaj następujący kod na początku pliku Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Następnie dodaj te dwie stałe i zmienną *client* poniżej klasy publicznej *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Następnie przejdź do witryny [Azure Portal](https://portal.azure.com), aby pobrać identyfikator URI i klucz podstawowy. Klucz podstawowy i identyfikator URI usługi Azure Cosmos DB są niezbędne, aby aplikacja mogła zinterpretować, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji.

W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos i kliknij pozycję **Klucze**.

Skopiuj identyfikator URI z portalu i wklej go w miejsce `<your endpoint URI>` w pliku program.cs. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `<your key>`. Upewnij się, że zostały usunięte znaki < i >, ale pozostawiono podwójny cudzysłów wokół punktu końcowego i klucza.

![Pobieranie kluczy z witryny Azure Portal][keys]

Zaczniemy tworzenie aplikacji rozpoczynania pracy, tworząc nowe wystąpienie klasy **DocumentClient**.

Poniżej metody **Main** dodaj nowe zadanie asynchroniczne o nazwie **GetStartedDemo**, które utworzy nowe wystąpienie klasy **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Dodaj następujący kod w celu uruchomienia zadania asynchronicznego z metody **Main**. Metoda **Main** przechwytuje wyjątki i wyświetla je w konsoli.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Wybierz przycisk **DocumentDBGettingStarted**, aby skompilować i uruchomić aplikację.

## <a id="CreateDatabase"></a>Tworzenie bazy danych

Przed dodaniem kodu na potrzeby tworzenia bazy danych dodaj metodę pomocnika na potrzeby pisania w konsoli. Skopiuj i wklej metodę **WriteToConsoleAndPromptToContinue** poniżej metody **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Utwórz bazę danych usługi Azure Cosmos DB za pomocą metody `CreateDatabaseAsync` klasy **DocumentClient**. Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach. Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia klienta. Powoduje to utworzenie bazy danych o nazwie *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

## <a id="CreateColl"></a>Tworzenie kolekcji

Utwórz kolekcję za pomocą metody `CreateDocumentCollectionAsync` klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.

> [!WARNING]
> Metoda **CreateDocumentCollectionAsync** tworzy nową kolekcję z zarezerwowaną przepływnością, co ma wpływ na cenę. Więcej szczegółów można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia bazy danych. Ten kod służy do utworzenia kolekcji dokumentów o nazwie *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

## <a id="CreateDoc"></a>Tworzenie dokumentów JSON

Utwórz dokument za pomocą metody `CreateDocumentAsync` klasy **DocumentClient**. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Można teraz wstawić jeden lub więcej dokumentów. 

Najpierw zostanie utworzona klasa **Family**, która będzie reprezentowała obiekty przechowywane w usłudze Azure Cosmos DB. Zostaną również utworzone podklasy **Parent**, **Child**, **Pet** i **Address**, które są używane w ramach klasy **Family**. Należy pamiętać, że dokumenty muszą mieć właściwość **Id** serializowaną jako **id** w formacie JSON. Utwórz te klasy, dodając następujące podklasy wewnętrzne po metodzie **GetStartedDemo**. Skopiuj i wklej klasy **Family**, **Parent**, **Child**, **Pet** i **Address** poniżej metody **WriteToConsoleAndPromptToContinue**.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
public class Family
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    public string LastName { get; set; }
    public Parent[] Parents { get; set; }
    public Child[] Children { get; set; }
    public Address Address { get; set; }
    public bool IsRegistered { get; set; }
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
```

Skopiuj i wklej metodę **CreateFamilyDocumentIfNotExists** poniżej metody **CreateDocumentCollectionIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Następnie wstaw dwa dokumenty, jeden dla rodziny Andersen i jeden dla rodziny Wakefield. Skopiuj i wklej kod następujący po ciągu `// ADD THIS PART TO YOUR CODE` do metody **GetStartedDemo** poniżej kodu służącego do tworzenia kolekcji dokumentów.

```csharp
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
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
        IsRegistered = true
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
                new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FamilyName = "Merriam",
                        FirstName = "Jesse",
                        Gender = "female",
                        Grade = 8,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Goofy" },
                                new Pet { GivenName = "Shadow" }
                        }
                },
                new Child
                {
                        FamilyName = "Miller",
                        FirstName = "Lisa",
                        Gender = "female",
                        Grade = 1
                }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

![Hierarchiczna relacja między kontem, bazą danych online i kolekcją](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Wykonywanie zapytania względem zasobów usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje zaawansowane zapytania o dokumenty JSON przechowywane w każdej kolekcji. Następujący przykładowy kod przedstawia różne zapytania — przy użyciu składni SQL usługi Azure Cosmos DB oraz LINQ — które można uruchomić względem dokumentów wstawionych w poprzednim kroku.

Skopiuj i wklej metodę **ExecuteSimpleQuery** poniżej metody **CreateFamilyDocumentIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia drugiego dokumentu.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Na poniższym diagramie przedstawiono sposób, w jaki składnia zapytania SQL usługi Azure Cosmos DB jest wywoływana względem utworzonej kolekcji. Ta sama logika dotyczy zapytania LINQ.

![Diagram pokazujący zakres i znaczenie zapytania używanego przez samouczek NoSQL do utworzenia aplikacji konsolowej C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Słowo kluczowe `FROM` jest opcjonalne w zapytaniu, ponieważ zapytania usługi Azure Cosmos DB mają już zakres określony do pojedynczej kolekcji. W związku z tym klauzula "FROM Families f" może być zamieniona na "FROM root r" lub dowolną inną wybraną nazwę zmiennej. Usługa Azure Cosmos DB wnioskuje, że zmienne Families, root lub wybrana nazwa zmiennej domyślnie odwołują się do bieżącej kolekcji.

## <a id="ReplaceDocument"></a>Zastępowanie dokumentu JSON

Usługa Azure Cosmos DB obsługuje zastępowanie dokumentów JSON.  

Skopiuj i wklej metodę **ReplaceFamilyDocument** poniżej metody **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do wykonywania zapytania. Po zastąpieniu dokumentu spowoduje to ponowne uruchomienie tego samego zapytania, aby wyświetlić zmieniony dokument.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

## <a id="DeleteDocument"></a>Usuwanie dokumentu JSON

Usługa Azure Cosmos DB obsługuje usuwanie dokumentów JSON.  

Skopiuj i wklej metodę **DeleteFamilyDocument** poniżej metody **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do drugiego wykonywania zapytania.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

## <a id="DeleteDatabase"></a>Usuwanie bazy danych

Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kolekcji, dokumentów itd.). Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej dokumentu, aby usunąć całą bazę danych i wszystkie zasoby podrzędne.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Wybierz przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

## <a id="Run"></a>Uruchamianie aplikacji konsolowej C#

Wybierz przycisk **DocumentDBGettingStarted** w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Dane wyjściowe aplikacji rozpoczynania pracy powinny zostać wyświetlone w oknie konsoli. Dane wyjściowe będą pokazywały wyniki dodanych zapytań i powinny odpowiadać poniższemu przykładowemu tekstowi.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Pomyślnie ukończono ten samouczek i utworzono działającą aplikację konsolową C#.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób kompilowania aplikacji .NET Core w celu zarządzania danymi zapisanymi na koncie interfejsu API SQL usługi Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

> [!div class="nextstepaction"]
> [Tworzenie aplikacji konsolowej Java przy użyciu konta interfejsu API SQL usługi Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
