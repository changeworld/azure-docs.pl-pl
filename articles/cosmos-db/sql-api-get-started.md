---
title: 'Azure Cosmos DB: rozpoczynanie pracy z interfejsem API SQL — samouczek | Microsoft Docs'
description: Samouczek, który pokazuje tworzenie bazy danych w trybie online i aplikacji konsolowej C# przy użyciu interfejsu API SQL.
keywords: samouczek nosql, baza danych online, aplikacja konsolowa c#
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/16/2017
ms.author: sngun
ms.openlocfilehash: c4f86b9fa949c854b557812a41aa9d86a11ecc94
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636989"
---
# <a name="azure-cosmos-db-sql-api-getting-started-tutorial"></a>Azure Cosmos DB: rozpoczynanie pracy z interfejsem API SQL — samouczek

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Witamy w samouczku ułatwiającym rozpoczęcie pracy z interfejsem API SQL w usłudze Azure Cosmos DB! W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

W tym samouczku opisano następujące czynności:

* Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
* Konfigurowanie rozwiązania Visual Studio
* Tworzenie bazy danych w trybie online
* Tworzenie kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Zastępowanie dokumentu
* Usuwanie dokumentu
* Usuwanie bazy danych

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Przeskocz do sekcji [Pobieranie kompletnego rozwiązania samouczka NoSQL](#GetSolution), aby uzyskać krótkie instrukcje.

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie rozwiązania Visual Studio](#SetupVS). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Krok 2. Konfigurowanie rozwiązania programu Visual Studio
1. Otwórz program **Visual Studio 2017** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Szablony** / **Visual C#** / **Aplikacja konsolowa**, podaj nazwę projektu, a następnie kliknij przycisk **OK**.
   ![Zrzut ekranu przedstawiający okno Nowy projekt](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
4. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w ramach rozwiązania Visual Studio, a następnie kliknij pozycję **Zarządzaj pakietami NuGet...**
    
    ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. Na karcie **NuGet** kliknij pozycję **Przeglądaj** i wpisz ciąg **azure documentdb** w polu wyszukiwania.
6. W wynikach znajdź pozycję **Microsoft.Azure.DocumentDB** i kliknij przycisk **Zainstaluj**.
   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   ![Zrzut ekranu przedstawiający menu NuGet służące do znajdowania zestawu SDK klienta usługi Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Jeśli zostanie wyświetlony komunikat dotyczący przejrzenia zmian wprowadzonych w rozwiązaniu, kliknij przycisk **OK**. Jeśli wyświetlany jest komunikat o akceptacji licencji, kliknij pozycję **Akceptuję**.

Wspaniale! Teraz, po zakończeniu konfigurowania, zacznijmy pisanie kodu. Ukończony projekt kodu z tego samouczka można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB
Najpierw dodaj te odwołania na początku aplikacji C# w pliku Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> W celu ukończenia tego samouczka upewnij się, że dodano zależności opisane powyżej.
> 
> 

Dodaj teraz te dwie stałe i zmienną *client* poniżej klasy publicznej *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Następnie wróć do witryny [Azure Portal](https://portal.azure.com), aby pobrać adres URL punktu końcowego i klucz podstawowy. Adres URL punktu końcowego i klucz podstawowy są niezbędne, aby aplikacja wiedziała, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji.

W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos DB i kliknij pozycję **Klucze**.

Skopiuj identyfikator URI z portalu i wklej go w miejsce `<your endpoint URL>` w pliku program.cs. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `<your primary key>`.

![Zrzut ekranu przedstawiający witrynę Azure Portal używaną przez samouczek NoSQL do tworzenia aplikacji konsolowej C#. Przedstawia konto usługi Azure Cosmos DB z wyróżnionym AKTYWNYM centrum, przyciskiem KLUCZE wyróżnionym na stronie konta usługi Azure Cosmos DB oraz wartościami IDENTYFIKATOR URI, KLUCZ PODSTAWOWY i KLUCZ POMOCNICZY wyróżnionymi na stronie Klucze][keys]

Następnie zaczniemy budowanie aplikacji, tworząc nowe wystąpienie klasy **DocumentClient**.

Poniżej metody **Main** dodaj to nowe zadanie asynchroniczne o nazwie **GetStartedDemo**, które utworzy nowe wystąpienie klasy **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

Dodaj następujący kod w celu uruchomienia zadania asynchronicznego z metody **Main**. Metoda **Main** będzie przechwytywać wyjątki i zapisywać je w konsoli.

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

Naciśnij klawisz **F5**, aby uruchomić aplikację. W danych wyjściowych okna konsoli wyświetlany jest komunikat `End of demo, press any key to exit.` potwierdzający, że połączenie zostało nawiązane.  Następnie można zamknąć okno konsoli. 

Gratulacje! Pomyślnie nawiązano połączenie z kontem usługi Azure Cosmos DB. Teraz przyjrzyjmy się pracy z zasobami usługi Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych
Przed dodaniem kodu na potrzeby tworzenia bazy danych dodaj metodę pomocnika na potrzeby pisania w konsoli.

Skopiuj i wklej metodę **WriteToConsoleAndPromptToContinue** poniżej metody **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

[Bazę danych](databases-containers-items.md#azure-cosmos-databases) usługi Azure Cosmos DB można utworzyć za pomocą metody [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) klasy **DocumentClient**. Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach.

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia klienta. Spowoduje to utworzenie bazy danych o nazwie *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono bazę danych usługi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5. Tworzenie kolekcji
> [!WARNING]
> Za pomocą metody **CreateDocumentCollectionIfNotExistsAsync** zostanie utworzona nowa kolekcja z zarezerwowaną przepływnością, co ma wpływ na cenę. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Kolekcję można utworzyć za pomocą metody [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia bazy danych. Spowoduje to utworzenie kolekcji dokumentów o nazwie *FamilyCollection*.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono kolekcję dokumentów Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6. Tworzenie dokumentów JSON
Dokument można utworzyć za pomocą metody [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) klasy **DocumentClient**. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Można teraz wstawić jeden lub więcej dokumentów. Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć [narzędzia migracji danych](import-data.md) usługi Azure Cosmos DB, aby zaimportować dane do bazy danych.

Najpierw należy utworzyć klasę **Family**, która będzie reprezentowała obiekty przechowywane w usłudze Azure Cosmos DB w tym przykładzie. Zostaną również utworzone podklasy **Parent**, **Child**, **Pet** i **Address**, które są używane w ramach klasy **Family**. Należy pamiętać, że dokumenty muszą mieć właściwość **Id** serializowaną jako **id** w formacie JSON. Utwórz te klasy, dodając następujące podklasy wewnętrzne po metodzie **GetStartedDemo**.

Skopiuj i wklej klasy **Family**, **Parent**, **Child**, **Pet** i **Address** poniżej metody **WriteToConsoleAndPromptToContinue**.

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

Skopiuj i wklej metodę **CreateFamilyDocumentIfNotExists** poniżej klasy **Address**.

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

Następnie wstaw dwa dokumenty, jeden dla rodziny Andersen i jeden dla rodziny Wakefield.

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia kolekcji dokumentów.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono dwa dokumenty usługi Azure Cosmos DB.  

![Diagram pokazujący hierarchiczną relację między kontem, bazą danych w trybie online, kolekcją i dokumentami używanymi przez samouczek NoSQL do tworzenia aplikacji konsolowej C#](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji.  Następujący przykładowy kod przedstawia różne zapytania — przy użyciu składni SQL usługi Azure Cosmos DB oraz LINQ — które można uruchomić względem dokumentów wstawionych w poprzednim kroku.

Skopiuj i wklej metodę **ExecuteSimpleQuery** poniżej metody **CreateFamilyDocumentIfNotExists**.

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

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do tworzenia drugiego dokumentu.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie wykonano zapytanie względem kolekcji usługi Azure Cosmos DB.

Na poniższym diagramie przedstawiono sposób, w jaki składnia zapytania SQL usługi Azure Cosmos DB jest wywoływana względem utworzonej kolekcji. Ta sama logika dotyczy również zapytania LINQ.

![Diagram pokazujący zakres i znaczenie zapytania używanego przez samouczek NoSQL do utworzenia aplikacji konsolowej C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Słowo kluczowe [FROM](sql-api-sql-query.md#FromClause) jest opcjonalne w zapytaniu, ponieważ zapytania usługi Azure Cosmos DB mają już zakres określony do pojedynczej kolekcji. W związku z tym klauzula "FROM Families f" może być zamieniona na "FROM root r" lub dowolną inną wybraną nazwę zmiennej. Usługa Azure Cosmos DB będzie wnioskowała, że zmienne Families, root lub wybrana nazwa zmiennej domyślnie odwołują się do bieżącej kolekcji.

## <a id="ReplaceDocument"></a>Krok 8. Zastępowanie dokumentu JSON
Usługa Azure Cosmos DB obsługuje zastępowanie dokumentów JSON.  

Skopiuj i wklej metodę **ReplaceFamilyDocument** poniżej metody **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do wykonywania zapytania (na końcu metody). Po zastąpieniu dokumentu spowoduje to ponowne uruchomienie tego samego zapytania, aby wyświetlić zmieniony dokument.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie zastąpiono dokument usługi Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9. Usuwanie dokumentu JSON
Usługa Azure Cosmos DB obsługuje usuwanie dokumentów JSON.  

Skopiuj i wklej metodę **DeleteFamilyDocument** poniżej metody **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do wykonywania drugiego zapytania (na końcu metody).

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto dokument usługi Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych
Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kolekcji, dokumentów itd.).

Skopiuj i wklej następujący kod do metody **GetStartedDemo** poniżej kodu służącego do usuwania dokumentu, aby usunąć całą bazę danych i wszystkie zasoby podrzędne.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto bazę danych usługi Azure Cosmos DB.

## <a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej C#
Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania.

Dane wyjściowe aplikacji rozpoczynania pracy powinny zostać wyświetlone w oknie konsoli. Dane wyjściowe będą pokazywały wyniki dodanych zapytań i powinny odpowiadać poniższemu przykładowemu tekstowi.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
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

Gratulacje! Pomyślnie ukończono ten samouczek i utworzono działającą aplikację konsolową C#.

## <a id="GetSolution"></a> Pobieranie kompletnego rozwiązania samouczka
Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać przykłady kodu, możesz uzyskać je w serwisie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Aby skompilować rozwiązanie GetStarted, niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* [Konto usługi Azure Cosmos DB][cosmos-db-create-account].
* Rozwiązanie [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) dostępne w witrynie GitHub.

Aby przywrócić odwołania do zestawu SDK dla platformy .NET usługi Azure Cosmos DB w programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie **GetStarted** w Eksploratorze rozwiązań, a następnie kliknij polecenie **Włącz przywracanie pakietów NuGet**. Następnie w pliku App.config zaktualizuj wartości EndpointUrl i AuthorizationKey zgodnie z opisem w kroku [Łączenie się z kontem usługi Azure Cosmos DB](#Connect).

To wszystko — skompiluj projekt i gotowe!


## <a name="next-steps"></a>Następne kroki
* Potrzebujesz bardziej złożonego samouczka platformy ASP.NET MVC? Zobacz [Samouczek platformy ASP.NET MVC: opracowywanie aplikacji internetowych za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md).
* Czy chcesz wykonać testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB? Zobacz [Performance and scale testing with Azure Cosmos DB](performance-testing.md) (Testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB)
* Dowiedz się, jak [monitorować żądania, użycie i magazyn usługi Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).
* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](https://docs.microsoft.com/azure/cosmos-db/introduction)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
[cosmos-db-create-account]: create-sql-api-dotnet.md#create-account
