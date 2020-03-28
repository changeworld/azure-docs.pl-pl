---
title: 'Samouczek: tworzenie aplikacji konsoli platformy .NET do zarządzania danymi na koncie interfejsu API SQL usługi Azure Cosmos DB'
description: 'Samouczek: Dowiedz się, jak utworzyć zasoby interfejsu API SQL usługi Azure Cosmos DB przy użyciu aplikacji konsoli języka C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: 2681b2199f321f695bc621ed5580319a5e907b34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274029"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Samouczek: tworzenie aplikacji konsoli platformy .NET do zarządzania danymi na koncie interfejsu API SQL usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Zapraszamy do samouczka wprowadzenie interfejsu API usługi Azure Cosmos DB. W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

W tym samouczku użyto wersji 3.0 lub nowszej zestawu [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Można pracować z [programem .NET Framework lub .NET Core](https://dotnet.microsoft.com/download).

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie konta usługi Azure Cosmos i łączenie się z nim
> * Konfigurowanie projektu w programie Visual Studio
> * Tworzenie bazy danych i kontenera
> * Dodawanie elementów do kontenera
> * Wykonywanie zapytań względem kontenera
> * Wykonywanie operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) w elemencie
> * Usuwanie bazy danych

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Przeskocz do sekcji [Pobieranie kompletnego rozwiązania samouczka](#GetSolution), aby uzyskać krótkie instrukcje.

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB

Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, pomiń tę sekcję. Aby użyć emulatora usługi Azure Cosmos DB, wykonaj kroki w [emulatorze usługi Azure Cosmos DB,](local-emulator.md) aby skonfigurować emulator. Następnie przejdź do [kroku 2: Skonfiguruj projekt programu Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Krok 2: Konfigurowanie projektu programu Visual Studio

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C#, a następnie wybierz pozycję **Dalej**.
1. Nazwij swój projekt *CosmosGettingStartedTutorial*, a następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie projektu](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy nową aplikację konsoli, która znajduje się w obszarze rozwiązania programu Visual Studio, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj** i wyszukaj pozycję *Microsoft.Azure.Cosmos*. Wybierz pozycję **Microsoft.Azure.Cosmos** i wybierz pozycję **Zainstaluj**.

   ![Instalowanie pakietu SDK klienta usługi NuGet for Azure Cosmos DB](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Wspaniale! Teraz, po zakończeniu konfigurowania, zacznijmy pisanie kodu. Aby zapoznać się z ukończonym projektem tego samouczka, zobacz [Tworzenie aplikacji konsoli .NET przy użyciu usługi Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB

1. Zastąp odwołania na początku aplikacji Języka C# w pliku *Program.cs* tymi odwołaniami:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Dodaj te stałe i zmienne do swojej `Program` klasy.

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

   > [!NOTE]
   > Jeśli znasz poprzednią wersję sdk .NET, możesz znać *kolekcję* terminów i *dokument*. Ponieważ usługa Azure Cosmos DB obsługuje wiele modeli interfejsu API, wersja 3.0 sdk .NET używa *ogólnego kontenera* i *elementu*terminów ogólnych . *Kontener* może być kolekcją, wykresem lub tabelą. *Element* może być dokumentem, krawędzią/wierzchołkiem lub wierszem i jest zawartością wewnątrz kontenera. Aby uzyskać więcej informacji, zobacz [Praca z bazami danych, kontenerami i elementami w usłudze Azure Cosmos DB](databases-containers-items.md).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Znajdź swoje konto usługi Azure Cosmos DB, a następnie wybierz pozycję **Klucze**.

   ![Pobierz klucze usługi Azure Cosmos DB z witryny Azure portal](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. W *Program.cs*, `<your endpoint URL>` zastąp wartością **URI**. Zamień `<your primary key>` na wartość **KLUCZA PODSTAWOWEGO**.

1. Poniżej **Main** metody, dodać nowe zadanie asynchroniczne o nazwie **GetStartedDemoAsync**, który smusza nasze nowe `CosmosClient`.

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

    Używamy **GetStartedDemoAsync** jako punktu wejścia, który wywołuje metody, które działają na zasoby usługi Azure Cosmos DB.

1. Dodaj następujący kod w celu uruchomienia zadania asynchronicznego **GetStartedDemoAsync** w metodzie **Main**. Metoda **Main** przechwytuje wyjątki i wyświetla je w konsoli.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Wybierz F5, aby uruchomić aplikację.

    Konsola wyświetla komunikat: **Koniec wersji demonstracyjnej, naciśnij dowolny klawisz, aby wyjść.** Ten komunikat potwierdza, że aplikacja nawiązała połączenie z usługą Azure Cosmos DB. Następnie można zamknąć okno konsoli.

Gratulacje! Pomyślnie nawiązano połączenie z kontem usługi Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych

Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach. Lub `CreateDatabaseIfNotExistsAsync` `CreateDatabaseAsync` metody [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) klasy można utworzyć bazę danych.

1. Skopiuj `CreateDatabaseAsync` i `GetStartedDemoAsync` wklej metodę poniżej.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`tworzy nową bazę `FamilyDatabase` danych o identyfikatorze, jeśli jeszcze nie istnieje, `databaseId` która ma identyfikator określony z pola.

1. Skopiuj i wklej poniższy kod, w którym wystąpienia CosmosClient wywołać **CreateDatabaseAsync** metoda właśnie dodane.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Twój *Program.cs* powinien teraz wyglądać następująco, z punktem końcowym i kluczem podstawowym.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
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

1. Wybierz F5, aby uruchomić aplikację.

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd "Wyjątek niedostępny dla usługi 503", możliwe, że wymagane [porty](performance-tips.md#networking) dla trybu łączności bezpośredniej są blokowane przez zaporę. Aby rozwiązać ten problem, otwórz wymagane porty lub użyj łączności w trybie bramy, jak pokazano w poniższym kodzie:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Gratulacje! Pomyślnie utworzono bazę danych usługi Azure Cosmos.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Krok 5. Tworzenie kontenera

> [!WARNING]
> Metoda `CreateContainerIfNotExistsAsync` tworzy nowy kontener, który ma wpływ na ceny. Aby uzyskać więcej informacji, odwiedź naszą [stronę z cenami.](https://azure.microsoft.com/pricing/details/cosmos-db/)
>
>

Kontener można utworzyć przy użyciu [**metody CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) lub [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) w `CosmosDatabase` klasie. Kontener składa się z elementów (dokumentów JSON, jeśli SQL API) i skojarzonej logiki aplikacji po stronie serwera w języku JavaScript, na przykład procedur przechowywanych, funkcji zdefiniowanych przez użytkownika i wyzwalaczy.

1. Skopiuj `CreateContainerAsync` i `CreateDatabaseAsync` wklej metodę poniżej. `CreateContainerAsync`tworzy nowy kontener o `FamilyContainer` identyfikatorze, jeśli jeszcze nie istnieje, przy użyciu `containerId` identyfikatora `LastName` określonego z pola podzielonego na partycje przez właściwość.

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

1. Wybierz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono kontener usługi Azure Cosmos.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Krok 6: Dodawanie elementów do kontenera

Metoda [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) `CosmosContainer` klasy może utworzyć element. Podczas korzystania z interfejsu API SQL elementy są rzutowane jako dokumenty, które są zdefiniowane przez użytkownika dowolnej zawartości JSON. Teraz można wstawić element do kontenera usługi Azure Cosmos.

Najpierw utwórzmy `Family` klasę, która reprezentuje obiekty przechowywane w usłudze Azure Cosmos DB w tym przykładzie. `Parent`Utworzymy również `Child`podklasy `Pet`, , `Address` `Family`które są używane w obrębie . Element musi mieć `Id` właściwość `id` serializowany jak w JSON.

1. Wybierz klawisze Ctrl+Shift+A, aby otworzyć **otwieranie opcji Dodaj nowy element**. Dodaj nową `Family.cs` klasę do projektu.

    ![Zrzut ekranu przedstawiający dodawanie nowej klasy Family.cs do projektu](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Skopiuj `Family` `Parent`i `Child` `Pet`wklej `Address` , `Family.cs`, i klasy do .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Powrót *do Program.cs*, dodaj `AddItemsToContainerAsync` metodę `CreateContainerAsync` po metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Kod sprawdza, czy element o tym samym identyfikatorze jeszcze nie istnieje. Wstawimy dwa elementy, po jednym dla *rodziny Andersen* i *rodziny Wakefield.*

1. Dodaj wywołanie metody `AddItemsToContainerAsync` w metodzie `GetStartedDemoAsync`.

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

1. Wybierz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono dwa elementy usługi Azure Cosmos.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje zaawansowane zapytania względem dokumentów JSON przechowywanych w poszczególnych kontenerach. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do zapytań SQL](sql-api-sql-query.md). W następującym przykładowym kodzie pokazano, jak uruchomić zapytanie względem elementów wstawionych w poprzednim kroku.

1. Skopiuj `QueryItemsAsync` i `AddItemsToContainerAsync` wklej metodę po swojej metodzie.

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

1. Wybierz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie zapytano kontener usługi Azure Cosmos.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Krok 8. Zastępowanie elementu JSON

Teraz zaktualizujemy element w usłudze Azure Cosmos DB. Zmienimy `IsRegistered` własność `Family` i jednego `Grade` z dzieci.

1. Skopiuj `ReplaceFamilyItemAsync` i `QueryItemsAsync` wklej metodę po swojej metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Dodaj wywołanie metody `ReplaceFamilyItemAsync` w metodzie `GetStartedDemoAsync`.

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

1. Wybierz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie zastąpiono element usługi Azure Cosmos.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Krok 9: Usuń element

Teraz usuniemy element w usłudze Azure Cosmos DB.

1. Skopiuj `DeleteFamilyItemAsync` i `ReplaceFamilyItemAsync` wklej metodę po swojej metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Dodaj wywołanie metody `DeleteFamilyItemAsync` w metodzie `GetStartedDemoAsync`.

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

1. Wybierz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto element usługi Azure Cosmos.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych

Teraz usuniemy naszą bazę danych. Usunięcie utworzonej bazy danych powoduje usunięcie bazy danych i wszystkich zasobów podrzędnych. Zasoby obejmują kontenery, elementy i wszelkie procedury przechowywane, funkcje zdefiniowane przez użytkownika i wyzwalacze. Usuwamy również `CosmosClient` wystąpienie.

1. Skopiuj `DeleteDatabaseAndCleanupAsync` i `DeleteFamilyItemAsync` wklej metodę po swojej metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Dodaj wywołanie metody ``DeleteDatabaseAndCleanupAsync`` w metodzie ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Wybierz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto bazę danych usługi Azure Cosmos.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej C#

Naciśnij klawisz F5 w programie Visual Studio, aby skompilować i uruchomić aplikację w trybie debugowania.

Wszystkie dane wyjściowe aplikacji powinny zostać wyświetlone w oknie konsoli. Dane wyjściowe pokazują wyniki zapytań, które dodaliśmy. Powinien on pasować do przykładowego tekstu poniżej.

```cmd
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

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> Pobieranie kompletnego rozwiązania samouczka

Jeśli nie masz czasu, aby wykonać kroki w tym samouczku lub po prostu chcesz pobrać przykłady kodu, można go pobrać.

Aby utworzyć `GetStarted` rozwiązanie, potrzebujesz następujących wymagań wstępnych:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* [Konto usługi Azure Cosmos DB][cosmos-db-create-account].
* Rozwiązanie [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostępne w witrynie GitHub.

Aby przywrócić odwołania do sdk usługi Azure Cosmos DB .NET w programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie w **Eksploratorze rozwiązań,** a następnie wybierz polecenie **Przywróć pakiety NuGet**. Następnie w pliku *App.config* zaktualizuj `EndPointUri` wartości opisane `PrimaryKey` w kroku [3: Połącz się z kontem usługi Azure Cosmos DB](#Connect).

To wszystko — skompiluj projekt i gotowe!

## <a name="next-steps"></a>Następne kroki

* Potrzebujesz bardziej złożonego samouczka platformy ASP.NET MVC? Zobacz [samouczek: Opracowanie ASP.NET podstawowej aplikacji sieci web MVC za pomocą usługi Azure Cosmos DB przy użyciu zestawu .NET SDK](sql-api-dotnet-application.md).
* Chcesz wykonać testowanie skali i wydajności za pomocą usługi Azure Cosmos DB? Zobacz [Testowanie wydajności i skalowania za pomocą usługi Azure Cosmos DB.](performance-testing.md)
* Aby dowiedzieć się, jak monitorować żądania, użycie i magazyn usługi Azure Cosmos DB, zobacz [Monitorowanie wydajności i metryk magazynu w usłudze Azure Cosmos DB.](monitor-accounts.md)
* Aby uruchomić kwerendy dotyczące naszego przykładowego zestawu danych, zobacz [query playground](https://www.documentdb.com/sql/demo).
* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](https://docs.microsoft.com/azure/cosmos-db/introduction)

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
