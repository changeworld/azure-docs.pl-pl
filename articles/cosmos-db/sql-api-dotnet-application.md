---
title: 'Samouczek ASP.NET Core MVC dla Azure Cosmos DB: opracowywanie aplikacji internetowych'
description: Samouczek MVC ASP.NET Core do tworzenia aplikacji sieci Web MVC przy użyciu Azure Cosmos DB. Będziesz przechowywać dane JSON i uzyskiwać dostęp z aplikacji do zrobienia hostowanej na samouczku MVC Azure App Service-ASP NET Core.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 9824e1468604763834e37abe94290d68d81077ab
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020129"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Samouczek: Opracowywanie ASP.NET Core aplikacji sieci Web MVC z Azure Cosmos DB przy użyciu zestawu .NET SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji ASP.NET MVC hostowanej na platformie Azure. W tym samouczku użyto zestawu .NET SDK v3. Na poniższej ilustracji przedstawiono stronę internetową, którą utworzymy, korzystając z przykładu opisanego w tym artykule:
 
![Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Jeśli nie masz czasu na ukończenie tego samouczka, możesz pobrać kompletny przykładowy projekt z usługi [GitHub][GitHub].

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Tworzenie aplikacji ASP.NET Core MVC
> * Łączenie aplikacji z usługą Azure Cosmos DB 
> * Wykonywanie operacji CRUD na danych

> [!TIP]
> W tym samouczku założono, że masz wcześniejsze środowisko korzystania z ASP.NET Core MVC i Azure App Service. Jeśli dopiero zaczynasz korzystać z narzędzia ASP.NET Core lub [wstępnie wymaganych narzędzi](#prerequisites), zalecamy pobranie kompletnego przykładowego projektu z usługi [GitHub][GitHub], dodanie wymaganych pakietów NuGet i uruchomienie go. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.

## <a name="prerequisites"></a>Wymagania wstępne 

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* **Aktywne konto platformy Azure:** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Wszystkie zrzuty ekranu w tym artykule zostały wykonane przy użyciu Microsoft Visual Studio Community 2019. Jeśli w Twoim systemie jest skonfigurowana inna wersja, być może Twoje ekrany i opcje nie będą całkiem zgodne, lecz jeśli spełniasz powyższe wymagania wstępne, to rozwiązanie powinno działać.

## <a name="create-an-azure-cosmos-account"></a>Krok 1: tworzenie konta usługi Azure Cosmos

Zacznijmy od utworzenia konta usługi Azure Cosmos. Jeśli masz już konto interfejsu API SQL usługi Azure Cosmos DB lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Tworzenie nowej aplikacji platformy ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

W następnej sekcji utworzysz nową aplikację ASP.NET Core MVC. 

## <a name="create-a-new-mvc-application"></a>Krok 2. Tworzenie nowej aplikacji ASP.NET Core MVC

1. W programie Visual Studio z menu **Plik** wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. **Nowy projekt** pojawi się okno dialogowe.

2. W oknie **Nowy projekt** Użyj pola wejściowego **Szukaj szablonów** , aby wyszukać ciąg "Web", a następnie wybierz pozycję **aplikacja sieci Web ASP.NET Core**. 

   ![Utwórz nowy projekt aplikacji sieci Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. W polu **Nazwa** wpisz nazwę projektu. W tym samouczku jest używana nazwa „todo”. Jeśli zdecydujesz się użyć czegoś innego niż ta nazwa, wszędzie tam, gdzie ten samouczek zawiera informacje o przestrzeni nazw do zrobienia, Dostosuj podane przykłady kodu, aby użyć ich jako nazwy aplikacji. 

4. Wybierz pozycję **Przeglądaj** , aby przejść do folderu, w którym chcesz utworzyć projekt. Wybierz pozycję **Utwórz**. 

5. Zostanie wyświetlone okno dialogowe **Tworzenie nowej aplikacji sieci Web ASP.NET Core** . Na liście szablony wybierz pozycję **aplikacja sieci Web (Model-View-Controller)** .

6. Wybierz pozycję Utwórz i Pozwól programowi Visual Studio na **Tworzenie** szkieletów wokół pustego szablonu ASP.NET Core MVC. 

7. Po zakończeniu tworzenia standardowej aplikacji MVC przez program Visual Studio będzie dostępna pusta aplikacja platformy ASP.NET, którą można uruchomić lokalnie.

## <a name="add-nuget-packages"></a>Krok 3: dodawanie pakietu NuGet usługi Azure Cosmos DB do projektu

Teraz, gdy mamy najwięcej ASP.NET Core kodu platformy MVC dla tego rozwiązania, dodamy pakiety NuGet wymagane do nawiązania połączenia z Azure Cosmos DB.

1. Zestaw SDK platformy .NET dla usługi Azure Cosmos DB ma postać pakietu NuGet i jest dystrybuowany jako taki pakiet. Aby pobrać pakiet NuGet w programie Visual Studio, użyj menedżera pakietów NuGet w programie Visual Studio — kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
   
   ![Zrzut ekranu opcji dostępnych po kliknięciu prawym przyciskiem myszy dla projektu aplikacji sieci Web w Eksplorator rozwiązań, z wyróżnioną pozycją Zarządzaj pakietami NuGet.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Zostanie wyświetlone okno dialogowe **Zarządzanie pakietami NuGet**. W polu **Przeglądaj** obszaru pakietów NuGet wpisz ciąg **Microsoft.Azure.Cosmos**. Na podstawie wyników Zainstaluj pakiet **Microsoft. Azure. Cosmos** . Pobiera i instaluje pakiet Azure Cosmos DB wraz z jego zależnościami. Zaznacz opcję Akceptuję w oknie **Akceptacja licencji** , aby zakończyć instalację.
   
   Inna możliwość to zainstalowanie pakietu NuGet za pomocą konsoli menedżera pakietów. W tym celu w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet**, a następnie wybierz pozycję **Konsola menedżera pakietów**. W wierszu polecenia wpisz następujące polecenie:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Po zainstalowaniu pakietu projekt programu Visual Studio powinien zawierać odwołanie do biblioteki Microsoft. Azure. Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Krok 4. Konfigurowanie aplikacji ASP.NET Core MVC

Teraz możemy dodać modele, widoki i kontrolery do aplikacji MVC:

* [Dodaj model](#add-a-model).
* [Dodaj kontroler](#add-a-controller).
* [Dodaj widoki](#add-views).

### <a name="add-a-model"></a> Dodawanie modelu

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**. **Dodaj nowy element** pojawi się okno dialogowe.

1. Nadaj nowej klasie nazwę **Item.cs** i wybierz pozycję **Dodaj**. 

1. Następnie zastąp kod w klasie "Item.cs" następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Dane z usługi Azure Cosmos DB zostaną przekazane i zapisane w formacie JSON. Aby kontrolować sposób serializowania/deserializacji obiektów przez JSON.NET, można użyć atrybutu **JsonProperty** , jak pokazano w utworzonej klasie **Item** . Można nie tylko kontrolować format nazwy właściwości, która znajduje się w formacie JSON, a także zmienić nazwy właściwości platformy .NET, tak jak w przypadku właściwości **ukończone** . 

### <a name="add-views"></a>Dodawanie widoków

Następnie utworzymy trzy następujące widoki: 

* [Dodaj widok elementu listy](#AddItemIndexView).
* [Dodaj widok nowego elementu](#AddNewIndexView).
* [Dodaj widok edycji elementu](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Dodawanie widoku elementu listy

1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, kliknij prawym przyciskiem myszy pusty folder **Item** utworzony wcześniej przez program Visual Studio po dodaniu elementu **ItemController**, kliknij polecenie **Dodaj**, a następnie pozycję **Widok**.
   
   ![Zrzut ekranu przedstawiający Eksplorator rozwiązań pokazujący folder elementów utworzony przez program Visual Studio z wyróżnionymi poleceniami Dodaj widok](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. W oknie dialogowym **Dodawanie widoku** zaktualizuj następujące wartości:
   
   * W polu **Nazwa widoku** wpisz ***Index*** (Indeks).
   * W polu **Szablon** wybierz pozycję ***Lista***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
     
   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie widoku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

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

### <a name="add-a-controller"></a>Dodawanie kontrolera

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Kontroler**. Zostanie wyświetlone okno dialogowe **Dodawanie szkieletu**.

1. Wybierz pozycję **kontroler MVC — pusty** i wybierz pozycję **Dodaj**.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu z wyróżnioną opcją pusty kontroler MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nazwij nowy kontroler, **ItemController**i Zastąp kod w tym pliku następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Dodanie tego atrybutu to nie jedyna korzyść — widoki powinny też współdziałać z tokenem zabezpieczającym przed fałszerstwem. Aby uzyskać więcej informacji na temat tego tematu i Przykłady sposobu ich implementacji, zobacz [zapobieganie fałszerstwu żądań][Preventing Cross-Site Request Forgery]międzywitrynowych. Kod źródłowy dostępny w usłudze [GitHub][GitHub] zawiera pełną implementację.

   Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby uzyskać więcej informacji, zobacz [podstawowe operacje CRUD w ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Krok 5. Łączenie z usługą Azure Cosmos DB 

Po przygotowaniu standardowych zasobów wzorca MVC możemy zacząć dodawać kod umożliwiający połączenie się z usługą Azure Cosmos DB i wykonywanie operacji CRUD. 

### <a name="perform-crud-operations"></a> Wykonywanie operacji CRUD na danych

Najpierw musisz tutaj dodać klasę, która zawiera logikę umożliwiającą połączenie z usługą Azure Cosmos DB i używanie jej. W tym samouczku będziemy hermetyzować tę logikę do klasy o nazwie `CosmosDBService` i interfejsu o nazwie `ICosmosDBService`. Ta usługa wykonuje operacje CRUD i Read kanału informacyjnego, takie jak wyświetlanie niekompletnych elementów, tworzenie, edytowanie i usuwanie elementów. 

1. W **Eksploratorze rozwiązań** utwórz nowy folder w ramach projektu o nazwie **Services**.

1. Kliknij prawym przyciskiem myszy folder **Services**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**. Nadaj nowej klasie nazwę **CosmosDBService** i wybierz pozycję **Dodaj**.

1. Dodaj następujący kod do klasy **CosmosDBService** i Zastąp kod w tym pliku następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Powtórz kroki 2-3, ale tym razem dla klasy o nazwie **ICosmosDBService**i Dodaj następujący kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Poprzedni kod otrzymuje `CosmosClient` jako część konstruktora. Po poASP.NET Core potoku należy przejść do **Startup.cs** projektu i zainicjować klienta w oparciu o konfigurację jako pojedyncze wystąpienie, które ma zostać dodane za pośrednictwem iniekcji [zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). W programie obsługi **ConfigureServices** definiujemy:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. W tym samym pliku definiujemy nasze metody pomocnika **InitializeCosmosClientInstanceAsync**, co spowoduje odczytanie konfiguracji i zainicjowanie klienta.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)] 

1. Konfiguracja jest zdefiniowana w pliku **appSettings. JSON** projektu. Otwórz go i Dodaj sekcję o nazwie **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Teraz po uruchomieniu aplikacji potok ASP.NET Core będzie tworzyć wystąpienia **CosmosDbService** i obsługiwać pojedyncze wystąpienie jako pojedyncze; gdy **ItemController** jest używany do przetwarzania żądań po stronie klienta, otrzyma to jedno wystąpienie i będzie mogło używać go do wykonywania operacji CRUD.

Jeśli kompilujesz i uruchamiasz ten projekt teraz, zobaczysz coś, co wygląda następująco:

![Zrzut ekranu przedstawiający aplikację sieci Web z listą zadań do zrobienia utworzoną przez ten samouczek dotyczący bazy danych](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Krok 6. Uruchamianie aplikacji lokalnie

Aby przetestować aplikację na komputerze lokalnym, wykonaj następujące czynności:

1. Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:
   
   ![Zrzut ekranu przedstawiający aplikację sieci Web z listą rzeczy do zrobienia utworzoną przez ten samouczek](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Kliknij link **Create new** (Utwórz nowy) i dodaj wartości w polach **Name** (Nazwa) i **Description** (Opis). Nie zaznaczaj pola wyboru **Completed** (Zakończono) — w przeciwnym razie nowy element zostanie dodany jako zakończony i nie zostanie wyświetlony na początkowej liście.
   
3. Kliknij przycisk **Create** (Utwórz) — nastąpi przekierowanie z powrotem do widoku **Index** (Indeks), a element zostanie wyświetlony na liście. Możesz także dodać kilka kolejnych elementów do listy rzeczy do zrobienia.

    ![Zrzut ekranu przedstawiający widok indeksu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Kliknij pozycję **Edit** (Edytuj) obok **elementu** na liście. Zostanie wyświetlony widok **Edit** (Edycja), w którym można zaktualizować dowolną właściwość obiektu, w tym flagę **Completed** (Zakończono). Jeśli oznaczesz flagę **Complete** i klikniesz pozycję **Save (Zapisz**), **element** zostanie wyświetlony jako zakończony na liście.
   
   ![Zrzut ekranu przedstawiający widok indeksu z zaznaczonym polem ukończono](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. W dowolnym momencie można sprawdzić stan danych w usłudze Azure Cosmos DB przy użyciu [Eksploratora Cosmos](https://cosmos.azure.com) lub Eksplorator danych emulatora Azure Cosmos DB.

6. Po przetestowaniu aplikacji naciśnij klawisze Ctrl+F5, aby zatrzymać jej debugowanie. Wszystko jest gotowe do wdrożenia.

## <a name="deploy-the-application-to-azure"></a>Krok 7: Wdrażanie aplikacji 
Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować tę aplikację, w **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Publikuj**.
   
2. W oknie dialogowym **Publikowanie** wybierz pozycję **App Service**, a następnie wybierz pozycję **Utwórz nowy** , aby utworzyć profil App Service, lub wybierz **pozycję Wybierz istniejący** , aby użyć istniejącego profilu.

3. Jeśli masz już profil usługi Azure App Service, wybierz subskrypcję z listy rozwijanej **Subskrypcja**. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu. Następnie wyszukaj wymaganą usługę Azure App Service i wybierz przycisk **OK**.
   
   ![Okno dialogowe usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Aby utworzyć nowy profil usługi Azure App Service, kliknij pozycję **Utwórz nowy** w oknie dialogowym **Publikowanie**. W oknie dialogowym **Tworzenie usługi App Service** wprowadź nazwę aplikacji internetowej oraz odpowiednią subskrypcję, grupę zasobów i plan usługi App Service, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Tworzenie usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

W ciągu kilku sekund program Visual Studio opublikuje aplikację sieci Web i uruchomi przeglądarkę, w której można zobaczyć swój projekt uruchomiony na platformie Azure.

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób tworzenia aplikacji sieci Web ASP.NET Core MVC, która może uzyskiwać dostęp do danych przechowywanych w Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

* [Informacje na temat partycjonowania danych w Azure Cosmos DB](./partitioning-overview.md)
* [Dowiedz się więcej o tym, jak wykonywać bardziej zaawansowane zapytania w Azure Cosmos DB](./how-to-sql-query.md)
* [Dowiedz się, jak modelować dane w bardziej zaawansowanym scenariuszu](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
