---
title: Tworzenie aplikacji konsolowej .NET do zarządzania danymi na koncie usługi Azure Cosmos DB — interfejs API SQL
description: Dowiedz się, jak tworzyć zasoby usługi Azure Cosmos DB — interfejs API SQL za pomocą C# aplikacji konsoli.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: kirankk
ms.openlocfilehash: 6fd7efe38aeb1f1094d240cf1675d432f3766229
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985725"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tworzenie aplikacji konsolowej .NET do zarządzania danymi na koncie usługi Azure Cosmos DB — interfejs API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Samouczek wprowadzający get interfejsu API SQL usługi Azure Cosmos DB — Zapraszamy!. W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania. W tym samouczku [w wersji 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) z usługi Azure Cosmos DB .NET SDK, które mogą być kierowane do [.NET Framework](https://dotnet.microsoft.com/download) lub [platformy .NET Core](https://dotnet.microsoft.com/download).

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
    > Dla platformy .NET core obiektu docelowego, w **nowy projekt** okno dialogowe, wybierz opcję **Visual C#**   /  **Aplikacja konsoli (.NET Core)** , nazwę projektu, a następnie kliknij przycisk  **Ok**

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w ramach rozwiązania Visual Studio, a następnie kliknij pozycję **Zarządzaj pakietami NuGet...**

    ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Na karcie **NuGet** kliknij pozycję **Przeglądaj** i wpisz ciąg **Microsoft.Azure.Cosmos** w polu wyszukiwania.
1. W wynikach znajdź pozycję **Microsoft.Azure.Cosmos**, a następnie kliknij przycisk **Zainstaluj**.
   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Zrzut ekranu przedstawiający NuGet Menu służące do znajdowania zestawu SDK klienta usługi Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

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

   ![Zrzut ekranu, można pobrać kluczy usługi Azure Cosmos DB w witrynie Azure portal](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

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

1. Skopiuj i Wklej **CreateDatabaseAsync** metoda poniżej swoje **GetStartedDemoAsync** metody. **CreateDatabaseAsync** utworzy nową bazę danych o identyfikatorze ``FamilyDatabase`` Jeśli go jeszcze nie istnieje, z Identyfikatorem określony z ``databaseId`` pola. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Skopiuj i Wklej kod poniżej, gdzie tworzone CosmosClient do wywołania **CreateDatabaseAsync** metoda właśnie został dodany.

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

Kontener można utworzyć przy użyciu [ **CreateContainerIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) lub [ **CreateContainerAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) działa w programach **CosmosDatabase** klasy. Kontener, który składa się z elementów (dokumenty JSON, jeśli interfejs API SQL) i skojarzonej logiki aplikacji po stronie serwera w języku JavaScript, na przykład, procedury składowane, funkcje zdefiniowane przez użytkownika i wyzwalacze.

1. Skopiuj i Wklej **CreateContainerAsync** metoda poniżej swoje **CreateDatabaseAsync** metody. **CreateContainerAsync** utworzy nowy kontener o identyfikatorze ``FamilyContainer`` Jeśli go jeszcze nie istnieje, z Identyfikatorem określony z ``containerId`` pola są dzielone według ``LastName`` właściwości.

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
Można utworzyć elementu za pomocą [ **CreateItemAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) funkcji **CosmosContainer** klasy. Podczas korzystania z interfejsu API SQL elementy są przekazywane jako dokumenty, które stanowią zdefiniowaną przez użytkownika (dowolną) zawartość JSON. Teraz można wstawić element do kontenera usługi Azure Cosmos DB.

Po pierwsze Utwórzmy **rodziny** klasy, która będzie reprezentowała obiekty przechowywane w usłudze Azure Cosmos DB, w tym przykładzie. Zostaną również utworzone podklasy **Parent**, **Child**, **Pet** i **Address**, które są używane w ramach klasy **Family**. Należy pamiętać, element musi mieć **identyfikator** właściwość jest serializowana jako **identyfikator** w formacie JSON.

1. Naciśnij klawisze **Ctrl+Shift+A**, aby otworzyć okno dialogowe **Dodaj nowy element**. Dodaj do projektu nową klasę **Family.cs**.

    ![Zrzut ekranu przedstawiający dodawanie nowej klasy Family.cs do projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Skopiuj i wklej klasy **Family**, **Parent**, **Child**, **Pet** i **Address** do klasy **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Przejdź z powrotem do **Program.cs** i Dodaj **AddItemsToContainerAsync** testowana metoda swoje **CreateContainerAsync** metody.
Kod sprawdza, upewnij się, że element o takim samym identyfikatorze jeszcze nie istnieje przed jego utworzeniem. Wstawimy dwa elementy, jeden dla rodziny Andersen i jeden dla rodziny Wakefield.

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

1. Skopiuj i Wklej **QueryItemsAsync** metoda poniżej swoje **AddItemsToContainerAsync** metody.

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

1. Skopiuj i Wklej **ReplaceFamilyItemAsync** metoda poniżej swoje **QueryItemsAsync** metody. Zwróć uwagę, że zmieniamy właściwość ``IsRegistered`` rodziny i właściwość ``Grade`` jednego dziecka.

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

1. Skopiuj i Wklej **DeleteFamilyItemAsync** metoda poniżej swoje **ReplaceFamilyItemAsync** metody.

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

1. Skopiuj i Wklej **DeleteDatabaseAndCleanupAsync** metoda poniżej swoje **DeleteFamilyItemAsync** metody.

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
* [Konta usługi Azure Cosmos DB][cosmos-db-create-account].
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
