---
title: Samouczek do tworzenia aplikacji sieci web platformy ASP.NET MVC za pomocą usługi Azure Cosmos DB przy użyciu platformy .NET w wersji zapoznawczej zestawu SDK.
description: W tym samouczku opisano, jak utworzyć aplikację sieci web ASP .NET MVC za pomocą usługi Azure Cosmos DB. W aplikacji z listą zadań do wykonania hostowanej na platformie Azure będziesz zapisywać dane JSON i uzyskiwać dostęp do nich.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: bf1da7e8a1041b15076ebda6eeac9b0a75c567c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439031"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Samouczek: Tworzenie aplikacji sieci web ASP.NET MVC za pomocą usługi Azure Cosmos DB przy użyciu platformy .NET w wersji zapoznawczej zestawu SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET w wersji zapoznawczej](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji ASP.NET MVC hostowanej na platformie Azure. W tym samouczku użyjesz V3 zestawu SDK platformy .NET, która jest obecnie dostępna w wersji zapoznawczej. Na poniższej ilustracji przedstawiono stronę internetową, którą utworzymy, korzystając z przykładu opisanego w tym artykule:
 
![Zrzut ekranu przedstawiający listę zadań do wykonania utworzone w ramach tego samouczka - ASP platformy ASP.NET MVC, samouczek krok po kroku, aplikacja sieci web MVC](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Jeśli nie masz czasu na pracę z tym samouczkiem, możesz pobrać kompletny przykładowy projekt z witryny [GitHub][GitHub]. 

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Tworzenie aplikacji ASP.NET MVC
> * Łączenie aplikacji z usługą Azure Cosmos DB 
> * Wykonywanie operacji CRUD na danych

> [!TIP]
> Ten samouczek zakłada, że masz już pewne doświadczenie w korzystaniu z platformy ASP.NET MVC i usługi Azure Websites. Jeśli nie znasz platformy ASP.NET lub [wstępnie wymaganych narzędzi](#prerequisites), zalecamy pobranie kompletnego przykładowego projektu z witryny [GitHub][GitHub] i uruchomienie go po dodaniu wymaganych pakietów NuGet. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.

## <a name="prerequisites"></a>Wymagania wstępne 

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* **Aktywne konto platformy Azure:** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Zestaw Azure SDK dla platformy .NET dla programu Visual Studio 2017 dostępny za pośrednictwem Instalatora programu Visual Studio.

Wszystkie zrzuty ekranu w tym artykule wykonano za pomocą programu Microsoft Visual Studio Community 2017. Jeśli w Twoim systemie jest skonfigurowana inna wersja, być może Twoje ekrany i opcje nie będą całkiem zgodne, lecz jeśli spełniasz powyższe wymagania wstępne, to rozwiązanie powinno działać.

## <a name="create-an-azure-cosmos-account"></a>Krok 1: tworzenie konta usługi Azure Cosmos

Zacznijmy od utworzenia konta usługi Azure Cosmos. Jeśli masz już konto interfejsu API SQL usługi Azure Cosmos DB lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Tworzenie nowej aplikacji platformy ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

W następnej sekcji utworzysz nową aplikację ASP.NET MVC. 

## <a name="create-a-new-mvc-application"></a>Krok 2: tworzenie nowej aplikacji platformy ASP.NET MVC

1. W programie Visual Studio z menu **Plik** wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. Zostanie wyświetlone okno dialogowe **Nowy projekt**.

2. W oknie **Nowy projekt** rozwiń pozycje **Zainstalowane szablony**, **Visual C#**, **Internet**, a następnie wybierz pozycję **Aplikacja sieci Web ASP.NET**. 

   ![Tworzenie nowego projektu aplikacji internetowej ASP.NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. W polu **Nazwa** wpisz nazwę projektu. W tym samouczku jest używana nazwa „todo”. Jeśli wybierzesz inną nazwę, to wszędzie tam, gdzie jest wspomniana przestrzeń nazw todo, musisz dostosować podane przykłady kodu tak, aby używały nazwy Twojej aplikacji. 

4. Wybierz **Przeglądaj** przejdź do folderu, w którym chcesz utworzyć projekt, a następnie wybierz **platformy .NET framework 4.6.1** lub nowszej. Kliknij przycisk **OK**. 

5. Zostanie wyświetlone okno dialogowe **Nowa aplikacja internetowa platformy ASP.NET**. W okienku szablonów wybierz pozycję **MVC**.

6. Wybierz przycisk **OK**, aby umożliwić programowi Visual Studio przygotowanie szkieletu na podstawie pustego szablonu platformy ASP.NET MVC. 

7. Po zakończeniu tworzenia standardowej aplikacji MVC przez program Visual Studio będzie dostępna pusta aplikacja platformy ASP.NET, którą można uruchomić lokalnie.

## <a name="add-nuget-packages"></a>Krok 3: dodawanie pakietu NuGet usługi Azure Cosmos DB do projektu

Mamy już większość kodu platformy ASP.NET MVC potrzebnego w tym rozwiązaniu. Kolejnym krokiem jest dodanie pakietów NuGet wymaganych do nawiązania połączenia z usługą Azure Cosmos DB.

1. Zestaw SDK platformy .NET dla usługi Azure Cosmos DB ma postać pakietu NuGet i jest dystrybuowany jako taki pakiet. Aby pobrać pakiet NuGet w programie Visual Studio, użyj menedżera pakietów NuGet w programie Visual Studio — kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
   
   ![Zrzut ekranu przedstawiający opcje prawego przycisku myszy dla projektu aplikacji sieci web w Eksploratorze rozwiązań z NuGet wyróżnioną pozycją Zarządzaj pakietami.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Zostanie wyświetlone okno dialogowe **Zarządzanie pakietami NuGet**. W polu **Przeglądaj** obszaru pakietów NuGet wpisz ciąg **Microsoft.Azure.Cosmos**. Korzystając z wyników, zainstaluj wersję **Microsoft.Azure.Cosmos** 3.0.0.1-preview. Pakiet usługi Azure Cosmos DB zostanie pobrany i zainstalowany razem z wszystkimi zależnościami, takimi jak pakiet Newtonsoft.Json. Wybierz przycisk **OK** w oknie **Podgląd** i pozycję **Akceptuję** w oknie **Akceptacja licencji**, aby zakończyć instalację.
   
   Inna możliwość to zainstalowanie pakietu NuGet za pomocą konsoli menedżera pakietów. W tym celu w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet**, a następnie wybierz pozycję **Konsola menedżera pakietów**. W wierszu polecenia wpisz następujące polecenie:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. Po zainstalowaniu pakietu rozwiązanie Visual Studio powinno zawierać dwa nowe odwołania do bibliotek Microsoft.Azure.Documents.Client i Newtonsoft.Json.
  
## <a name="set-up-the-mvc-application"></a>Krok 4: konfigurowanie aplikacji platformy ASP.NET MVC

Teraz możemy dodać modele, widoki i kontrolery do aplikacji MVC:

* [Dodaj model](#add-a-model).
* [Dodaj kontroler](#add-a-controller).
* [Dodaj widoki](#add-views).

### <a name="add-a-model"></a> Dodawanie modelu

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**. Zostanie wyświetlone okno dialogowe **Dodawanie nowego elementu**.

1. Nadaj nowej klasie nazwę **TodoItem.cs** i wybierz pozycję **Dodaj**. 

1. Następnie zastąp kod klasy „Todoitem” następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Dane z usługi Azure Cosmos DB zostaną przekazane i zapisane w formacie JSON. Aby kontrolować sposób serializacji/deserializacji obiektów przez program JSON.NET, możesz użyć atrybutu **JsonProperty**, tak jak przedstawiono to w klasie **TodoItem**, którą właśnie utworzyliśmy. Możesz nie tylko kontrolować format nazwy właściwości umieszczanej w kodzie JSON, ale także zmienić nazwy właściwości platformy .NET, tak jak to zrobiono w przypadku właściwości **Description**. 

### <a name="add-a-controller"></a>Dodawanie kontrolera

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Kontroler**. Zostanie wyświetlone okno dialogowe **Dodawanie szkieletu**.

1. Wybierz pozycję **Kontroler MVC 5 — pusty**, a następnie wybierz polecenie **Dodaj**.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu z kontroler MVC 5 — pusty wyróżnioną](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nadaj nowemu kontrolerowi nazwę ** ItemController i zastąp kod w pliku następującym kodem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Dodanie tego atrybutu to nie jedyna korzyść — widoki powinny też współdziałać z tokenem zabezpieczającym przed fałszerstwem. Aby uzyskać więcej informacji na ten temat i przykłady poprawnej implementacji, zobacz [Preventing Cross-Site Request Forgery (Zapobieganie fałszerstwom żądania międzywitrynowego)][Preventing Cross-Site Request Forgery]. Kod źródłowy dostępny w usłudze [GitHub][GitHub] zawiera pełną implementację.
   
   Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby poznać więcej szczegółów, zobacz [Basic CRUD Operations in ASP.NET MVC(Podstawowe operacje CRUD na platformie ASP.NET MVC)][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Dodawanie widoków

Następnie utworzymy trzy następujące widoki: 

* [Dodaj widok elementu listy](#AddItemIndexView).
* [Dodaj widok nowego elementu](#AddNewIndexView).
* [Dodaj widok edycji elementu](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Dodawanie widoku elementu listy

1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, kliknij prawym przyciskiem myszy pusty folder **Item** utworzony wcześniej przez program Visual Studio po dodaniu elementu **ItemController**, kliknij polecenie **Dodaj**, a następnie pozycję **Widok**.
   
   ![Zrzut ekranu z Eksploratora rozwiązań przedstawiający folder Item utworzony w programie Visual Studio z wyróżnionymi poleceniami Dodaj widok](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. W oknie dialogowym **Dodawanie widoku** zaktualizuj następujące wartości:
   
   * W polu **Nazwa widoku** wpisz ***Index*** (Indeks).
   * W polu **Szablon** wybierz pozycję ***Lista***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
     
   ![Zrzut ekranu przedstawiający okno dialogowe dodawania widoku](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

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

Najpierw musisz tutaj dodać klasę, która zawiera logikę umożliwiającą połączenie z usługą Azure Cosmos DB i używanie jej. W tym samouczku zahermetyzujemy tę logikę w klasie o nazwie TodoItemService.cs. Ten kod pozwala odczytywać wartości punktu końcowego usługi Azure Cosmos DB z pliku konfiguracji i wykonywać operacje CRUD, takie jak wyświetlanie listy niezakończonych elementów oraz tworzenie, edytowanie i usuwanie elementów. 

1. W **Eksploratorze rozwiązań** utwórz nowy folder w ramach projektu o nazwie **Services**.

1. Kliknij prawym przyciskiem myszy folder **Services**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**. Nadaj nowej klasie nazwę **TodoItemService** i wybierz pozycję **Dodaj**.

1. Do klasy **TodoItemService** dodaj następujący kod, zastępując kod w pliku:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. Powyższy kod umożliwia odczyt wartości parametrów połączenia z pliku konfiguracji. Aby zaktualizować wartości parametrów połączenia na koncie usługi Azure Cosmos, otwórz plik **Web.config** w projekcie i dodaj następujące wiersze w sekcji `<AppSettings>`:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Zmień wartości punktu końcowego i parametru primarykey na wartości pobrane z bloku **Klucze** w witrynie Azure Portal. Użyj wartości **URI** z bloku Klucze jako wartości ustawienia punktu końcowego oraz wartości **KLUCZ PODSTAWOWY** lub **KLUCZ POMOCNICZY** z bloku Klucze jako wartości ustawienia kluczy. W ten sposób połączyliśmy usługę Azure Cosmos DB z aplikacją. Teraz możemy dodać logikę aplikacji.

1. Otwórz plik **Global.asax.cs** i dodaj następujący wiersz do metody **Application_Start**. 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   W tym momencie powinno być możliwe skompilowanie projektu rozwiązania bez żadnych błędów. Jeśli teraz uruchomisz aplikację, zostaną otwarte kontroler **HomeController** i widok **Index** tego kontrolera. Jest to domyślne zachowanie projektu szablonu MVC wybranego na początku. Zmieńmy routing aplikacji MVC, aby zmienić to zachowanie.

1. Otwórz plik ***App\_Start\RouteConfig.cs***, odszukaj wiersz rozpoczynający się od ciągu „defaults:” i dodaj do niego następujący kod:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

   W ten sposób platforma ASP.NET MVC zostanie poinformowana, że jeśli nie określisz wartości elementu URL na potrzeby kontrolowania zachowania routingu, to zamiast elementu **Home** zostanie użyty element **Item** jako kontroler i element **Index** jako widok.

Jeśli teraz uruchomisz aplikację, zostanie wywołany kontroler **ItemController**, który wywołuje metody GetItems klasy TodoItemService. Zostaną one zdefiniowane w następnej sekcji. 

Jeśli skompilujesz i uruchomisz projekt teraz, zobaczysz stronę podobną do następującej.    

![Zrzut ekranu przedstawiający aplikację sieci web listy zadań do wykonania utworzone w ramach tego samouczka bazy danych](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Krok 6. Uruchamianie aplikacji lokalnie

Aby przetestować aplikację na komputerze lokalnym, wykonaj następujące czynności:

1. Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:
   
   ![Zrzut ekranu przedstawiający aplikację sieci web listy zadań do wykonania utworzone w ramach tego samouczka bazy danych](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Kliknij link **Create new** (Utwórz nowy) i dodaj wartości w polach **Name** (Nazwa) i **Description** (Opis). Nie zaznaczaj pola wyboru **Completed** (Zakończono) — w przeciwnym razie nowy element zostanie dodany jako zakończony i nie zostanie wyświetlony na początkowej liście.
   
3. Kliknij przycisk **Create** (Utwórz) — nastąpi przekierowanie z powrotem do widoku **Index** (Indeks), a element zostanie wyświetlony na liście. Możesz także dodać kilka kolejnych elementów do listy rzeczy do zrobienia.

    ![Zrzut ekranu przedstawiający widok Index](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Kliknij pozycję **Edit** (Edytuj) obok **elementu** na liście. Zostanie wyświetlony widok **Edit** (Edycja), w którym można zaktualizować dowolną właściwość obiektu, w tym flagę **Completed** (Zakończono). Po zaznaczeniu flagi **Complete** (Zakończono) i kliknięciu przycisku **Save** (Zapisz) **element** zostanie usunięty z listy niezakończonych zadań.
   
   ![Zrzut ekranu przedstawiający widok indeksu przy użyciu zaznaczone pole ukończone](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Po przetestowaniu aplikacji naciśnij klawisze Ctrl+F5, aby zatrzymać jej debugowanie. Wszystko jest gotowe do wdrożenia.

## <a name="deploy-the-application-to-azure"></a>Krok 7: Wdrażanie aplikacji 
Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować tę aplikację, w **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Publikuj**.
   
2. W oknie dialogowym **Publikowanie** wybierz pozycję **Microsoft Azure App Service**, a następnie wybierz pozycję **Utwórz nowy**, aby utworzyć profil usługi App Service, lub pozycję **Wybierz istniejący**, aby skorzystać z istniejącego profilu.

3. Jeśli masz już profil usługi Azure App Service, wybierz subskrypcję z listy rozwijanej **Subskrypcja**. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu. Następnie wyszukaj wymaganą usługę Azure App Service i wybierz przycisk **OK**. 
   
   ![Okno dialogowe usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Aby utworzyć nowy profil usługi Azure App Service, kliknij pozycję **Utwórz nowy** w oknie dialogowym **Publikowanie**. W oknie dialogowym **Tworzenie usługi App Service** wprowadź nazwę aplikacji internetowej oraz odpowiednią subskrypcję, grupę zasobów i plan usługi App Service, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Tworzenie usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

W ciągu kilku sekund program Visual Studio zakończy publikowanie aplikacji internetowej i uruchomi przeglądarkę, w której będzie można zobaczyć swój projekt działający na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku wyjaśniono sposób tworzenia aplikacji sieci web, które mają dostęp do danych przechowywanych w usłudze Azure Cosmos DB platformy ASP.NET MVC. Teraz możesz przejść do następnego artykułu:

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Java umożliwiającej dostęp do danych przechowywanych na koncie interfejsu API SQL usługi Azure Cosmos DB]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
