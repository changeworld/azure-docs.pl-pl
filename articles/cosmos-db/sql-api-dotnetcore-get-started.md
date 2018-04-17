---
title: 'Azure DB rozwiązania Cosmos: Wprowadzenie do korzystania z samouczka .NET Core interfejsu API SQL | Dokumentacja firmy Microsoft'
description: Samouczek, który powoduje utworzenie bazy danych w trybie online i aplikacji konsolowej C# za pomocą usługi Azure rozwiązania Cosmos bazy danych SQL interfejsu API platformy .NET Core SDK.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/12/2018
ms.author: sngun
ms.custom: devcenter
ms.openlocfilehash: 81c7767852b25c6e075c48feb245227643799316
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-getting-started-with-the-sql-api-and-net-core"></a>Azure rozwiązania Cosmos bazy danych: Wprowadzenie do korzystania z interfejsu API SQL i .NET Core
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Zapraszamy do interfejsu API SQL dla bazy danych rozwiązania Cosmos Azure Wprowadzenie — samouczek platformy .NET Core! W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

Ten samouczek obejmuje:

* Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
* Konfigurowanie rozwiązania Visual Studio
* Tworzenie bazy danych w trybie online
* Tworzenie kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Zastępowanie dokumentu
* Usuwanie dokumentu
* Usuwanie bazy danych

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). Przeskocz do sekcji [Pobieranie kompletnego rozwiązania](#GetSolution), aby uzyskać krótkie instrukcje.

Chcesz tworzyć Xamarin iOS, Android lub formularzy aplikacji przy użyciu interfejsu API SQL i .NET Core SDK? Zobacz [tworzenie aplikacji dla urządzeń przenośnych dzięki platformie Xamarin i usłudze Azure DB rozwiązania Cosmos](mobile-apps-with-xamarin.md).

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Jeśli nie masz jeszcze programu Visual Studio 2017 r zainstalowany, możesz pobrać i korzystać z BEZPŁATNEJ [programu Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Jeśli tworzysz aplikację systemu Windows platformy Uniwersalnej, należy użyć **programu Visual Studio 2017 z wersją 15.4** lub nowszej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
    * Jeśli pracujesz nad MacOS lub Linux, można tworzyć aplikacje .NET Core w wierszu polecenia, instalując [.NET Core SDK](https://www.microsoft.com/net/core#macos) platformy wybranych przez użytkownika. 
    * Podczas pracy w systemie Windows, można tworzyć aplikacje .NET Core w wierszu polecenia, instalując [.NET Core SDK](https://www.microsoft.com/net/core#windows). 
    * Możesz użyć własnych edytora lub pobrać [Visual Studio Code](https://code.visualstudio.com/), który jest wolny i działa w systemach Windows, Linux i MacOS. 

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie rozwiązania Visual Studio](#SetupVS). Jeśli używasz emulatora usługi Azure rozwiązania Cosmos bazy danych, wykonaj kroki opisane w temacie [Azure rozwiązania Cosmos DB emulatora](local-emulator.md) skonfigurować emulatora i przejść od razu do [konfigurowanie rozwiązania Visual Studio](#SetupVS).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Krok 2. Konfigurowanie rozwiązania programu Visual Studio
1. Otwórz program **Visual Studio 2017** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Szablony** / **Visual C#** / **.NET Core**/**Aplikacja konsolowa (.NET Core)**, podaj nazwę projektu **DocumentDBGettingStarted**, a następnie kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający okno Nowy projekt](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **DocumentDBGettingStarted**.
5. Następnie bez opuszczania menu kliknij pozycję **Zarządzaj pakietami NuGet...** .

   ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. Na karcie **NuGet** kliknij pozycję **Przeglądaj** w górnej części okna i wpisz ciąg **azure documentdb** w polu wyszukiwania.
7. W wynikach znajdź pozycję **Microsoft.Azure.DocumentDB.Core** i kliknij przycisk **Zainstaluj**.
   Identyfikator pakietu dla biblioteki jest [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Jeśli elementem docelowym jest wersja programu .NET Framework (na przykład net461), która nie jest obsługiwana przez ten pakiet .NET Core NuGet, użyj elementu [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) obsługującego wszystkie wersje programu .NET Framework, począwszy od wersji .NET Framework 4.5.
8. Po wyświetleniu monitów zaakceptuj instalacje pakietów NuGet i umowę licencyjną.

Wspaniale! Teraz, że instalacja została ukończona, Zacznijmy pisanie kodu. Ukończony projekt kodu z tego samouczka można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB
Najpierw dodaj te odwołania na początku aplikacji C# w pliku Program.cs:

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

> [!IMPORTANT]
> W celu ukończenia tego samouczka upewnij się, że dodano zależności opisane powyżej.

Dodaj teraz te dwie stałe i zmienną *client* poniżej klasy publicznej *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Następny, przejdź do [portalu Azure](https://portal.azure.com) można pobrać identyfikator URI i klucz podstawowy. Azure rozwiązania Cosmos DB URI oraz primary key są niezbędne, aplikacja wiedziała, gdzie można połączyć się i Azure DB rozwiązania Cosmos zaufania połączeniu aplikacji.

W portalu Azure, przejdź do swojego konta bazy danych rozwiązania Cosmos platformy Azure, a następnie kliknij przycisk **klucze**.

Skopiuj identyfikator URI z portalu i wklej go w miejsce `<your endpoint URI>` w pliku program.cs. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `<your key>`. Jeśli używasz emulatora usługi Azure Cosmos DB, użyj `https://localhost:8081` jako punktu końcowego i dobrze zdefiniowanego klucza autoryzacji z tematu [How to develop using the Azure Cosmos DB Emulator](local-emulator.md) (Programowanie przy użyciu emulatora usługi Azure Cosmos DB). Upewnij się, że zostały usunięte znaki < i >, ale pozostawiono podwójny cudzysłów wokół punktu końcowego i klucza.

![Zrzut ekranu przedstawiający witrynę Azure Portal używaną przez samouczek NoSQL do tworzenia aplikacji konsolowej C#. Przedstawia konto bazy danych Azure rozwiązania Cosmos z wyróżnionym AKTYWNYM Centrum, przyciskiem KLUCZE wyróżnionym na stronie konta bazy danych Azure rozwiązania Cosmos oraz wartościami identyfikator URI, klucz podstawowy i klucz POMOCNICZY wyróżnione na stronie kluczy][keys]

Zaczniemy tworzenie aplikacji rozpoczynania pracy, tworząc nowe wystąpienie klasy **DocumentClient**.

Poniżej metody **Main** dodaj to nowe zadanie asynchroniczne o nazwie **GetStartedDemo**, które utworzy nowe wystąpienie klasy **DocumentClient**.

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

Dodaj następujący kod w celu uruchomienia zadania asynchronicznego z metody **Main**. **Main** — metoda przechwytuje wyjątki i zapisywać je w konsoli.

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

Naciśnij przycisk **DocumentDBGettingStarted**, aby skompilować i uruchomić aplikację.

Gratulacje! Pomyślnie nawiązano połączenie z kontem usługi Azure Cosmos DB. Teraz przyjrzyjmy się pracy z zasobami usługi Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych
Przed dodaniem kodu na potrzeby tworzenia bazy danych dodaj metodę pomocnika na potrzeby pisania w konsoli.

Skopiuj i wklej metodę **WriteToConsoleAndPromptToContinue** poniżej metody **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Bazy danych programu Azure rozwiązania Cosmos [bazy danych](sql-api-resources.md#databases) można utworzyć za pomocą [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metody **DocumentClient** klasy. Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach.

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia klienta. Spowoduje to utworzenie bazy danych o nazwie *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono bazę danych usługi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5. Tworzenie kolekcji
> [!WARNING]
> **CreateDocumentCollectionAsync** tworzy nową kolekcję z zarezerwowaną przepływnością, co ma implikacje cenowe. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

[Kolekcję](sql-api-resources.md#collections) można utworzyć za pomocą metody [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia bazy danych. Spowoduje to utworzenie kolekcji dokumentów o nazwie *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono kolekcję dokumentów Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6. Tworzenie dokumentów JSON
[Dokument](sql-api-resources.md#documents) można utworzyć za pomocą metody [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) klasy **DocumentClient**. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Można teraz wstawić jeden lub więcej dokumentów. Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć Azure rozwiązania Cosmos DB [narzędzia migracji danych](import-data.md).

Najpierw należy utworzyć klasę **Family**, która będzie reprezentowała obiekty przechowywane w usłudze Azure Cosmos DB w tym przykładzie. Zostaną również utworzone podklasy **Parent**, **Child**, **Pet** i **Address**, które są używane w ramach klasy **Family**. Należy pamiętać, że dokumenty muszą mieć właściwość **Id** serializowaną jako **id** w formacie JSON. Utwórz te klasy, dodając następujące podklasy wewnętrzne po metodzie **GetStartedDemo**.

Skopiuj i wklej klasy **Family**, **Parent**, **Child**, **Pet** i **Address** poniżej metody **WriteToConsoleAndPromptToContinue**.

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

Następnie wstaw dwa dokumenty, jeden dla rodziny Andersen i jeden dla rodziny Wakefield.

Skopiuj i wklej kod następujący po ciągu `// ADD THIS PART TO YOUR CODE` do metody **GetStartedDemo** poniżej kodu służącego do tworzenia kolekcji dokumentów.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

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

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono dwa dokumenty usługi Azure Cosmos DB.  

![Diagram pokazujący hierarchiczną relację między kontem, bazą danych w trybie online, kolekcją i dokumentami używanymi przez samouczek NoSQL do tworzenia aplikacji konsolowej C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji.  Następujący przykładowy kod przedstawia różne zapytania — przy użyciu składni SQL usługi Azure Cosmos DB oraz LINQ — które można uruchomić względem dokumentów wstawionych w poprzednim kroku.

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

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie wykonano zapytanie względem kolekcji usługi Azure Cosmos DB.

Na poniższym diagramie przedstawiono sposób, w jaki składnia zapytania SQL usługi Azure Cosmos DB jest wywoływana względem utworzonej kolekcji. Ta sama logika dotyczy również zapytania LINQ.

![Diagram pokazujący zakres i znaczenie zapytania używanego przez samouczek NoSQL do utworzenia aplikacji konsolowej C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

[FROM](sql-api-sql-query.md#FromClause) — słowo kluczowe jest opcjonalne w zapytaniu, ponieważ zapytania bazy danych Azure rozwiązania Cosmos mają już zakres określony jako jedna kolekcja. W związku z tym klauzula "FROM Families f" może być zamieniona na "FROM root r" lub dowolną inną wybraną nazwę zmiennej. Azure DB rozwiązania Cosmos wnioskuje rodzin, root lub nazwę zmiennej, która została wybrana opcja odwołanie do bieżącej kolekcji domyślnie.

## <a id="ReplaceDocument"></a>Krok 8. Zastępowanie dokumentu JSON
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

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie zastąpiono dokument usługi Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9. Usuwanie dokumentu JSON
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

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto dokument usługi Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych
Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kolekcji, dokumentów itd.).

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do usuwania dokumentu, aby usunąć całą bazę danych i wszystkie zasoby podrzędne.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Naciśnij przycisk **DocumentDBGettingStarted**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto bazę danych usługi Azure Cosmos DB.

## <a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej C#
Naciśnij przycisk **DocumentDBGettingStarted** w programie Visual Studio, aby skompilować aplikację w trybie debugowania.

Powinny pojawić się dane wyjściowe aplikacji rozpoczynania pracy w oknie konsoli. Dane wyjściowe będą pokazywały wyniki dodanych zapytań i powinny odpowiadać poniższemu przykładowemu tekstowi.

```
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

Gratulacje! Pomyślnie ukończono ten samouczek i utworzono działającą aplikację konsolową C#.

## <a id="GetSolution"></a> Pobieranie kompletnego rozwiązania samouczka
Do utworzenia rozwiązania GetStarted, które zawiera wszystkie przykłady w tym artykule, będą potrzebne następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* [Konta bazy danych Azure rozwiązania Cosmos][create-sql-api-dotnet.md#create-account].
* Rozwiązanie [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) dostępne w witrynie GitHub.

Aby przywrócić odwołania do interfejsu API SQL Azure rozwiązania Cosmos DB .NET Core SDK w programie Visual Studio, kliknij prawym przyciskiem myszy **GetStarted** rozwiązania w Eksploratorze rozwiązań, a następnie kliknij przycisk **Włącz Przywracanie pakietu NuGet**. Następnie w pliku Program.cs zaktualizuj wartości EndpointUrl i AuthorizationKey zgodnie z opisem w [Połącz z konta bazy danych Azure rozwiązania Cosmos](#Connect).

## <a name="next-steps"></a>Kolejne kroki
* Potrzebujesz bardziej złożonego samouczka platformy ASP.NET MVC? Zobacz [samouczek programu ASP.NET MVC: opracowywanie aplikacji z bazy danych Azure rozwiązania Cosmos sieci Web](sql-api-dotnet-application.md).
* Aby opracować Xamarin iOS, Android lub formularzy aplikacji przy użyciu interfejsu API SQL dla zestawu SDK usługi Azure rozwiązania Cosmos DB .NET Core? Zobacz [tworzenie aplikacji dla urządzeń przenośnych dzięki platformie Xamarin i usłudze Azure DB rozwiązania Cosmos](mobile-apps-with-xamarin.md).
* Czy chcesz wykonać testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB? Zobacz [wydajności i skalowania testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md)
* Dowiedz się, jak [żądań, użycia i magazynu bazy danych rozwiązania Cosmos Azure Monitor](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).
* Aby dowiedzieć się więcej na temat modelu programowania, zobacz [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/).

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
