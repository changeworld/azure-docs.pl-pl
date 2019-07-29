---
title: Tworzenie aplikacji konsolowej .NET do zarządzania danymi w Azure Cosmos DB koncie interfejsu API SQL
description: Dowiedz się, jak tworzyć Azure Cosmos DB zasoby interfejsu API C# SQL przy użyciu aplikacji konsolowej.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 37b6a9947d3cabe1f566f842e321229efe9d03b6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598518"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tworzenie aplikacji konsolowej .NET do zarządzania danymi w Azure Cosmos DB koncie interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Witamy w samouczku wprowadzenie do interfejsu API SQL Azure Cosmos DB. W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania. W tym samouczku jest używana [wersja 3.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) programu Azure Cosmos DB .NET SDK, która może być przeznaczona dla [.NET Framework](https://dotnet.microsoft.com/download) lub [.NET Core](https://dotnet.microsoft.com/download).

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos i łączenie się z nim
> * Konfigurowanie projektu w programie Visual Studio
> * Tworzenie bazy danych i kontenera
> * Dodawanie elementów do kontenera
> * Wykonywanie zapytań względem kontenera
> * Wykonywanie operacji CRUD na elemencie
> * Usuwanie bazy danych

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Przeskocz do sekcji [Pobieranie kompletnego rozwiązania samouczka](#GetSolution), aby uzyskać krótkie instrukcje.

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie rozwiązania Visual Studio](#SetupVS). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie projektu programu Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Krok 2. Konfigurowanie projektu programu Visual Studio
1. Otwórz program **Visual Studio 2017** na komputerze.
1. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
1. W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C#**  / **Aplikacja konsolowa (.NET Framework)** , podaj nazwę projektu, a następnie kliknij przycisk **OK**.
    ![Zrzut ekranu przedstawiający okno nowy projekt](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > W przypadku programu .NET Core Target w oknie dialogowym **Nowy projekt** wybierz **pozycję C#**   /  **Aplikacja konsolowa (.NET Core)** , nazwij projekt, a następnie kliknij przycisk **OK** .

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w ramach rozwiązania Visual Studio, a następnie kliknij pozycję **Zarządzaj pakietami NuGet...**

    ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Na karcie **NuGet** kliknij pozycję **Przeglądaj** i wpisz ciąg **Microsoft.Azure.Cosmos** w polu wyszukiwania.
1. W wynikach znajdź pozycję **Microsoft.Azure.Cosmos**, a następnie kliknij przycisk **Zainstaluj**.
   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Zrzut ekranu przedstawiający menu NuGet służące do znajdowania zestawu SDK klienta Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Jeśli zostanie wyświetlony komunikat dotyczący przejrzenia zmian wprowadzonych w rozwiązaniu, kliknij przycisk **OK**. Jeśli wyświetlany jest komunikat o akceptacji licencji, kliknij pozycję **Akceptuję**.

Wspaniale! Teraz, po zakończeniu konfigurowania, zacznijmy pisanie kodu. Ukończony projekt kodu z tego samouczka można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB
1. Najpierw zastąp odwołania na początku aplikacji C# w pliku **Program.cs** następującymi odwołaniami:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Następnie dodaj te stałe i zmienne do klasy publicznej ``Program``.

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
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    Osoby znające poprzednią wersję zestawu .NET SDK mogą być przyzwyczajone do terminów „kolekcja” i „dokument”. Ponieważ usługa Azure Cosmos DB obsługuje wiele modeli interfejsu API, w wersji 3.0+ zestawu .NET SDK używane są ogólne terminy „kontener” i „element”. Kontener może być kolekcją, wykresem lub tabelą. Element może być dokumentem, krawędzią/wierzchołkiem lub wierszem i stanowi zawartość znajdująca się w kontenerze. [Dowiedz się więcej na temat baz danych, kontenerów i elementów.](databases-containers-items.md)

1. Pobierz adres URL punktu końcowego i klucz podstawowy z witryny [Azure Portal](https://portal.azure.com).

    W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos DB i kliknij pozycję **Klucze**.

    Skopiuj identyfikator URI z portalu i wklej go w miejsce `<your endpoint URL>` w pliku ```Program.cs```. Skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `<your primary key>`.

   ![Zrzut ekranu przedstawiający klucze Azure Cosmos DB z Azure Portal](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Następnie utworzymy nowe wystąpienie klasy ```CosmosClient``` i skonfigurujemy niektóre elementy szkieletu naszego programu.

    Poniżej metody **Main** dodaj nowe zadanie asynchroniczne o nazwie **GetStartedDemoAsync**, które utworzy nowe wystąpienie klasy ```CosmosClient```. Zadanie **GetStartedDemoAsync** służy jako punkt wejścia wywołujący metody, które działają na zasobach usługi Azure Cosmos DB.

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

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Naciśnij klawisz **F5**, aby uruchomić aplikację. W danych wyjściowych okna konsoli wyświetlany jest komunikat `End of demo, press any key to exit.` potwierdzający nawiązanie połączenia z usługą Azure Cosmos DB. Następnie można zamknąć okno konsoli.

Gratulacje! Pomyślnie nawiązano połączenie z kontem usługi Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych
Bazę danych można utworzyć przy użyciu funkcji [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) lub [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) klasy ``CosmosClient``. Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach.

1. Skopiuj i wklej metodę **CreateDatabaseAsync** poniżej metody **GetStartedDemoAsync** . **CreateDatabaseAsync** utworzy nową bazę danych o identyfikatorze ``FamilyDatabase`` , jeśli jeszcze nie istnieje, z ``databaseId`` identyfikatorem określonym w polu. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Skopiuj i wklej kod poniżej miejsca, w którym utworzono wystąpienie CosmosClient, aby wywołać metodę **CreateDatabaseAsync** , która właśnie została dodana.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Na tym etapie kod powinien wyglądać mniej więcej tak i zawierać dane dotyczące punktu końcowego i klucza podstawowego.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
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

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
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
>
>

Kontener można utworzyć za pomocą funkcji [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) lub [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) w klasie **CosmosDatabase** . Kontener składa się z elementów (dokumentów JSON, jeśli interfejs API SQL) i skojarzonej logiki aplikacji po stronie serwera w języku JavaScript, na przykład procedury składowane, funkcje zdefiniowane przez użytkownika i wyzwalacze.

1. Skopiuj i wklej metodę **CreateContainerAsync** poniżej metody **CreateDatabaseAsync** . **CreateContainerAsync** utworzy nowy kontener o identyfikatorze ``FamilyContainer`` , jeśli jeszcze nie istnieje, z ``containerId`` identyfikatorem określonym w polu partycjonowanym przez ``LastName`` właściwość.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Poniższy kod umożliwia wywołanie właśnie dodanej metody **CreateContainer**. Skopiuj go i wklej pod wierszem, w którym jest tworzone wystąpienie klasy CosmosClient.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono kontener usługi Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6. Dodawanie elementów do kontenera
Element można utworzyć za pomocą funkcji [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) klasy **CosmosContainer** . Podczas korzystania z interfejsu API SQL elementy są przekazywane jako dokumenty, które stanowią zdefiniowaną przez użytkownika (dowolną) zawartość JSON. Teraz można wstawić element do kontenera usługi Azure Cosmos DB.

Najpierw Utwórzmy klasę **rodziny** , która będzie reprezentować obiekty przechowywane w Azure Cosmos DB w tym przykładzie. Zostaną również utworzone podklasy **Parent**, **Child**, **Pet** i **Address**, które są używane w ramach klasy **Family**. Element Note musi mieć serializowaną Właściwość **ID** jako **Identyfikator** w formacie JSON.

1. Naciśnij klawisze **Ctrl+Shift+A**, aby otworzyć okno dialogowe **Dodaj nowy element**. Dodaj do projektu nową klasę **Family.cs**.

    ![Zrzut ekranu przedstawiający Dodawanie nowej klasy Family.cs do projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Skopiuj i wklej klasy **Family**, **Parent**, **Child**, **Pet** i **Address** do klasy **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Wróć do **program.cs** i Dodaj metodę **AddItemsToContainerAsync** w ramach metody **CreateContainerAsync** .
Kod sprawdza, czy element o takim samym IDENTYFIKATORze jeszcze nie istnieje przed jego utworzeniem. Wstawimy dwa elementy, jeden dla rodziny Andersen i jeden dla rodziny Wakefield.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Dodaj wywołanie metody ``AddItemsToContainerAsync`` w metodzie ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono dwa elementy usługi Azure Cosmos DB.  

## <a id="Query"></a>Krok 7. Wykonywanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji. W następującym przykładowym kodzie pokazano, jak uruchomić zapytanie względem elementów wstawionych w poprzednim kroku.

1. Skopiuj i wklej metodę **QueryItemsAsync** poniżej metody **AddItemsToContainerAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Dodaj wywołanie metody ``QueryItemsAsync`` w metodzie ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie wykonano zapytanie względem kontenera usługi Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 8. Zastępowanie elementu JSON
Teraz zaktualizujemy element w usłudze Azure Cosmos DB.

1. Skopiuj i wklej metodę **ReplaceFamilyItemAsync** poniżej metody **QueryItemsAsync** . Zwróć uwagę, że zmieniamy właściwość ``IsRegistered`` rodziny i właściwość ``Grade`` jednego dziecka.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Dodaj wywołanie metody ``ReplaceFamilyItemAsync`` w metodzie ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie zastąpiono element usługi Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9. Usuwanie elementu
Teraz usuniemy element w usłudze Azure Cosmos DB.

1. Skopiuj i wklej metodę **DeleteFamilyItemAsync** poniżej metody **ReplaceFamilyItemAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Dodaj wywołanie metody ``DeleteFamilyItemAsync`` w metodzie ``GetStartedDemoAsync``.

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

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto element usługi Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych
Teraz usuniemy naszą bazę danych. Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych (kontenerów, elementów, procedur składowanych, funkcji zdefiniowanych przez użytkownika i wyzwalaczy). Usuniemy także wystąpienie klasy **CosmosClient**.

1. Skopiuj i wklej metodę **DeleteDatabaseAndCleanupAsync** poniżej metody **DeleteFamilyItemAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Dodaj wywołanie metody ``DeleteDatabaseAndCleanupAsync`` w metodzie ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Naciśnij klawisz **F5**, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto bazę danych usługi Azure Cosmos DB.

## <a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej C#
Naciśnij klawisz F5 w programie Visual Studio, aby skompilować i uruchomić aplikację w trybie debugowania.

Wszystkie dane wyjściowe aplikacji powinny zostać wyświetlone w oknie konsoli. Dane wyjściowe będą pokazywały wyniki dodanych zapytań i powinny odpowiadać poniższemu przykładowemu tekstowi.

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
Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać przykłady kodu, możesz uzyskać je w serwisie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Aby skompilować rozwiązanie GetStarted, niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* [Konto Azure Cosmos DB][cosmos-db-create-account].
* Rozwiązanie [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostępne w witrynie GitHub.

Aby przywrócić odwołania do zestawu SDK dla platformy .NET usługi Azure Cosmos DB w programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie **GetStarted** w Eksploratorze rozwiązań, a następnie kliknij polecenie **Przywróć pakiety Nuget**. Następnie w pliku App.config zaktualizuj wartości EndPointUri i PrimaryKey zgodnie z opisem w kroku [Łączenie się z kontem usługi Azure Cosmos DB](#Connect).

To wszystko — skompiluj projekt i gotowe!

## <a name="next-steps"></a>Następne kroki
* Potrzebujesz bardziej złożonego samouczka platformy ASP.NET MVC? Zobacz [Samouczek platformy ASP.NET MVC: Opracowywanie aplikacji internetowych za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Czy chcesz wykonać testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB? Zobacz [Performance and scale testing with Azure Cosmos DB](performance-testing.md) (Testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB)
* Dowiedz się, jak [monitorować żądania, użycie i magazyn usługi Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).
* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](https://docs.microsoft.com/azure/cosmos-db/introduction)

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
