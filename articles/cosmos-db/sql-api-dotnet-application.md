---
title: Samouczek aplikacji sieci Web ASP.NET Core MVC przy użyciu Azure Cosmos DB
description: Samouczek MVC ASP.NET Core do tworzenia aplikacji sieci Web MVC przy użyciu Azure Cosmos DB. Będziesz przechowywać dane JSON i uzyskiwać dostęp z aplikacji do zrobienia hostowanej na samouczku MVC Azure App Service-ASP NET Core.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 6af5f4c3ab028f8f0c6945eba86ec79dd6027680
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587468"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Samouczek: opracowywanie aplikacji sieci Web ASP.NET Core MVC z Azure Cosmos DB przy użyciu zestawu SDK platformy .NET

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji ASP.NET MVC hostowanej na platformie Azure. W tym samouczku użyto zestawu .NET SDK v3. Na poniższej ilustracji przedstawiono stronę sieci Web, która zostanie utworzona za pomocą przykładu w tym artykule:

![Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Jeśli nie masz czasu na ukończenie tego samouczka, możesz pobrać kompletny przykładowy projekt z usługi [GitHub][GitHub].

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie konta usługi Azure Cosmos
> * Tworzenie aplikacji ASP.NET Core MVC
> * Łączenie aplikacji z usługą Azure Cosmos DB
> * Wykonywanie operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych

> [!TIP]
> W tym samouczku założono, że masz wcześniejsze środowisko korzystania z ASP.NET Core MVC i Azure App Service. Jeśli dopiero zaczynasz korzystać z narzędzia ASP.NET Core lub [wstępnie wymaganych narzędzi](#prerequisites), zalecamy pobranie kompletnego przykładowego projektu z usługi [GitHub][GitHub], dodanie wymaganych pakietów NuGet i uruchomienie go. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Program Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Wszystkie zrzuty ekranu w tym artykule pochodzą z Microsoft Visual Studio Community 2019. Jeśli używasz innej wersji, ekrany i opcje mogą nie być zgodne całkowicie. Rozwiązanie powinno być wykonane, jeśli spełniasz wymagania wstępne.

## <a name="create-an-azure-cosmos-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos

Zacznijmy od utworzenia konta usługi Azure Cosmos. Jeśli masz już konto interfejsu API SQL Azure Cosmos DB lub jeśli korzystasz z emulatora Azure Cosmos DB, przejdź do [kroku 2: Tworzenie nowej aplikacji ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

W następnej sekcji utworzysz nową aplikację ASP.NET Core MVC.

## <a name="create-a-new-mvc-application"></a>Krok 2. Tworzenie nowej aplikacji ASP.NET Core MVC

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**Znajdź i wybierz **ASP.NET Core aplikację sieci Web** dla C#. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Utwórz nowy projekt aplikacji sieci Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. W obszarze **Konfigurowanie nowego projektu**Nazwij projekt do *zrobienia* i wybierz pozycję **Utwórz**.

1. W obszarze **Utwórz nową aplikację sieci web ASP.NET Core**wybierz pozycję **aplikacja sieci Web (Model-View-Controller)** . Wybierz pozycję **Utwórz** , aby kontynuować.

   Program Visual Studio tworzy pustą aplikację MVC.

1. Wybierz pozycję **debuguj** > **Rozpocznij debugowanie** lub F5, aby uruchomić aplikację ASP.NET lokalnie.

## <a name="add-nuget-packages"></a>Krok 3. Dodawanie pakietu NuGet usługi Azure Cosmos DB do projektu

Teraz, gdy mamy najwięcej ASP.NET Core kodu platformy MVC dla tego rozwiązania, dodamy pakiety NuGet wymagane do nawiązania połączenia z Azure Cosmos DB.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**Wyszukaj i wybierz pozycję **Microsoft. Azure. Cosmos**. Wybierz pozycję **Zainstaluj**.

   ![Zainstaluj pakiet NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Program Visual Studio pobiera i instaluje pakiet Azure Cosmos DB wraz z jego zależnościami.

   Do zainstalowania pakietu NuGet można także użyć **konsoli Menedżera pakietów** . W tym celu wybierz kolejno pozycje **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów**. W wierszu polecenia wpisz następujące polecenie:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Krok 4. Konfigurowanie aplikacji ASP.NET Core MVC

Teraz Dodajmy modele, widoki i kontrolery do tej aplikacji MVC.

### <a name="add-a-model"></a> Dodawanie modelu

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **modele** , a następnie wybierz polecenie **Dodaj** **klasę** > .

1. W obszarze **Dodaj nowy element**nadaj nowej klasie nazwę *Item.cs* i wybierz pozycję **Dodaj**.

1. Zastąp zawartość klasy *Item.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB używa formatu JSON do przenoszenia i przechowywania danych. Można użyć atrybutu `JsonProperty`, aby kontrolować sposób serializacji JSON i deserializacji obiektów. Klasa `Item` demonstruje atrybut `JsonProperty`. Ten kod steruje formatem nazwy właściwości, która znajduje się w formacie JSON. Zmienia również nazwę właściwości .NET `Completed`.

### <a name="add-views"></a>Dodawanie widoków

Następnie Utwórzmy następujące trzy widoki.

* Dodawanie widoku elementu listy
* Dodawanie widoku nowego elementu
* Dodawanie widoku edycji elementu

#### <a name="AddItemIndexView"></a>Dodawanie widoku elementu listy

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **widoki** i wybierz polecenie **Dodaj** > **Nowy folder**. Nadaj nazwę *elementowi*folderu.

1. Kliknij prawym przyciskiem myszy folder pusty **element** , a następnie wybierz polecenie **Dodaj** > **Widok**.

1. W obszarze **Dodaj widok MVC**podaj następujące wartości:

   * W polu **Nazwa widoku**wpisz *index*.
   * W obszarze **szablon**wybierz pozycję **Lista**.
   * W obszarze **Klasa modelu**wybierz pozycję **element (do zrobienia. Modele)** .
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie widoku MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Po dodaniu tych wartości wybierz przycisk **Dodaj**. Program Visual Studio utworzy nowy widok szablonu.

Po zakończeniu program Visual Studio otworzy plik *cshtml* , który tworzy. Możesz zamknąć ten plik w programie Visual Studio. Powrócimy do niego później.

#### <a name="AddNewIndexView"></a>Dodawanie widoku nowego elementu

Podobnie jak w przypadku widoku umożliwiającego wyświetlanie elementów utwórz widok, który pozwala tworzyć elementy. W tym celu wykonaj następujące kroki:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **elementu** , a następnie wybierz polecenie **Dodaj** > **Widok**.

1. W obszarze **Dodaj widok MVC**wprowadź następujące zmiany:

   * W polu **Nazwa widoku**wpisz polecenie *Utwórz*.
   * W obszarze **szablon**wybierz pozycję **Utwórz**.
   * W obszarze **Klasa modelu**wybierz pozycję **element (do zrobienia. Modele)** .
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.
   * Wybierz pozycję **Dodaj**.

#### <a name="AddEditIndexView"></a>Dodawanie widoku edycji elementu

Na koniec wykonaj następujące kroki, aby dodać widok umożliwiający edycję elementu:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **elementu** , a następnie wybierz pozycję **Dodaj** > **Widok**.

1. W obszarze **Dodaj widok MVC**wprowadź następujące zmiany:

   * W polu **Nazwa widoku** wpisz *Edit* (Edycja).
   * W polu **Szablon** wybierz pozycję **Edycja**.
   * W polu **Klasa modelu** wybierz pozycję **Item (todo.Models)** .
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.
   * Wybierz pozycję **Dodaj**.

Po wykonaniu tych kroków zamknij wszystkie dokumenty *cshtml* w programie Visual Studio w celu późniejszego powrotu do tych widoków.

### <a name="add-a-controller"></a>Dodawanie kontrolera

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **controllers** , a następnie wybierz pozycję **Dodaj** **kontroler** > .

1. W obszarze **Dodawanie szkieletu**wybierz pozycję **kontroler MVC — puste** i wybierz pozycję **Dodaj**.

   ![Wybierz kontroler MVC — pusty w obszarze Dodawanie szkieletu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nazwij nowy kontroler *ItemController*.

1. Zastąp zawartość *ItemController.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Twoje widoki powinny również współpracować z tym tokenem chroniącym przed fałszerstwem. Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [zapobieganie atakom przez wiele witryn (CSRF) w aplikacji ASP.NET MVC][Preventing Cross-Site Request Forgery]. Kod źródłowy dostępny w usłudze [GitHub][GitHub] zawiera pełną implementację.

Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby uzyskać więcej informacji, zobacz [Samouczek: Implementowanie funkcji CRUD przy użyciu Entity Framework w ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Krok 5. Łączenie się z usługą Azure Cosmos DB

Teraz, gdy są brane pod uwagę standardowe elementy MVC, przyjrzyjmy się dodawaniu kodu w celu nawiązania połączenia z Azure Cosmos DB i wykonywania operacji CRUD.

### <a name="perform-crud-operations"></a>Wykonywanie operacji CRUD na danych

Najpierw dodamy klasę zawierającą logikę umożliwiającą nawiązanie połączenia i użycie Azure Cosmos DB. W tym samouczku będziemy hermetyzować tę logikę do klasy o nazwie `CosmosDBService` i interfejsu o nazwie `ICosmosDBService`. Ta usługa wykonuje operacje CRUD. Wykonuje również operacje odczytu źródła danych, takie jak wyświetlanie niekompletnych elementów, tworzenie, edytowanie i usuwanie elementów.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **Nowy folder**. Nazwij *usługi*folderów.

1. Kliknij prawym przyciskiem myszy folder **Services** , a następnie wybierz pozycję **Add** > **class**. Nadaj nowej klasie nazwę *CosmosDBService* i wybierz pozycję **Dodaj**.

1. Zastąp zawartość *CosmosDBService.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Powtórz dwa poprzednie kroki, ale tym razem użyj nazwy *ICosmosDBService*i użyj następującego kodu:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. W obsłudze **ConfigureServices** Dodaj następujący wiersz:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    Kod w poprzednim kroku otrzymuje `CosmosClient` w ramach konstruktora. Po poASP.NET Core potoku należy przejść do pliku *Startup.cs* projektu. Kod w tym kroku inicjuje klienta w oparciu o konfigurację jako pojedyncze wystąpienie, które ma zostać dodane przez [iniekcję zależności w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. W tym samym pliku Dodaj następującą metodę **InitializeCosmosClientInstanceAsync**, która odczytuje konfigurację i inicjuje klienta.

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync":::

1. Zdefiniuj konfigurację w pliku *appSettings. JSON* projektu. Otwórz plik i Dodaj sekcję o nazwie **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

Jeśli uruchomisz aplikację, ASP.NET Core tworzy wystąpienia potoku **CosmosDbService** i utrzymuje pojedyncze wystąpienie jako pojedyncze. Gdy **ItemController** przetwarza żądania po stronie klienta, otrzymuje to pojedyncze wystąpienie i może używać go do operacji CRUD.

Jeśli kompilujesz i uruchamiasz ten projekt teraz, zobaczysz coś, co wygląda następująco:

![Zrzut ekranu przedstawiający aplikację sieci Web z listą zadań do zrobienia utworzoną przez ten samouczek dotyczący bazy danych](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>Krok 6. Uruchamianie aplikacji lokalnie

Aby przetestować aplikację na komputerze lokalnym, wykonaj następujące czynności:

1. Wybierz klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:

   ![Zrzut ekranu przedstawiający aplikację sieci Web z listą rzeczy do zrobienia utworzoną przez ten samouczek](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. Wybierz opcję **Utwórz nowe** łącze i Dodaj wartości do pól **Nazwa** i **Opis** . Pozostaw zaznaczone pole wyboru **ukończone** . W przypadku wybrania tej opcji aplikacja dodaje nowy element w stanie ukończone. Element nie jest już wyświetlany na liście początkowej.

1. Wybierz pozycję **Utwórz**. Aplikacja wyśle Cię z powrotem do widoku **indeksu** , a element zostanie wyświetlony na liście. Do listy **czynności do wykonania** można dodać kilka elementów.

    ![Zrzut ekranu przedstawiający widok indeksu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Wybierz pozycję **Edytuj** obok **elementu** na liście. Aplikacja otwiera widok **edycji** , w którym można zaktualizować każdą właściwość obiektu, łącznie z flagą **ukończoną** . Jeśli wybierzesz pozycję **ukończono** i wybierzesz pozycję **Zapisz**, aplikacja wyświetli **element** jako wykonany na liście.

   ![Zrzut ekranu przedstawiający widok indeksu z zaznaczonym polem ukończono](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Sprawdź stan danych w usłudze Azure Cosmos DB przy użyciu [Eksploratora Cosmos](https://cosmos.azure.com) lub Eksplorator danych emulatora Azure Cosmos DB.

1. Po przetestowaniu aplikacji wybierz kombinację klawiszy CTRL + F5, aby zatrzymać debugowanie aplikacji. Wszystko jest gotowe do wdrożenia.

## <a name="deploy-the-application-to-azure"></a>Krok 7. Wdrażanie aplikacji

Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować tę aplikację, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.

1. W obszarze **Wybierz element docelowy publikowania**wybierz pozycję **App Service**.

1. Aby użyć istniejącego profilu App Service, wybierz **pozycję Wybierz istniejące**, a następnie wybierz pozycję **Publikuj**.

1. W obszarze **App Service**wybierz **subskrypcję**. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu.

1. Znajdź swój profil, a następnie wybierz przycisk **OK**. Następnie wyszukaj wymaganą usługę Azure App Service i wybierz przycisk **OK**.

   ![Okno dialogowe usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Innym rozwiązaniem jest utworzenie nowego profilu:

1. Tak jak w poprzedniej procedurze, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.
  
1. W obszarze **Wybierz element docelowy publikowania**wybierz pozycję **App Service**.

1. W obszarze **Wybieranie elementu docelowego publikowania**wybierz pozycję **Utwórz nowy** i wybierz pozycję **Publikuj**.

1. W **App Service**wprowadź nazwę aplikacji sieci Web i odpowiednią subskrypcję, grupę zasobów i plan hostingu, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Tworzenie usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

W ciągu kilku sekund program Visual Studio opublikuje aplikację sieci Web i uruchomi przeglądarkę, w której można zobaczyć swój projekt uruchomiony na platformie Azure.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia aplikacji sieci Web ASP.NET Core MVC. Aplikacja może uzyskiwać dostęp do danych przechowywanych w Azure Cosmos DB. Teraz możesz kontynuować z następującymi zasobami:

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](./partitioning-overview.md)
* [Wprowadzenie do zapytań SQL](./how-to-sql-query.md)
* [Jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu wziętego z życia](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
