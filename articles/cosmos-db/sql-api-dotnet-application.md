---
title: ASP.NET samouczek aplikacji sieci Web Core MVC przy użyciu usługi Azure Cosmos DB
description: ASP.NET samouczka Core MVC, aby utworzyć aplikację sieci web MVC przy użyciu usługi Azure Cosmos DB. Będziesz przechowywać JSON i uzyskać dostęp do danych z aplikacji do wykonania hostowane w usłudze Azure App Service — ASP NET Core MVC samouczek krok po kroku.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274073"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Samouczek: Opracowanie ASP.NET podstawowej aplikacji sieci Web MVC za pomocą usługi Azure Cosmos DB przy użyciu zestawu .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji ASP.NET MVC hostowanej na platformie Azure. W tym samouczku należy użyć pliku .NET SDK V3. Na poniższej ilustracji przedstawiono stronę sieci Web, która zostanie zbudowana przy użyciu przykładu w tym artykule:

![Zrzut ekranu przedstawiający listę todo aplikacji internetowej MVC utworzonej przez ten samouczek - Samouczek ASP NET Core MVC krok po kroku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Jeśli nie masz czasu na pracę z tym samouczkiem, możesz pobrać kompletny przykładowy projekt z witryny [GitHub][GitHub].

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie konta usługi Azure Cosmos
> * Tworzenie aplikacji ASP.NET Core MVC
> * Łączenie aplikacji z usługą Azure Cosmos DB
> * Wykonywanie operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych

> [!TIP]
> W tym samouczku założono, że masz wcześniejsze doświadczenie przy użyciu ASP.NET Core MVC i usługi Azure App Service. Jeśli jesteś nowy, aby ASP.NET Core lub [narzędzia wymagań wstępnych,](#prerequisites)zaleca się pobrać kompletny przykładowy projekt z [GitHub,][GitHub]dodać wymagane pakiety NuGet i uruchomić go. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne

Przed postępem w tym artykule należy wykonać następujące czynności:

* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Wszystkie zrzuty ekranu w tym artykule pochodzą z programu Microsoft Visual Studio Community 2019. Jeśli używasz innej wersji, ekrany i opcje mogą nie być całkowicie dopasowane. Rozwiązanie powinno działać, jeśli spełniasz wymagania wstępne.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos

Zacznijmy od utworzenia konta usługi Azure Cosmos. Jeśli masz już konto interfejsu API usługi Azure Cosmos DB SQL lub jeśli używasz emulatora usługi Azure Cosmos DB, przejdź do [kroku 2: Utwórz nową ASP.NET aplikacji MVC.](#create-a-new-mvc-application)

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

W następnej sekcji utworzysz nową ASP.NET podstawową aplikację MVC.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Krok 2: Tworzenie nowej aplikacji ASP.NET Core MVC

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W **obszarze Tworzenie nowego projektu**znajdź i wybierz ASP.NET **podstawową aplikację sieci Web** dla języka C#. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Tworzenie nowego projektu aplikacji sieci Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. W **obszarze Konfiguruj nowy projekt**nazwij projekt *todo* i wybierz pozycję **Utwórz**.

1. W **obszarze Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web**wybierz pozycję Aplikacja sieci Web **(Model-View-Controller)**. Wybierz **pozycję Utwórz,** aby kontynuować.

   Visual Studio tworzy pustą aplikację MVC.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** lub F5, aby uruchomić aplikację ASP.NET lokalnie.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Krok 3. Dodawanie pakietu NuGet usługi Azure Cosmos DB do projektu

Teraz, gdy mamy większość ASP.NET core kod framework MVC, które są potrzebne dla tego rozwiązania, dodajmy pakiety NuGet wymagane do połączenia z usługą Azure Cosmos DB.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wyszukaj i wybierz pozycję **Microsoft.Azure.Cosmos**. Wybierz pozycję **Zainstaluj**.

   ![Instalowanie pakietu NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio pobiera i instaluje pakiet usługi Azure Cosmos DB i jego zależności.

   Można również użyć **Konsoli Menedżera pakietów,** aby zainstalować pakiet NuGet. W tym celu wybierz pozycję **Tools** > **NuGet Package Manager** > **Package Manager Console**. W wierszu polecenia wpisz następujące polecenie:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Krok 4: Konfigurowanie aplikacji ASP.NET Core MVC

Teraz dodajmy modele, widoki i kontrolery do tej aplikacji MVC.

### <a name="add-a-model"></a><a name="add-a-model"></a> Dodawanie modelu

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder **Modele,** wybierz polecenie **Dodaj** > **klasę**.

1. W **obszarze Dodaj nowy element**nazwij nową klasę *Item.cs* i wybierz pozycję **Dodaj**.

1. Zastąp zawartość *Item.cs* klasy następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Usługa Azure Cosmos DB używa JSON do przenoszenia i przechowywania danych. Za pomocą `JsonProperty` atrybutu można kontrolować sposób serializacji i deserializacji obiektów przez JSON. Klasa `Item` demonstruje `JsonProperty` atrybut. Ten kod steruje formatem nazwy właściwości, która przechodzi do JSON. Zmienia również nazwę właściwości `Completed`.NET .

### <a name="add-views"></a><a name="add-views"></a>Dodawanie widoków

Następnie utwórzmy następujące trzy widoki.

* Dodawanie widoku elementu listy
* Dodawanie widoku nowego elementu
* Dodawanie widoku edycji elementu

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>Dodawanie widoku elementu listy

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder **Widoki** i wybierz polecenie **Dodaj** > **nowy folder**. Nazwij folder *Element*.

1. Kliknij prawym przyciskiem myszy pusty **folder Element,** a następnie wybierz polecenie **Dodaj** > **widok**.

1. W **polu Dodaj widok MVC**podaj następujące wartości:

   * W **widoku wpisz** *Index*.
   * W **obszarze Szablon**wybierz pozycję **Lista**.
   * W **klasie Model**wybierz pozycję Element **(todo. modeli)**.
   * Wybierz **pozycję Użyj strony układu** i wprowadź pozycję *~/Views/Shared/_Layout.cshtml*.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie widoku MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Po dodaniu tych wartości wybierz przycisk **Dodaj**. Program Visual Studio utworzy nowy widok szablonu.

Po zakończeniu programu Visual Studio otwiera plik *cshtml,* który tworzy. Można zamknąć ten plik w programie Visual Studio. Wrócimy do niego później.

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>Dodawanie widoku nowego elementu

Podobnie jak w przypadku widoku umożliwiającego wyświetlanie elementów utwórz widok, który pozwala tworzyć elementy. W tym celu wykonaj następujące kroki:

1. W **Eksploratorze rozwiązań**ponownie kliknij prawym przyciskiem myszy folder **Element,** wybierz polecenie **Dodaj** > **widok**.

1. W **add MVC View**należy wprowadzić następujące zmiany:

   * W **pliku View name**wprowadź pozycję *Utwórz*.
   * W **obszarze Szablon**wybierz pozycję **Utwórz**.
   * W **klasie Model**wybierz pozycję Element **(todo. modeli)**.
   * Wybierz **pozycję Użyj strony układu** i wprowadź pozycję *~/Views/Shared/_Layout.cshtml*.
   * Wybierz pozycję **Dodaj**.

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Dodawanie widoku edycji elementu

Na koniec wykonaj następujące kroki, aby dodać widok umożliwiający edycję elementu:

1. W **Eksploratorze rozwiązań**ponownie kliknij prawym przyciskiem myszy folder **Element,** wybierz polecenie **Dodaj** > **widok**.

1. W **add MVC View**należy wprowadzić następujące zmiany:

   * W polu **Nazwa widoku** wpisz *Edit* (Edycja).
   * W polu **Szablon** wybierz pozycję **Edycja**.
   * W polu **Klasa modelu** wybierz pozycję **Item (todo.Models)**.
   * Wybierz **pozycję Użyj strony układu** i wprowadź pozycję *~/Views/Shared/_Layout.cshtml*.
   * Wybierz pozycję **Dodaj**.

Po wykonaniu tych kroków zamknij wszystkie dokumenty *cshtml* w programie Visual Studio, gdy powrócisz do tych widoków później.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Deklarowanie i inicjowanie usług

Najpierw dodamy klasę, która zawiera logikę, aby połączyć się z usługą Azure Cosmos DB i używać jej. W tym samouczku hermetyzujemy tę logikę `CosmosDBService` w klasę `ICosmosDBService`o nazwie i interfejs o nazwie . Ta usługa wykonuje operacje CRUD. Zajmuje się również odczytywaniem operacji pliku danych, takich jak wystawianie niekompletnych elementów, tworzenie, edytowanie i usuwanie elementów.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj** > **nowy folder**. Nazwij folder *Usługi*.

1. Kliknij prawym przyciskiem myszy folder **Usługi,** a następnie wybierz polecenie **Dodaj** > **klasę**. Nazwij nową klasę *CosmosDBService* i wybierz pozycję **Dodaj**.

1. Zastąp zawartość *CosmosDBService.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Kliknij prawym przyciskiem myszy folder **Usługi,** a następnie wybierz polecenie **Dodaj** > **klasę**. Nazwij nową klasę *ICosmosDBService* i wybierz pozycję **Dodaj**.

1. Dodaj następujący kod do klasy *ICosmosDBService:*

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Otwórz plik *Startup.cs* w rozwiązaniu i `ConfigureServices` zastąp metodę na:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    Kod w tym kroku inicjuje klienta na podstawie konfiguracji jako pojedynczego wystąpienia, które mają być wstrzykiwane przez [iniekcję zależności w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. W tym samym pliku dodaj następującą metodę **InitializeCosmosClientInstanceAsync**, która odczytuje konfigurację i inicjuje klienta.

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Zdefiniuj konfigurację w pliku *appsettings.json* projektu, jak pokazano w następującym urywek:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Dodawanie kontrolera

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder **Kontrolery,** wybierz polecenie **Dodaj** > **kontroler**.

1. W **obszarze Dodaj rusztowanie**wybierz **kontroler MVC — opróżnij** i wybierz pozycję **Dodaj**.

   ![Wybierz kontroler MVC — puste w dodatku rusztowania](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nazwij nowy kontroler *ItemController*.

1. Zastąp zawartość *ItemController.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Twoje widoki powinny działać z tym tokenem anty-fałszerstwa, jak również. Aby uzyskać więcej informacji i przykładów, zobacz [Zapobieganie atakom międzyustniczowym (CSRF) w ASP.NET aplikacji MVC][Preventing Cross-Site Request Forgery]. Kod źródłowy dostępny w usłudze [GitHub][GitHub] zawiera pełną implementację.

Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby uzyskać więcej informacji, zobacz [Samouczek: Implementowanie funkcji CRUD z entity framework w ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Krok 5: Uruchom aplikację lokalnie

Aby przetestować aplikację na komputerze lokalnym, należy wykonać następujące czynności:

1. Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:

   ![Zrzut ekranu przedstawiający aplikację internetową listy todo stworzoną przez ten samouczek](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Jeśli aplikacja zostanie otwarta na stronie głównej, dołącz `/Item` do adresu URL.

1. Zaznacz **łącze Utwórz nowy** i dodaj wartości do pól **Nazwa** i **Opis.** Pole wyboru **Zakończone** nie należy zaznaczyć. Jeśli go wybierzesz, aplikacja doda nowy element w stanie ukończonym. Element nie jest już wyświetlany na liście początkowej.

1. Wybierz **pozycję Utwórz**. Aplikacja wysyła cię z powrotem do widoku **indeksu,** a element pojawi się na liście. Możesz dodać jeszcze kilka elementów do listy **zadań do wykonania.**

    ![Zrzut ekranu przedstawiający widok indeksu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Wybierz **pozycję Edytuj** obok **elementu** na liście. Aplikacja otwiera widok **Edytuj,** w którym można zaktualizować dowolną właściwość obiektu, w tym flagę **Zakończone.** Jeśli **wybierzesz opcję Zakończ** i **wybierześ zapisz,** aplikacja wyświetli element jako **ukończony** na liście.

   ![Zrzut ekranu przedstawiający widok indeksu z zaznaczonym polem Zakończone](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Sprawdź stan danych w usłudze Azure Cosmos DB przy użyciu [Eksploratora usługi Cosmos](https://cosmos.azure.com) lub Eksploratora danych usługi Azure Cosmos DB.

1. Po przetestowaniu aplikacji wybierz ctrl+F5, aby zatrzymać debugowanie aplikacji. Wszystko jest gotowe do wdrożenia.

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Krok 6: Wdrażanie aplikacji

Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować tę aplikację, kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Publikuj**.

1. W **obszarze Wybierz cel publikowania**wybierz pozycję App **Service**.

1. Aby użyć istniejącego profilu usługi App Service, wybierz **pozycję Wybierz istniejący,** a następnie wybierz pozycję **Publikuj**.

1. W **usłudze app service**wybierz **subskrypcję**. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu.

1. Znajdź swój profil, a następnie wybierz **przycisk OK**. Następnie wyszukaj wymaganą usługę Azure App Service i wybierz przycisk **OK**.

   ![Okno dialogowe usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Inną opcją jest utworzenie nowego profilu:

1. Podobnie jak w poprzedniej procedurze, kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.
  
1. W **obszarze Wybierz cel publikowania**wybierz pozycję App **Service**.

1. W **obszarze Wybierz cel publikowania**wybierz pozycję **Utwórz nowy** i wybierz pozycję **Publikuj**.

1. W **usłudze App Service**wprowadź nazwę aplikacji sieci Web oraz odpowiednią subskrypcję, grupę zasobów i plan hostingu, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Tworzenie usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

W ciągu kilku sekund program Visual Studio publikuje aplikację sieci web i uruchamia przeglądarkę, w której można wyświetlić projekt uruchomiony na platformie Azure!

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć ASP.NET podstawowej aplikacji sieci web MVC. Aplikacja może uzyskać dostęp do danych przechowywanych w usłudze Azure Cosmos DB. Teraz możesz kontynuować następujące zasoby:

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](./partitioning-overview.md)
* [Wprowadzenie do zapytań SQL](./how-to-sql-query.md)
* [Jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu wziętego z życia](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
