---
title: Tworzenie aplikacji konsolowej .NET do zarządzania danymi na koncie usługi Azure Cosmos DB — interfejs API SQL
description: Samouczek, który pokazuje tworzenie bazy danych online i C# aplikacji konsolowej przy użyciu interfejsu API SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 7574985dbcc502d03bc886c7651c859b22968c5f
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596090"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tworzenie aplikacji konsolowej .NET do zarządzania danymi na koncie usługi Azure Cosmos DB — interfejs API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (wersja zapoznawcza)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (wersja zapoznawcza)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Samouczek wprowadzający get interfejsu API SQL usługi Azure Cosmos DB — Zapraszamy!. Po ukończeniu tego samouczka będziesz mieć aplikację konsolową, która tworzy zasoby usługi Azure Cosmos DB zapytania.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie konta usługi Azure Cosmos DB i nawiązać z nim
> - Konfigurowanie rozwiązania programu Visual Studio
> - Tworzenie bazy danych
> - Tworzenie kolekcji
> - Tworzenie dokumentów JSON
> - Zapytanie do kolekcji
> - Aktualizowanie dokumentów JSON
> - Usuń dokument
> - Usuwanie bazy danych

## <a name="prerequisites"></a>Wymagania wstępne

Program Visual Studio 2017 z przepływem pracy programowania dla platformy Azure zainstalowany:
- Możesz pobrać i używać **bezpłatne** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**. 

Subskrypcja platformy Azure lub konto bezpłatnej wersji próbnej usługi Cosmos DB:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [emulatora usługi Azure Cosmos DB](local-emulator.md) Aby skonfigurować emulator. Następnie uruchom samouczek w [konfigurowanie rozwiązania programu Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Pobieranie ukończone rozwiązanie

Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz przykłady kodu, możesz pobrać kompletne rozwiązanie z [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Aby uruchomić pobrany kompletnego rozwiązania: 

1. Upewnij się, że masz [wymagania wstępne](#prerequisites) zainstalowane. 
1. Otwórz pobrany *GetStarted.sln* plik rozwiązania w programie Visual Studio.
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **GetStarted** projektu, a następnie wybierz **Zarządzaj pakietami NuGet**.
1. Na **NuGet** zaznacz **przywrócić** Aby przywrócić odwołania do zestawu SDK .NET usługi Azure Cosmos DB.
1. W *App.config* pliku, zaktualizuj `EndpointUrl` i `PrimaryKey` wartości zgodnie z opisem w [Połącz z kontem usługi Azure Cosmos DB](#Connect) sekcji.
1. Wybierz **debugowania** > **Rozpocznij bez debugowania** lub naciśnij **Ctrl**+**F5** Aby skompilować i uruchomić aplikację.

## <a name="create-an-azure-cosmos-db-account"></a>Utwórz konto usługi Azure Cosmos DB

Wykonaj te instrukcje, aby utworzyć konto usługi Azure Cosmos DB w witrynie Azure portal. Jeśli masz już konto usługi Azure Cosmos DB, przejdź do sekcji [konfigurowanie rozwiązania programu Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Konfigurowanie rozwiązania programu Visual Studio

1. W programie Visual Studio 2017, wybierz **pliku** > **New** > **projektu**.
   
1. W **nowy projekt** okno dialogowe, wybierz opcję **Visual C#**   >  **Aplikacja konsoli (.NET Framework)**, nazwij swój projekt *AzureCosmosDBApp* , a następnie wybierz pozycję **OK**.
   
   ![Zrzut ekranu przedstawiający okno Nowy projekt](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **AzureCosmosDBApp** projektu, a następnie wybierz **Zarządzaj pakietami NuGet**.
   
   ![Menu kontekstowego projektu](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Na **NuGet** zaznacz **Przeglądaj**, a następnie wprowadź *usługa azure documentdb* w polu wyszukiwania.
   
1. Znajdź i zaznacz **Microsoft.Azure.DocumentDB**i wybierz **zainstalować** Jeśli jeszcze nie jest zainstalowany.
   
   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Jeśli używasz platformy .NET Core, zobacz [dokumentacji platformy .NET Core](./sql-api-dotnetcore-get-started.md).

   ![Zrzut ekranu przedstawiający NuGet Menu służące do znajdowania zestawu SDK klienta usługi Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Jeśli zostanie wyświetlony komunikat o podgląd zmian w rozwiązaniu, wybierz opcję **OK**. Jeśli zostanie wyświetlony komunikat o akceptacji licencji, wybierz opcję **akceptuję**.

## <a id="Connect"></a>Połączenie z kontem usługi Azure Cosmos DB

Teraz zacznij pisanie kodu. Pełne *Project.cs* plik znajduje się w tym samouczku [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. W **Eksploratora rozwiązań**, wybierz opcję *Program.cs*i w edytorze kodu Dodaj następujące odwołania do początku pliku:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Następnie dodaj następujące dwie stałe i `client` zmienną `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. Adres URL punktu końcowego i klucz podstawowy umożliwiają łączenie się z kontem usługi Azure Cosmos DB i konto usługi Azure Cosmos DB ufała połączeniu aplikacji. Kopiowanie kluczy z [witryny Azure portal](https://portal.azure.com)i wklej je do kodu. 

   
   1. W nawigacji po lewej stronie konta usługi Azure Cosmos DB, wybierz **klucze**.
      
      ![Wyświetlanie i kopiowanie kluczy dostępu w witrynie Azure portal](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. W obszarze **klucze odczytu i zapisu**, kopia **URI** wartości przy użyciu przycisku kopiowania po prawej stronie, a następnie wklej go do `<your endpoint URL>` w *Program.cs*. Na przykład: 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Kopiuj **klucza podstawowego** wartość i wklej go w `<your primary key>` w *Program.cs*. Na przykład: 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Po `Main` metody, Dodaj nowe zadanie asynchroniczne o nazwie `GetStartedDemo`, która tworzy nową `DocumentClient` o nazwie `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```

   Jeśli używasz obiektu serwera proxy do łączenia z usługą Azure Cosmos DB, należy w zamian użyć poniższy blok kodu można utworzyć obiektu DocumentClient. Przykład podany w tym dokumencie nie używa obiekt serwera proxy, więc w poniższym przykładzie przedstawiono tylko dla Twojej informacji:

   ```csharp
   HttpClientHandler handler = new HttpClientHandler()
   {
     Proxy = proxyObject
     UseProxy = true,
   };

   //Pass handler to the constructor of DocumentClient.
   DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey, handler);
   
   ```
   
1. Dodaj następujący kod do `Main` metodę, aby uruchomić `GetStartedDemo` zadania. `Main` Metoda przechwytuje wyjątki i zapisuje je w konsoli.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji. 
   
1. Gdy zostanie wyświetlony komunikat **zakończenia z pokazu naciśnij dowolny klawisz, aby zakończyć działanie** w oknie konsoli, oznacza to połączenie powiodło się. Naciśnij dowolny klawisz, aby zamknąć okno konsoli. 

Pomyślnie nawiązano połączenie konta usługi Azure Cosmos DB. Teraz pracować z niektórymi zasobami usługi Azure Cosmos DB.  

## <a name="create-a-database"></a>Tworzenie bazy danych

Usługi Azure Cosmos DB [bazy danych](databases-containers-items.md#azure-cosmos-databases) jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach. Tworzenie bazy danych przy użyciu [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) metody `DocumentClient` klasy. 

1. Przed dodaniem kodu na potrzeby tworzenia bazy danych dodaj metodę pomocnika na potrzeby pisania w konsoli. Skopiuj i wklej następujący kod `WriteToConsoleAndPromptToContinue` metody `GetStartedDemo` metody w kodzie.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Skopiuj i wklej następujący wiersz do Twojej `GetStartedDemo` metoda po `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` wiersza. Ten kod tworzy bazę danych o nazwie `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Pomyślnie utworzono bazę danych usługi Azure Cosmos DB. Możesz zobaczyć bazy danych w [witryny Azure portal](https://portal.azure.com) , wybierając **Eksplorator danych** w nawigacji po lewej stronie konta usługi Azure Cosmos DB. 

## <a id="CreateColl"></a>Tworzenie kolekcji

Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript. Tworzenie kolekcji przy użyciu [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) metody `DocumentClient` klasy. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** tworzy nową kolekcję z zarezerwowaną przepływnością, co ma implikacje cenowe. Aby uzyskać więcej informacji, odwiedź stronę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Skopiuj i wklej następujący kod, aby Twoje `GetStartedDemo` metody `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` wiersza. Ten kod umożliwia utworzenie kolekcji dokumentów o nazwie `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Pomyślnie utworzono kolekcję dokumentów Azure Cosmos DB. Możesz zobaczyć kolekcję w węźle usługi **FamilyDB** bazy danych w **Eksplorator danych** w witrynie Azure portal.  

## <a id="CreateDoc"></a>Tworzenie dokumentów JSON

Dokumenty są zdefiniowane przez użytkownika, dowolną zawartość JSON. Dokumenty muszą mieć właściwość Identyfikatora zserializowanym w formacie `id` w formacie JSON. Tworzenie dokumentów przy użyciu [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) metody `DocumentClient` klasy. 

> [!TIP]
> Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć usługi Azure Cosmos DB [narzędzia migracji danych](import-data.md) go zaimportować.
>

Poniższy kod tworzy i wstawia dwa dokumenty w kolekcji bazy danych. Najpierw należy utworzyć `Family` klasy, a `Parent`, `Child`, `Pet`, i `Address` podklasy do użycia w ramach `Family`. Następnie należy utworzyć `CreateFamilyDocumentIfNotExists` metody, a następnie utwórz i Wstaw dwa dokumenty. 

1. Skopiuj i wklej następujący kod `Family`, `Parent`, `Child`, `Pet`, i `Address` klasy `WriteToConsoleAndPromptToContinue` metody w kodzie.
   
   ```csharp
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
   
1. Skopiuj i wklej następujący kod `CreateFamilyDocumentIfNotExists` metody `Address` klasy właśnie został dodany.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Skopiuj i wklej następujący kod na końcu Twojej `GetStartedDemo` metoda po `await client.CreateDocumentCollectionIfNotExistsAsync` wiersza. Ten kod tworzy i wstawia dwa dokumenty, jeden dla rodziny Andersen i Wakefield.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Pomyślnie utworzono dwa dokumenty usługi Azure Cosmos DB. Można wyświetlić dokumenty w ramach Twojej **FamilyDB** bazy danych i **FamilyCollection** kolekcji w **Eksplorator danych** w witrynie Azure portal.   

![Diagram pokazujący hierarchiczną relację między konta, bazy danych online, kolekcję i dokumenty](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Wykonywanie zapytania względem zasobów usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](how-to-sql-query.md) względem dokumentów JSON przechowywanych w kolekcjach. Następujący przykładowy kod używa składni LINQ i SQL usługi Azure Cosmos DB, uruchamiać zapytanie przykładowe dokumenty.

1. Skopiuj i wklej następujący kod `ExecuteSimpleQuery` metody `CreateFamilyDocumentIfNotExists` metody w kodzie.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Skopiuj i wklej następujący kod na końcu Twojej `GetStartedDemo` metoda po `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` wiersza.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Poprzednie zapytanie zwraca pełny element dla rodziny Andersen. Zostało pomyślnie przesłane zapytanie względem kolekcji usługi Azure Cosmos DB.

Na poniższym diagramie przedstawiono, jak składnia zapytania SQL usługi Azure Cosmos DB wywołuje względem kolekcji. Ta sama logika dotyczy zapytania LINQ.

![Diagram pokazujący zakres i znaczenie zapytania używanego przez samouczek NoSQL do utworzenia aplikacji konsolowej C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

[FROM](how-to-sql-query.md#FromClause) słów kluczowych w kwerendzie SQL jest opcjonalny, ponieważ zapytania usługi Azure Cosmos DB mają już zakres określony jako jedna kolekcja. Można wymienić `FROM Families f` z `FROM root r`, lub dowolnej innej wybranej nazwy zmiennej. Usługa Azure Cosmos DB ustali, że `Families`, `root`, lub nazwę zmiennej, możesz wybrać, który odwołuje się do bieżącej kolekcji.

## <a id="ReplaceDocument"></a>Aktualizowanie dokumentów JSON

Interfejs API usługi Azure Cosmos DB SQL obsługuje aktualizowanie i zastępowanie dokumentów JSON.  

1. Skopiuj i wklej następujący kod `ReplaceFamilyDocument` metody `ExecuteSimpleQuery` metody w kodzie.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Skopiuj i wklej następujący kod na końcu Twojej `GetStartedDemo` metoda po `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` wiersza. Kod aktualizuje dane w jednym z dokumentów, a następnie wykonuje zapytanie ponownie, aby wyświetlić zmieniony dokument.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Dane wyjściowe zapytanie pokazuje, że `Grade` dla rodziny Andersen dziecka aktualizowane na podstawie `5` do `6`. Został pomyślnie zaktualizowany i zastąpione dokument usługi Azure Cosmos DB. 

## <a id="DeleteDocument"></a>Usuwanie dokumentu JSON

Interfejs API usługi Azure Cosmos DB SQL obsługuje usuwanie dokumentów JSON.  

1. Skopiuj i wklej następujący kod `DeleteFamilyDocument` metody `ReplaceFamilyDocument` metody.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Skopiuj i wklej następujący kod na końcu Twojej `GetStartedDemo` metodę po drugiej `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` wiersza.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Została pomyślnie usunięto dokument usługi Azure Cosmos DB. 

## <a id="DeleteDatabase"></a>Usuwanie bazy danych

Usuń bazę danych, utworzonego do usunięcia go i wszystkie jej zasoby podrzędne, łącznie z kolekcji i dokumentów. 

1. Skopiuj i wklej następujący kod na końcu Twojej `GetStartedDemo` metoda po `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` wiersza. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Naciśnij klawisz **F5** do uruchomienia aplikacji.

Zostały pomyślnie usunięte bazy danych Azure Cosmos DB. Można zobaczyć w **Eksplorator danych** dla swojego konta usługi Azure Cosmos DB, bazy danych FamilyDB zostanie usunięty. 

## <a id="Run"></a>Uruchamianie całego C# aplikacji konsoli

Naciśnij klawisz **F5** w programie Visual Studio, aby skompilować i uruchomić pełne C# aplikacji konsoli w trybie debugowania. Powinny zostać wyświetlone następujące dane wyjściowe w oknie konsoli:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Gratulacje! Możesz ukończyć samouczek i utworzono działającą C# aplikację konsolową, która tworzy kwerendy, aktualizuje i usuwa zasoby usługi Azure Cosmos DB.  

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](introduction.md)
* Aby uzyskać bardziej złożonego samouczka platformy ASP.NET MVC, zobacz [samouczek ASP.NET MVC: Opracowywanie aplikacji internetowych za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md).
* Aby wykonać skalowalności i wydajności, testowanie za pomocą usługi Azure Cosmos DB, zobacz [wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB](performance-testing.md).
* Aby dowiedzieć się, jak monitorować usługę Azure Cosmos DB żądań i użycie magazynu, zobacz [monitorować konta](monitor-accounts.md).
* Uruchom zapytania względem przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).

