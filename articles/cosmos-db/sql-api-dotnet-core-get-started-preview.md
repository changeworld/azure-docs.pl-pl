---
title: Tworzenie aplikacji konsolowej .NET Core do zarządzania danymi na koncie interfejsu API SQL usługi Azure Cosmos DB (zestaw SDK w wersji 3 — wersja zapoznawcza)
description: Samouczek, który pokazuje tworzenie bazy danych w trybie online i aplikacji konsolowej C# przy użyciu zestawu SDK dla platformy .NET Core interfejsu API SQL usługi Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 828d0ced775d46b0e966969acb3220d2a678c57b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686053"
---
# <a name="build-a-net-core-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Tworzenie aplikacji konsolowej .NET Core do zarządzania danymi na koncie interfejsu API SQL usługi Azure Cosmos DB (zestaw SDK w wersji 3 — wersja zapoznawcza)

> [!div class="op_single_selector"]
>
> - [.NET Core (wersja zapoznawcza)](sql-api-dotnet-core-get-started-preview.md)
> - [.NET Core](sql-api-dotnetcore-get-started.md)
> - [.NET (wersja zapoznawcza)](sql-api-dotnet-get-started-preview.md)
> - [.NET](sql-api-get-started.md)
> - [Java](sql-api-java-get-started.md)
> - [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> - [Node.js](sql-api-nodejs-get-started.md)

Zapraszamy do samouczka wprowadzającego do interfejsu API SQL usługi Azure Cosmos DB na platformie .NET Core. W tym samouczku zostanie utworzona aplikacja konsolowa .NET Core, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania. W tym samouczku jest używana [wersja 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) zestawu SDK .NET usługi Azure Cosmos DB, przeznaczona dla programu [.NET Standard 2.0](https://docs.microsoft.com/dotnet/standard/net-standard).

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
>
> - Tworzenie konta usługi Azure Cosmos i łączenie się z nim
> - Konfigurowanie projektu w programie Visual Studio
> - Tworzenie bazy danych i kontenera
> - Dodawanie elementów do kontenera
> - Wykonywanie zapytań względem kontenera
> - Wykonywanie operacji CRUD na elemencie
> - Usuwanie bazy danych

Nie masz czasu na utworzenie aplikacji? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started). Przeskocz do sekcji [Pobieranie kompletnego rozwiązania](#GetSolution), aby uzyskać krótkie instrukcje.

Chcesz utworzyć aplikację platformy Xamarin iOS, Android lub Forms przy użyciu interfejsu API SQL i zestawu SDK dla platformy .NET Core? Zobacz [Tworzenie aplikacji mobilnych za pomocą platformy Xamarin i usługi Azure Cosmos DB](mobile-apps-with-xamarin.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

- Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go bezpłatnie. W przypadku opracowywania aplikacji platformy uniwersalnej systemu Windows należy użyć programu **Visual Studio 2017 w wersji 15.4** lub nowszej. Podczas instalacji programu Visual Studio upewnij się, że jest włączone obciążenie **Programowanie na platformie Azure**.

  - Jeśli pracujesz na komputerze z systemem MacOS lub Linux, możesz opracowywać aplikacje platformy .NET Core z poziomu wiersza polecenia, instalując [zestaw SDK platformy .NET Core](https://www.microsoft.com/net/core#macos) dla wybranej platformy.

  - Jeśli pracujesz na komputerze z systemem Windows, możesz opracowywać aplikacje platformy .NET Core z poziomu wiersza polecenia, instalując [zestaw SDK platformy .NET Core](https://www.microsoft.com/net/core#windows).

  - Możesz użyć własnego edytora lub pobrać bezpłatny program [Visual Studio Code](https://code.visualstudio.com/) działający na komputerach z systemem Windows, Linux i MacOS.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Tworzenie konta usługi Azure Cosmos DB

Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie rozwiązania Visual Studio](#SetupVS). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie projektu programu Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Krok 2. Konfigurowanie projektu programu Visual Studio

1. Otwórz program **Visual Studio 2017** na komputerze.
1. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
1. W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C#** / **Aplikacja konsolowa (.NET Core)**, podaj nazwę projektu, a następnie kliknij przycisk **OK**.
   ![Zrzut ekranu przedstawiający okno nowy projekt](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-new-project.png)
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w ramach rozwiązania Visual Studio, a następnie kliknij pozycję **Zarządzaj pakietami NuGet...**

   ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-manage-nuget.png)

1. Na karcie **NuGet** kliknij pozycję **Przeglądaj** i wpisz ciąg **Microsoft.Azure.Cosmos** w polu wyszukiwania.
1. W wynikach znajdź pozycję **Microsoft.Azure.Cosmos**, a następnie kliknij przycisk **Zainstaluj**.
   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Zrzut ekranu przedstawiający NuGet Menu służące do znajdowania zestawu SDK klienta usługi Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

   Jeśli zostanie wyświetlony komunikat dotyczący przejrzenia zmian wprowadzonych w rozwiązaniu, kliknij przycisk **OK**. Jeśli wyświetlany jest komunikat o akceptacji licencji, kliknij pozycję **Akceptuję**.

Wspaniale! Teraz, po zakończeniu konfigurowania, zacznijmy pisanie kodu. Ukończony projekt kodu z tego samouczka można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started/).

## <a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB

1. Najpierw zastąp odwołania na początku aplikacji C# w pliku **Program.cs** następującymi odwołaniami:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. Następnie dodaj te stałe i zmienne do klasy publicznej `Program`.

   ```csharp
   public class Program
   {
       // ADD THIS PART TO YOUR CODE

       // The Azure Cosmos DB endpoint for running this sample.
       private static readonly string EndpointUri = "<your endpoint here>";
       // The primary key for the Azure Cosmos account.
       private static readonly string PrimaryKey = "<your primary key>";

       // The Cosmos client instance
       private CosmosClient cosmosClient;

       // The database we will create
       private CosmosDatabase database;

       // The container we will create.
       private CosmosContainer container;

       // The name of the database and container we will create
       private string databaseId = "FamilyDatabase";
       private string containerId = "FamilyContainer";
   }
   ```

   Osoby znające poprzednią wersję zestawu .NET SDK mogą być przyzwyczajone do terminów „kolekcja” i „dokument”. Ponieważ usługa Azure Cosmos DB obsługuje wiele modeli interfejsu API, w wersji 3.0+ zestawu .NET SDK używane są ogólne terminy „kontener” i „element”. Kontener może być kolekcją, wykresem lub tabelą. Element może być dokumentem, krawędzią/wierzchołkiem lub wierszem i stanowi zawartość znajdująca się w kontenerze. [Dowiedz się więcej na temat baz danych, kontenerów i elementów.](databases-containers-items.md)

1. Pobierz adres URL punktu końcowego i klucz podstawowy z witryny [Azure Portal](https://portal.azure.com).

   W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos DB i kliknij pozycję **Klucze**.

   Skopiuj identyfikator URI z portalu i wklej go w miejsce `<your endpoint URL>` w pliku `Program.cs`. Skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `<your primary key>`.

   ![Zrzut ekranu, można pobrać kluczy usługi Azure Cosmos DB w witrynie Azure portal](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Następnie utworzymy nowe wystąpienie klasy `CosmosClient` i skonfigurujemy niektóre elementy szkieletu naszego programu.

   Poniżej metody **Main** dodaj nowe zadanie asynchroniczne o nazwie **GetStartedDemoAsync**, które utworzy nowe wystąpienie klasy `CosmosClient`. Zadanie **GetStartedDemoAsync** służy jako punkt wejścia wywołujący metody, które działają na zasobach usługi Azure Cosmos DB.

   ```csharp
   public static async Task Main(string[] args)
   {
   }

   // ADD THIS PART TO YOUR CODE
   /*
       Entry point to call methods that operate on Azure Cosmos DB resources in this sample
   */
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
   }
   ```

1. Dodaj następujący kod w celu uruchomienia zadania asynchronicznego **GetStartedDemoAsync** w metodzie **Main**. Metoda **Main** będzie przechwytywać wyjątki i zapisywać je w konsoli.

   ```csharp
   public static async Task Main(string[] args)
   {
       // ADD THIS PART TO YOUR CODE
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();
       }
       catch (CosmosException de)
       {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
       }
       catch (Exception e)
       {
           Console.WriteLine("Error: {0}\n", e);
       }
       finally
       {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
       }
   }
   ```

1. Naciśnij klawisz **F5**, aby uruchomić aplikację. W danych wyjściowych okna konsoli wyświetlany jest komunikat `End of demo, press any key to exit.` potwierdzający nawiązanie połączenia z usługą Azure Cosmos DB. Następnie można zamknąć okno konsoli.

Gratulacje! Pomyślnie nawiązano połączenie z kontem usługi Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych

Bazę danych można utworzyć przy użyciu funkcji [**CreateDatabaseIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) lub [**CreateDatabaseAsync**](https://aka.ms/CosmosDotnetAPIDocs) klasy **CosmosDatabases**. Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach.

1. Skopiuj metodę **CreateDatabase** i wklej ją pod metodą **GetStartedDemoAsync**. Funkcja **CreateDatabase** utworzy nową bazę danych `FamilyDatabase` o identyfikatorze podanym w polu `databaseId` (jeśli jeszcze jej nie ma).

   ```csharp
   /*
       Create the database if it does not exist
   */
   private async Task CreateDatabase()
   {
       // Create a new database
       this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
       Console.WriteLine("Created Database: {0}\n", this.database.Id);
   }
   ```

1. Poniższy kod umożliwia wywołanie właśnie dodanej metody **CreateDatabase**. Skopiuj go i wklej pod wierszem, w którym jest tworzone wystąpienie klasy CosmosClient.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

       //ADD THIS PART TO YOUR CODE
       await this.CreateDatabase();
   }
   ```

   Na tym etapie kod powinien wyglądać mniej więcej tak i zawierać dane dotyczące punktu końcowego i klucza podstawowego. Pamiętaj o tym, że przestrzeń nazw zależy od nazwy projektu.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;

   namespace CosmosGettingStartedDotnetCoreTutorial
   {
       class Program
       {
           // The Azure Cosmos DB endpoint for running this sample.
           private static readonly string EndpointUri = "<your endpoint here>";
           // The primary key for the Azure Cosmos account.
           private static readonly string PrimaryKey = "<your primary key>";

           // The Cosmos client instance
           private CosmosClient cosmosClient;

           // The database we will create
           private CosmosDatabase database;

           // The container we will create.
           private CosmosContainer container;

           // The name of the database and container we will create
           private string databaseId = "FamilyDatabase";
           private string containerId = "FamilyContainer";

           public static async Task Main(string[] args)
           {
               try
               {
                   Console.WriteLine("Beginning operations...");
                   Program p = new Program();
                   await p.GetStartedDemoAsync();
               }
               catch (CosmosException de)
               {
                   Exception baseException = de.GetBaseException();
                   Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
               }
               catch (Exception e)
               {
                   Console.WriteLine("Error: {0}\n", e);
               }
               finally
               {
                   Console.WriteLine("End of demo, press any key to exit.");
                   Console.ReadKey();
               }
           }

           /*
               Entry point to call methods that operate on Azure Cosmos DB resources in this sample
           */
           public async Task GetStartedDemoAsync()
           {
               // Create a new instance of the Cosmos Client
               this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
               await this.CreateDatabase();
           }

           /*
               Create the database if it does not exist
           */
           private async Task CreateDatabase()
           {
               // Create a new database
               this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
               Console.WriteLine("Created Database: {0}\n", this.database.Id);
           }
       }
   }
   ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono bazę danych usługi Azure Cosmos DB.

## <a id="CreateColl"></a>Krok 5. Tworzenie kontenera

> [!WARNING]
> Wywołanie metody **CreateContainerIfNotExistsAsync** spowoduje utworzenie nowego kontenera, co ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

Kontener można utworzyć przy użyciu funkcji [**CreateContainerIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) lub [**CreateContainerAsync**](https://aka.ms/CosmosDotnetAPIDocs) klasy **CosmosContainers**. Kontener składa się z elementów (w przypadku interfejsu API SQL są to dokumenty JSON) i skojarzonej logiki aplikacji JavaScript po stronie serwera, np. procedur składowanych, funkcji zdefiniowanych przez użytkownika i wyzwalaczy.

1. Skopiuj metodę **CreateContainer** i wklej ją pod metodą **CreateDatabase**. Funkcja **CreateContainer** utworzy nowy kontener `FamilyContainer` o identyfikatorze podanym w polu `containerId` (jeśli jeszcze go nie ma).

   ```csharp
   /*
       Create the container if it does not exist.
       Specify "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
   */
   private async Task CreateContainer()
   {
       // Create a new container
       this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
       Console.WriteLine("Created Container: {0}\n", this.container.Id);
   }
   ```

1. Poniższy kod umożliwia wywołanie właśnie dodanej metody **CreateContainer**. Skopiuj go i wklej pod wierszem, w którym jest tworzone wystąpienie klasy CosmosClient.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();

       //ADD THIS PART TO YOUR CODE
       await this.CreateContainer();
   }
   ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono kontener usługi Azure Cosmos DB.

## <a id="CreateDoc"></a>Krok 6. Dodawanie elementów do kontenera

Element można utworzyć za pomocą funkcji [**CreateItemAsync**](https://aka.ms/CosmosDotnetAPIDocs) klasy **CosmosItems**. Podczas korzystania z interfejsu API SQL elementy są przekazywane jako dokumenty, które stanowią zdefiniowaną przez użytkownika (dowolną) zawartość JSON. Teraz można wstawić element do kontenera usługi Azure Cosmos DB.

Najpierw należy utworzyć klasę **Family**, która będzie reprezentowała obiekty przechowywane w usłudze Azure Cosmos DB w tym przykładzie. Zostaną również utworzone podklasy **Parent**, **Child**, **Pet** i **Address**, które są używane w ramach klasy **Family**. Należy pamiętać, że dokumenty muszą mieć właściwość **Id** serializowaną jako **id** w formacie JSON.

1.  Naciśnij klawisze **Ctrl+Shift+A**, aby otworzyć okno dialogowe **Dodaj nowy element**. Dodaj do projektu nową klasę **Family.cs**.

    ![Zrzut ekranu przedstawiający dodawanie nowej klasy Family.cs do projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1.  Skopiuj i wklej klasy **Family**, **Parent**, **Child**, **Pet** i **Address** do klasy **Family.cs**. Pamiętaj o tym, że przestrzeń nazw zależy od nazwy projektu.

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStartedDotnetCoreTutorial
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

1.  Wróć do pliku **Program.cs** i dodaj metodę **AddItemsToContainer** pod metodą **CreateContainer**.
    Przed utworzeniem elementu jest wykonywane sprawdzenie, czy istnieje element o takim samym identyfikatorze. Wstawimy dwa elementy, jeden dla rodziny Andersen i jeden dla rodziny Wakefield.

    ```csharp
     /*
         Add Family items to the container
     */
     private async Task AddItemsToContainer()
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
             IsRegistered = true
         };

         // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object.
         CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

         if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
             andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
         }

         // Create a family object for the Wakefield family
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

         // Read the item to see if it exists
         CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

         if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
             wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
         }
     }
    
    ```

1.  Dodaj wywołanie metody `AddItemsToContainer` w metodzie `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase();
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono dwa elementy usługi Azure Cosmos DB.

## <a id="Query"></a>Krok 7. Wykonywanie zapytania względem zasobów usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji. W następującym przykładowym kodzie pokazano, jak uruchomić zapytanie względem elementów wstawionych w poprzednim kroku.

1. Skopiuj metodę **RunQuery** i wklej ją pod metodą **AddItemsToContainer**.

   ```csharp
   /*
       Run a query (using Azure Cosmos DB SQL syntax) against the container
   */
   private async Task RunQuery()
   {
       var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
       var partitionKeyValue = "Andersen";

       Console.WriteLine("Running query: {0}\n", sqlQueryText);

       CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
       CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

       List<Family> families = new List<Family>();

       while (queryResultSetIterator.HasMoreResults)
       {
           CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
           foreach (Family family in currentResultSet)
           {
               families.Add(family);
               Console.WriteLine("\tRead {0}\n", family);
           }
       }
   }
   ```

1. Dodaj wywołanie metody `RunQuery` w metodzie `GetStartedDemoAsync`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();

       //ADD THIS PART TO YOUR CODE
       await this.RunQuery();
   }
   ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie wykonano zapytanie względem kontenera usługi Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 8. Zastępowanie elementu JSON

Teraz zaktualizujemy element w usłudze Azure Cosmos DB.

1. Skopiuj metodę **ReplaceFamilyItem** i wklej ją pod metodą **RunQuery**. Zwróć uwagę, że zmieniamy właściwość `IsRegistered` rodziny i właściwość `Grade` jednego dziecka.

   ```csharp
   /*
   Update an item in the container
   */
   private async Task ReplaceFamilyItem()
   {
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
       var itemBody = wakefieldFamilyResponse.Resource;

       // update registration status from false to true
       itemBody.IsRegistered = true;
       // update grade of child
       itemBody.Children[0].Grade = 6;

       // replace the item with the updated content
       wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
       Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
   }
   ```

1. Dodaj wywołanie metody `ReplaceFamilyItem` w metodzie `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();

       //ADD THIS PART TO YOUR CODE
       await this.ReplaceFamilyItem();
   }
   ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie zastąpiono element usługi Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9. Usuwanie elementu

Teraz usuniemy element w usłudze Azure Cosmos DB.

1. Skopiuj i wklej metodę **DeleteFamilyItem** poniżej metody **ReplaceFamilyItem**.

   ```csharp
   /*
   Delete an item in the container
   */
   private async Task DeleteFamilyItem()
   {
       var partitionKeyValue = "Wakefield";
       var familyId = "Wakefield.7";

       // Delete an item. Note we must provide the partition key value and id of the item to delete
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
       Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
   }
   ```

1. Dodaj wywołanie metody `DeleteFamilyItem` w metodzie `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteFamilyItem();
   }
   ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto element usługi Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych

Teraz usuniemy naszą bazę danych. Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kontenerów, elementów, procedur składowanych, funkcji zdefiniowanych przez użytkownika i wyzwalaczy). Usuniemy także wystąpienie klasy **CosmosClient**.

1. Skopiuj metodę **DeleteDatabaseAndCleanup** i wklej ją pod metodą **DeleteFamilyItem**.

   ```csharp
   /*
   Delete the database and dispose of the Cosmos Client instance
   */
   private async Task DeleteDatabaseAndCleanup()
   {
       CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
       // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

       Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

       //Dispose of CosmosClient
       this.cosmosClient.Dispose();
   }
   ```

1. Dodaj wywołanie metody `DeleteDatabaseAndCleanup` w metodzie `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();
       await this.DeleteFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteDatabaseAndCleanup();
   }
   ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto bazę danych usługi Azure Cosmos DB.

## <a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej C#

Naciśnij klawisz **F5** w programie Visual Studio, aby skompilować aplikację w trybie debugowania.

Wszystkie dane wyjściowe aplikacji rozpoczynania pracy powinny zostać wyświetlone w oknie konsoli. Dane wyjściowe będą pokazywały wyniki dodanych zapytań i powinny odpowiadać poniższemu przykładowemu tekstowi.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gratulacje! Pomyślnie ukończono ten samouczek i utworzono działającą aplikację konsolową C#.

## <a id="GetSolution"></a> Pobieranie kompletnego rozwiązania samouczka

Do utworzenia rozwiązania GetStarted, które zawiera wszystkie przykłady w tym artykule, będą potrzebne następujące elementy:

- Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
- [Konto usługi Azure Cosmos DB][cosmos-db-create-account].
- Rozwiązanie [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started) dostępne w witrynie GitHub.

Aby przywrócić odwołania do interfejsu API SQL zestawu SDK dla platformy .NET Core usługi Azure Cosmos DB w programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie **GetStarted** w Eksploratorze rozwiązań, a następnie wybierz polecenie **Przywróć pakiety NuGet**. Następnie w pliku **Program.cs** zaktualizuj wartości `EndpointUri` i `PrimaryKey` zgodnie z opisem w kroku [Łączenie się z kontem usługi Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje na temat tworzenia aplikacji .NET Core umożliwiającej zarządzanie danymi interfejsu API SQL usługi Azure Cosmos DB. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
