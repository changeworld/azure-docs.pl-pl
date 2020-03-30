---
title: Tworzenie pierwszej aplikacji bezserwerowe w programie Visual Studio
description: Tworzenie, wdrażanie i zarządzanie aplikacją bez użycia serwera przy użyciu aplikacji Azure Logic Apps i usługi Azure Functions w programie Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981164"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Tworzenie pierwszej aplikacji bez użycia serwera przy użyciu aplikacji Azure Logic Apps i usługi Azure Functions w programie Visual Studio

Aplikacje w chmurze można szybko tworzyć i wdrażać przy użyciu narzędzi i możliwości bezserwerowych na platformie Azure, takich jak [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i usługi Azure [Functions.](../azure-functions/functions-overview.md) W tym artykule pokazano, jak rozpocząć tworzenie aplikacji bezserwerowej, która używa aplikacji logiki, która wywołuje funkcję platformy Azure w programie Visual Studio. Aby dowiedzieć się więcej o rozwiązaniach bezserwerowych na platformie Azure, zobacz [Azure Serverless z funkcjami i aplikacjami logiki](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć aplikację bezserwerową w programie Visual Studio, potrzebujesz:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Następujące narzędzia. Jeśli jeszcze ich nie masz, pobierz je i zainstaluj.

  * [Visual Studio 2019, 2017 lub 2015 (wersja wspólnotowa lub inna)](https://aka.ms/download-visual-studio). 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Podczas instalowania programu Visual Studio 2019 lub 2017 upewnij się, że wybierzesz obciążenie **deweloperskie platformy Azure.**

  * [Zestaw Microsoft Azure SDK dla platformy .NET (wersja 2.9.1 lub nowsza)](https://azure.microsoft.com/downloads/). 
  Dowiedz się więcej na temat [zestawu Azure SDK dla platformy .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Program Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Narzędzia azure logiki aplikacji dla wersji programu Visual Studio, które chcesz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

  * [Podstawowe narzędzia azure functions dla](https://www.npmjs.com/package/azure-functions-core-tools) funkcji lokalnego debugowania.

* Dostęp do sieci Web podczas korzystania z osadzonego projektanta aplikacji logiki.

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

## <a name="create-a-resource-group-project"></a>Tworzenie projektu grupy zasobów

Aby rozpocząć, utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dla aplikacji bezserwerowej. Na platformie Azure tworzysz zasoby w *ramach grupy zasobów*, która jest logiczną kolekcją używaną do organizowania, zarządzania i wdrażania zasobów dla całej aplikacji jako pojedynczego zasobu. W przypadku aplikacji bezserwerowej na platformie Azure grupa zasobów zawiera zasoby zarówno dla usługi Azure Logic Apps, jak i usługi Azure Functions. Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Uruchom program Visual Studio i zaloguj się przy użyciu konta platformy Azure.

1. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**.

   ![Tworzenie nowego projektu w programie Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Następnie wybierz pozycję **Cloud** > **Azure Resource Group**.

   > [!NOTE]
   > Jeśli kategoria **chmura** lub projekt **grupy zasobów platformy Azure** nie istnieje, upewnij się, że zainstalowano zestaw SDK platformy Azure dla programu Visual Studio.

   Jeśli używasz programu Visual Studio 2019, wykonaj następujące kroki:

   1. W polu **Utwórz nowy projekt** wybierz szablon projektu **grupy zasobów platformy Azure** dla języka Visual C# lub Visual Basic, a następnie wybierz pozycję **Dalej**.

   1. Podaj nazwę i inne informacje o projekcie, które mają być używane dla grupy zasobów platformy Azure. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Nadaj projektowi nazwę i lokalizację, a następnie wybierz przycisk **OK**.

   Program Visual Studio monituje o wybranie szablonu z listy szablonów. 
   W tym przykładzie użyto szablonu Szybki start platformy Azure, dzięki czemu można utworzyć aplikację bezserwerową, która zawiera aplikację logiki i wywołanie funkcji platformy Azure.

   > [!TIP]
   > W scenariuszach, w których nie chcesz wstępnie wdrożyć rozwiązania do grupy zasobów platformy Azure, można użyć pustego szablonu **aplikacji logiki,** który po prostu tworzy pustą aplikację logiki.

1. Na liście **Pokaż szablony z tej lokalizacji** wybierz pozycję Szybki start platformy Azure **(github.com/Azure/azure-quickstart-templates)**.

1. W polu wyszukiwania wpisz "logic-app" jako filtr. Z wyników wybierz szablon **101-logic-app-and-function-app.**

   ![Wybieranie szablonu szybkiego startu platformy Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio tworzy i otwiera rozwiązanie dla projektu grupy zasobów. 
   Wybrany szablon Szybki start platformy Azure tworzy szablon wdrożenia o nazwie azuredeploy.json wewnątrz projektu grupy zasobów. Ten szablon wdrożenia zawiera definicję prostej aplikacji logiki, która jest wyzwalana przez żądanie HTTP, wywołuje funkcję platformy Azure i zwraca wynik jako odpowiedź HTTP.

   ![Nowe rozwiązanie bezserwerowe](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Następnie wdrożyć rozwiązanie na platformie Azure. Należy to zrobić, zanim będzie można otworzyć szablon wdrożenia i przejrzeć zasoby dla aplikacji bezserwerowej.

## <a name="deploy-your-solution"></a>Wdrażanie rozwiązania

Aby można było otworzyć aplikację logiki w logice App Designer w programie Visual Studio, musisz mieć grupę zasobów platformy Azure, która jest już wdrożona na platformie Azure. Projektant może następnie utworzyć połączenia z zasobami i usługami w aplikacji logiki. W tym zadaniu wykonaj następujące kroki, aby wdrożyć rozwiązanie z programu Visual Studio w witrynie Azure portal:

1. W Eksploratorze rozwiązań z menu skrótów projektu zasobu wybierz polecenie **Wdrażanie** > **nowego**.

   ![Tworzenie nowego wdrożenia dla grupy zasobów](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Jeśli nie są jeszcze zaznaczone, wybierz subskrypcję platformy Azure i grupę zasobów, do której chcesz wdrożyć. Następnie wybierz pozycję **Wdrażanie**.

   ![Ustawienia wdrożenia](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Jeśli zostanie wyświetleń pole **Edytuj parametry,** podaj nazwy zasobów do użycia dla aplikacji logiki i aplikacji funkcji platformy Azure podczas wdrażania, a następnie zapisz ustawienia. Upewnij się, że używasz globalnie unikatowej nazwy aplikacji funkcji.

   ![Podaj nazwy aplikacji logiki i aplikacji funkcji](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Po uruchomieniu wdrożenia programu Visual Studio do określonej grupy zasobów stan wdrożenia rozwiązania pojawia się w oknie **Dane wyjściowe** programu Visual Studio. 
   Po zakończeniu wdrażania aplikacja logiki jest dostępna w witrynie Azure portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Edytowanie aplikacji logiki w programie Visual Studio

Aby edytować aplikację logiki po wdrożeniu, otwórz aplikację logiki przy użyciu projektanta aplikacji logiki w programie Visual Studio.

1. W Eksploratorze rozwiązań z menu skrótów pliku azuredeploy.json wybierz pozycję **Otwórz za pomocą projektanta aplikacji logiki**.

   ![Otwórz plik azuredeploy.json w projektancie aplikacji logiki](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

1. Po wyświetleniu pola **Właściwości aplikacji logiki** w obszarze **Subskrypcja**wybierz subskrypcję platformy Azure, jeśli nie jest jeszcze zaznaczona. W obszarze **Grupa zasobów**wybierz grupę zasobów i lokalizację, w której wdrożono rozwiązanie, a następnie wybierz przycisk **OK**.

   ![Właściwości aplikacji logiki](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otwarciu projektanta aplikacji logiki można kontynuować dodawanie kroków lub zmieniać przepływ pracy i zapisywać aktualizacje.

   ![Otwarta aplikacja logiki w logic app designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

Aby utworzyć projekt i funkcję funkcji funkcji przy użyciu języka JavaScript, Python, F#, PowerShell, Batch lub Bash, wykonaj kroki opisane w [programie Work with Azure Functions Core Tools](../azure-functions/functions-run-local.md). Aby opracować funkcję platformy Azure przy użyciu języka C# wewnątrz rozwiązania, należy użyć biblioteki klas języka C#, wykonując kroki opisane w [publish a .NET class library as a Function App](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Wdrażanie funkcji w programie Visual Studio

Szablon wdrażania wdraża wszystkie funkcje platformy Azure, które masz w rozwiązaniu z repozytorium Git, które jest określone przez zmienne w pliku azuredeploy.json. Jeśli utworzysz i autor projektu funkcji w rozwiązaniu, można sprawdzić, że projekt do kontroli źródła Git (na przykład GitHub lub Azure DevOps), a następnie zaktualizować zmienną, `repo` tak aby szablon wdraża funkcję platformy Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Zarządzanie aplikacjami logiki i historia uruchamiania widoku

W przypadku aplikacji logiki już wdrożonych na platformie Azure nadal można edytować, zarządzać, wyświetlać historię uruchamiania i wyłączać te aplikacje w programie Visual Studio.

1. Z menu **Widok** w programie Visual Studio otwórz **Cloud Explorer**.

1. W obszarze **Wszystkie subskrypcje**wybierz subskrypcję platformy Azure skojarzoną z aplikacjami logiki, którymi chcesz zarządzać, a następnie wybierz pozycję **Zastosuj**.

1. W obszarze **Aplikacje logiki**wybierz aplikację logiki. W menu skrótów tej aplikacji wybierz pozycję **Otwórz za pomocą edytora aplikacji Logika**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

Teraz można pobrać już opublikowaną aplikację logiki do projektu grupy zasobów. Tak, mimo że można było uruchomić aplikację logiki w witrynie Azure portal, nadal można importować i zarządzać tą aplikacją w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami logiki za pomocą programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md)
