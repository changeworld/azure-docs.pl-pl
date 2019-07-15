---
title: 'ASP.NET Core MVC samouczek usługi Azure Cosmos DB: opracowywanie aplikacji internetowych'
description: ASP.NET Core MVC samouczek tworzenia aplikacji sieci web MVC za pomocą usługi Azure Cosmos DB. Będziesz kodu JSON i uzyskać dostęp do danych z aplikacji todo hostowanych w usłudze Azure App Service — ASP NET Core MVC, samouczek krok po kroku.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985885"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Samouczek: Tworzenie aplikacji sieci web programu ASP.NET Core MVC za pomocą usługi Azure Cosmos DB przy użyciu zestawu SDK platformy .NET 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji ASP.NET MVC hostowanej na platformie Azure. W tym samouczku używasz zestawu SDK platformy .NET w wersji 3. Na poniższej ilustracji przedstawiono stronę internetową, którą utworzymy, korzystając z przykładu opisanego w tym artykule:
 
![Zrzut ekranu przedstawiający listę zadań do wykonania utworzone w ramach tego samouczka - ASP NET Core MVC, samouczek krok po kroku, aplikacja sieci web MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Jeśli nie masz czas do ukończenia tego samouczka, możesz pobrać kompletnego przykładowego projektu z [GitHub][GitHub].

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Tworzenie aplikacji MVC platformy ASP.NET Core
> * Łączenie aplikacji z usługą Azure Cosmos DB 
> * Wykonywanie operacji CRUD na danych

> [!TIP]
> Ten samouczek zakłada, że masz wcześniejszego doświadczenia w używaniu platformy ASP.NET Core MVC i usługi Azure App Service. Jeśli dopiero zaczynasz korzystać z platformy ASP.NET Core lub [wstępnie wymaganych narzędzi](#prerequisites), firma Microsoft zaleca pobieranie kompletnego przykładowego projektu z [GitHub][GitHub], Dodaj wymagane pakiety NuGet i uruchom go. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.

## <a name="prerequisites"></a>Wymagania wstępne 

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* **Aktywne konto platformy Azure:** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Wszystkie zrzuty ekranu w tym artykule wykonano za pomocą programu Microsoft Visual Studio Community 2019. Jeśli w Twoim systemie jest skonfigurowana inna wersja, być może Twoje ekrany i opcje nie będą całkiem zgodne, lecz jeśli spełniasz powyższe wymagania wstępne, to rozwiązanie powinno działać.

## <a name="create-an-azure-cosmos-account"></a>Krok 1: tworzenie konta usługi Azure Cosmos

Zacznijmy od utworzenia konta usługi Azure Cosmos. Jeśli masz już konto interfejsu API SQL usługi Azure Cosmos DB lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Tworzenie nowej aplikacji platformy ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

W następnej sekcji utworzysz nową aplikację ASP.NET Core MVC. 

## <a name="create-a-new-mvc-application"></a>Krok 2. Utwórz nową aplikację ASP.NET Core MVC

1. W programie Visual Studio z menu **Plik** wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. **Nowy projekt** pojawi się okno dialogowe.

2. W **nowy projekt** oknie, użyj **wyszukiwanie szablonów** pole wejściowe, aby wyszukać "Web", a następnie wybierz **aplikacji sieci Web programu ASP.NET Core**. 

   ![Utwórz nowy projekt aplikacji sieci web platformy ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. W polu **Nazwa** wpisz nazwę projektu. W tym samouczku jest używana nazwa „todo”. Jeśli zdecydujesz się używać coś innego niż tej nazwy, następnie wszędzie tam, gdzie ten samouczek zawiera informacje o przestrzeń nazw todo, dostosować podane przykłady kodu do użycia, niezależnie od rodzaju nosi nazwę aplikacji. 

4. Wybierz **Przeglądaj** przejdź do folderu, w którym chcesz utworzyć projekt. Wybierz pozycję **Utwórz**. 

5. **Tworzenie nowej aplikacji sieci Web platformy ASP.NET Core** pojawi się okno dialogowe. Na liście szablonów wybierz **aplikacji sieci Web (Model-View-Controller)** .

6. Wybierz **Utwórz** i umożliwić programowi Visual Studio szkielety wokół pustego szablonu platformy ASP.NET Core MVC. 

7. Po zakończeniu tworzenia standardowej aplikacji MVC przez program Visual Studio będzie dostępna pusta aplikacja platformy ASP.NET, którą można uruchomić lokalnie.

## <a name="add-nuget-packages"></a>Krok 3: dodawanie pakietu NuGet usługi Azure Cosmos DB do projektu

Teraz, gdy większość kodu platformy ASP.NET Core MVC framework, którego potrzebujemy do tego rozwiązania, możemy dodać pakiety NuGet, wymagane do połączenia z usługą Azure Cosmos DB.

1. Zestaw SDK platformy .NET dla usługi Azure Cosmos DB ma postać pakietu NuGet i jest dystrybuowany jako taki pakiet. Aby pobrać pakiet NuGet w programie Visual Studio, użyj menedżera pakietów NuGet w programie Visual Studio — kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
   
   ![Zrzut ekranu przedstawiający opcje prawego przycisku myszy dla projektu aplikacji sieci web w Eksploratorze rozwiązań z NuGet wyróżnioną pozycją Zarządzaj pakietami.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Zostanie wyświetlone okno dialogowe **Zarządzanie pakietami NuGet**. W polu **Przeglądaj** obszaru pakietów NuGet wpisz ciąg **Microsoft.Azure.Cosmos**. Na liście wyników, zainstaluj **Microsoft.Azure.Cosmos** pakietu. Jej pobiera i instaluje pakiet usługi Azure Cosmos DB i jego zależności. Wybierz **akceptuję** w **akceptacja licencji** okna, aby ukończyć instalację.
   
   Inna możliwość to zainstalowanie pakietu NuGet za pomocą konsoli menedżera pakietów. W tym celu w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet**, a następnie wybierz pozycję **Konsola menedżera pakietów**. W wierszu polecenia wpisz następujące polecenie:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Po zainstalowaniu pakietu projektu programu Visual Studio może zawierać odwołanie do biblioteki do Microsoft.Azure.Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Krok 4. Konfigurowanie aplikacji platformy ASP.NET Core MVC

Teraz możemy dodać modele, widoki i kontrolery do aplikacji MVC:

* [Dodaj model](#add-a-model).
* [Dodaj kontroler](#add-a-controller).
* [Dodaj widoki](#add-views).

### <a name="add-a-model"></a> Dodawanie modelu

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**. **Dodaj nowy element** pojawi się okno dialogowe.

1. Nadaj nazwę nowej klasie **Item.cs** i wybierz **Dodaj**. 

1. Następnie Zastąp kod w klasie "Item.cs" z następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Dane z usługi Azure Cosmos DB zostaną przekazane i zapisane w formacie JSON. Aby kontrolować sposób obiekty są serializacji/deserializacji przez program JSON.NET, możesz użyć **JsonProperty** atrybutu, jak pokazano w **elementu** klasy został utworzony. Nie tylko możesz kontrolować format nazwy właściwości, że zbliża się do postaci JSON, można również zmienić właściwości platformy .NET tak, jak w przypadku **Ukończono** właściwości. 

### <a name="add-a-controller"></a>Dodawanie kontrolera

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Kontroler**. Zostanie wyświetlone okno dialogowe **Dodawanie szkieletu**.

1. Wybierz **kontroler MVC — pusty** i wybierz **Dodaj**.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu z kontroler MVC — pusty wyróżnioną](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nadaj nazwę nowemu kontrolerowi **ItemController**i Zastąp kod w tym pliku następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Dodanie tego atrybutu to nie jedyna korzyść — widoki powinny też współdziałać z tokenem zabezpieczającym przed fałszerstwem. Aby uzyskać więcej informacji na temat i przykłady poprawnej implementacji, zobacz [zapobieganie Cross-Site Request Forgery][Preventing Cross-Site Request Forgery] . The source code provided on [GitHub][GitHub] zawiera pełną implementację.

   Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby uzyskać więcej informacji, zobacz [Basic CRUD Operations in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

### <a name="add-views"></a>Dodawanie widoków

Następnie utworzymy trzy następujące widoki: 

* [Dodaj widok elementu listy](#AddItemIndexView).
* [Dodaj widok nowego elementu](#AddNewIndexView).
* [Dodaj widok edycji elementu](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Dodawanie widoku elementu listy

1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, kliknij prawym przyciskiem myszy pusty folder **Item** utworzony wcześniej przez program Visual Studio po dodaniu elementu **ItemController**, kliknij polecenie **Dodaj**, a następnie pozycję **Widok**.
   
   ![Zrzut ekranu z Eksploratora rozwiązań przedstawiający folder Item utworzony w programie Visual Studio z wyróżnionymi poleceniami Dodaj widok](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. W oknie dialogowym **Dodawanie widoku** zaktualizuj następujące wartości:
   
   * W polu **Nazwa widoku** wpisz ***Index*** (Indeks).
   * W polu **Szablon** wybierz pozycję ***Lista***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
     
   ![Zrzut ekranu przedstawiający okno dialogowe dodawania widoku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Po dodaniu tych wartości wybierz przycisk **Dodaj**. Program Visual Studio utworzy nowy widok szablonu. Po zakończeniu tej operacji zostanie otwarty utworzony plik cshtml. Możemy zamknąć ten plik w programie Visual Studio, ponieważ wrócimy do niego później.

#### <a name="AddNewIndexView"></a>Dodawanie widoku nowego elementu

Podobnie jak w przypadku widoku umożliwiającego wyświetlanie elementów utwórz widok, który pozwala tworzyć elementy. W tym celu wykonaj następujące kroki:

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Item**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Widok**.

1. W oknie dialogowym **Dodawanie widoku** zaktualizuj następujące wartości:
   
   * W polu **Nazwa widoku** wpisz ***Create*** (Tworzenie).
   * W polu **Szablon** wybierz pozycję ***Tworzenie***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
   * Wybierz pozycję **Dodaj**.
   
#### <a name="AddEditIndexView"></a>Dodawanie widoku edycji elementu

Na koniec wykonaj następujące kroki, aby dodać widok umożliwiający edycję elementu:

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Item**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Widok**.

1. W oknie dialogowym **Dodawanie widoku** wykonaj następujące czynności:
   
   * W polu **Nazwa widoku** wpisz ***Edit*** (Edycja).
   * W polu **Szablon** wybierz pozycję ***Edycja***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
   * Wybierz pozycję **Dodaj**.

Po zakończeniu zamknij wszystkie dokumenty cshtml w programie Visual Studio. Wrócimy do tych widoków później.

## <a name="connect-to-cosmosdb"></a>Krok 5: Łączenie z usługą Azure Cosmos DB 

Po przygotowaniu standardowych zasobów wzorca MVC możemy zacząć dodawać kod umożliwiający połączenie się z usługą Azure Cosmos DB i wykonywanie operacji CRUD. 

### <a name="perform-crud-operations"></a> Wykonywanie operacji CRUD na danych

Najpierw musisz tutaj dodać klasę, która zawiera logikę umożliwiającą połączenie z usługą Azure Cosmos DB i używanie jej. Na potrzeby tego samouczka umieściliśmy tę logikę do klasy o nazwie `CosmosDBService` oraz interfejs o nazwie `ICosmosDBService`. Ta usługa wykonuje CRUD i odczytu kanału informacyjnego operacje, takie jak wyświetlanie niezakończonych elementów, tworzenia, edytowania i usuwania elementów. 

1. W **Eksploratorze rozwiązań** utwórz nowy folder w ramach projektu o nazwie **Services**.

1. Kliknij prawym przyciskiem myszy folder **Services**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**. Nadaj nowej klasie **CosmosDBService** i wybierz **Dodaj**.

1. Dodaj następujący kod do **CosmosDBService** klasy i Zastąp kod w pliku następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Powtórz kroki 2 i 3, natomiast w tym momencie i z klasy o nazwie **ICosmosDBService**i Dodaj następujący kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Powyższy kod odbiera `CosmosClient` jako część konstruktora. Następujące potoku platformy ASP.NET Core, musimy go w projekcie **Startup.cs** i zainicjowania klienta na podstawie konfiguracji pojedyncze wystąpienie dodane za pośrednictwem [wstrzykiwanie zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). W **ConfigureServices** definiujemy obsługi:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. W ramach tego samego pliku definiujemy naszych metody pomocnika **InitializeCosmosClientInstanceAsync**, co spowoduje odczytać konfiguracji i zainicjowania klienta.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. Konfiguracja jest zdefiniowana w projekcie **appsettings.json** pliku. Otwórz go, a następnie dodaj sekcję o nazwie **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Teraz po uruchomieniu aplikacji, potoku platformy ASP.NET Core zostanie wystąpienia **CosmosDbService** i obsługiwać pojedyncze wystąpienie jako wzorca Singleton; gdy **ItemController** służy do przetwarzania żądania po stronie klienta, jego będą otrzymywać to pojedyncze wystąpienie i będzie można go używać do wykonywania operacji CRUD.

Jeśli kompilujesz i uruchomisz projekt teraz, zobaczysz coś, który wygląda w następujący sposób:

![Zrzut ekranu przedstawiający aplikację sieci web listy zadań do wykonania utworzone w ramach tego samouczka bazy danych](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Krok 6. Uruchamianie aplikacji lokalnie

Aby przetestować aplikację na komputerze lokalnym, wykonaj następujące czynności:

1. Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:
   
   ![Zrzut ekranu przedstawiający aplikację sieci web listy zadań do wykonania utworzone w ramach tego samouczka](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Kliknij link **Create new** (Utwórz nowy) i dodaj wartości w polach **Name** (Nazwa) i **Description** (Opis). Nie zaznaczaj pola wyboru **Completed** (Zakończono) — w przeciwnym razie nowy element zostanie dodany jako zakończony i nie zostanie wyświetlony na początkowej liście.
   
3. Kliknij przycisk **Create** (Utwórz) — nastąpi przekierowanie z powrotem do widoku **Index** (Indeks), a element zostanie wyświetlony na liście. Możesz także dodać kilka kolejnych elementów do listy rzeczy do zrobienia.

    ![Zrzut ekranu przedstawiający widok Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Kliknij pozycję **Edit** (Edytuj) obok **elementu** na liście. Zostanie wyświetlony widok **Edit** (Edycja), w którym można zaktualizować dowolną właściwość obiektu, w tym flagę **Completed** (Zakończono). Po oznaczeniu **Complete** Flaga, a następnie kliknij przycisk **Zapisz**, **elementu** będą wyświetlane jako zakończone na liście.
   
   ![Zrzut ekranu przedstawiający widok indeksu przy użyciu zaznaczone pole ukończone](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. W dowolnym momencie można sprawdzić stanu danych przy użyciu usługi Azure Cosmos DB [Eksplorator Cosmos](https://cosmos.azure.com) lub Eksploratora danych emulatora usługi Azure Cosmos DB.

6. Po przetestowaniu aplikacji naciśnij klawisze Ctrl+F5, aby zatrzymać jej debugowanie. Wszystko jest gotowe do wdrożenia.

## <a name="deploy-the-application-to-azure"></a>Krok 7: Wdrażanie aplikacji 
Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować tę aplikację, w **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Publikuj**.
   
2. W **Publikuj** okno dialogowe, wybierz opcję **usługi App Service**, a następnie wybierz **Utwórz nowy** Aby utworzyć profil usługi App Service, lub wybierz **wybierz istniejącą**do użycia istniejącego profilu.

3. Jeśli masz już profil usługi Azure App Service, wybierz subskrypcję z listy rozwijanej **Subskrypcja**. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu. Następnie wyszukaj wymaganą usługę Azure App Service i wybierz przycisk **OK**.
   
   ![Okno dialogowe usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Aby utworzyć nowy profil usługi Azure App Service, kliknij pozycję **Utwórz nowy** w oknie dialogowym **Publikowanie**. W oknie dialogowym **Tworzenie usługi App Service** wprowadź nazwę aplikacji internetowej oraz odpowiednią subskrypcję, grupę zasobów i plan usługi App Service, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Tworzenie usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

W ciągu kilku sekund program Visual Studio publikuje aplikację sieci web i otworzy w przeglądarce, gdzie możesz zobaczyć swój projekt, działające na platformie Azure!

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku wyjaśniono sposób tworzenia aplikacji sieci web platformy ASP.NET Core MVC, która mają dostęp do danych przechowywanych w usłudze Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

* [Więcej informacji na temat partycjonowania danych w usłudze Azure Cosmos DB](./partitioning-overview.md)
* [Dowiedz się więcej o bardziej zaawansowanych zapytań w usłudze Azure Cosmos DB](./how-to-sql-query.md)
* [Dowiedz się więcej o modelu danych w bardziej zaawansowanym scenariuszu](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
