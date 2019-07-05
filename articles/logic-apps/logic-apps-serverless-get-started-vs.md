---
title: Twórz aplikacje bezserwerowe za pomocą usługi Azure Logic Apps i Azure Functions w programie Visual Studio
description: Tworzenie, wdrażanie i zarządzać swoją pierwszą aplikację bez użycia serwera za pomocą usługi Azure Logic Apps i Azure Functions w programie Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444871"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Utwórz swoją pierwszą aplikację bez użycia serwera za pomocą usługi Azure Logic Apps i Azure Functions w programie Visual Studio

Możesz szybko tworzyć i wdrażać aplikacje w chmurze przy użyciu narzędzia niewymagające użycia serwera i funkcji na platformie Azure, takich jak [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [usługi Azure Functions](../azure-functions/functions-overview.md). W tym artykule pokazano, jak rozpocząć Kompilowanie aplikacji bez użycia serwera, który korzysta z aplikacji logiki, która wywołuje funkcję platformy Azure w programie Visual Studio. Aby dowiedzieć się więcej o rozwiązaniach nieużywających serwera na platformie Azure, zobacz [platformy Azure bez użycia serwera za pomocą Functions i Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Tworzenie aplikacji bezserwerowej w programie Visual Studio, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Następujące narzędzia. Jeśli ich nie masz jeszcze, Pobierz i zainstaluj je.

  * [Visual Studio 2019 r, 2017 lub 2015 r. (Community lub innej wersji)](https://aka.ms/download-visual-studio). 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Po zainstalowaniu programu Visual Studio 2019 lub 2017, upewnij się, że wybrano **programowanie na platformie Azure** obciążenia.

  * [Zestaw Microsoft Azure SDK dla platformy .NET (wersja 2.9.1 lub nowszy)](https://azure.microsoft.com/downloads/). 
  Dowiedz się więcej na temat [zestawu Azure SDK dla platformy .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * Zainstalowanie programu [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Narzędzia systemu Azure aplikacje logiki dla wersji programu Visual Studio, które mają:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

  * [Podstawowe narzędzia usługi Azure Functions](https://www.npmjs.com/package/azure-functions-core-tools) lokalne debugowanie funkcji.

* Dostęp do Internetu podczas korzystania z osadzonego projektanta aplikacji logiki.

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

## <a name="create-a-resource-group-project"></a>Tworzenie projektu grupy zasobów

Aby rozpocząć, Utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) aplikacji bez użycia serwera. Na platformie Azure, tworzyć zasoby w ramach *grupy zasobów*, czyli logicznej kolekcji, użyj do organizowania, zarządzania i wdrażania zasobów dla całej aplikacji jako pojedynczy zasób. Dla aplikacji bez użycia serwera na platformie Azure grupa zasobów zawiera zasoby dla usługi Azure Functions i Azure Logic Apps. Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).

1. Uruchom program Visual Studio i zaloguj się przy użyciu konta platformy Azure.

1. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**.

   ![Utwórz nowy projekt w programie Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Następnie wybierz **chmury** > **grupy zasobów platformy Azure**.

   > [!NOTE]
   > Jeśli **chmury** kategorii lub **grupy zasobów platformy Azure** projektu nie istnieje, upewnij się, że zainstalowano zestaw Azure SDK dla programu Visual Studio.

   Jeśli używasz programu Visual Studio 2019 r, wykonaj następujące czynności:

   1. W **Utwórz nowy projekt** wybierz opcję **grupy zasobów platformy Azure** szablon projektu służący do wizualizacji albo C# lub Visual Basic, a następnie wybierz **dalej**.

   1. Podaj nazwę i innych informacji o projekcie, którego chcesz użyć dla grupy zasobów platformy Azure. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Nadaj projektowi nazwę i lokalizację, a następnie wybierz **OK**.

   Program Visual Studio wyświetli monit o wybierz szablon z listy szablonów. 
   W tym przykładzie użyto szablonu szybkiego startu platformy Azure, dzięki czemu możesz tworzyć aplikacji bez użycia serwera, który zawiera aplikację logiki i wywołanie funkcji platformy Azure.

   > [!TIP]
   > W scenariuszach, w których nie chcesz do wstępnego wdrażania rozwiązania w grupie zasobów platformy Azure, można użyć pustego **aplikacji logiki** szablonu, który po prostu tworzy pustej aplikacji logiki.

1. Z **Pokaż szablony z tej lokalizacji** listy wybierz **szybkiego startu platformy Azure (github.com/Azure/azure-quickstart-templates)** .

1. W polu wyszukiwania wprowadź "aplikacja logiki" jako filtr. Wybierz z wyników, **101-logic-app-and-function-app** szablonu.

   ![Wybierz szablon szybkiego startu platformy Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Program Visual Studio tworzy i otwiera rozwiązanie dla Twojego projektu grupy zasobów. 
   Wybrany szablon szybkiego startu platformy Azure tworzy szablon wdrożenia o nazwie azuredeploy.json wewnątrz projekt grupy zasobów. Szablon wdrożenia zawiera definicję to prosta aplikacja logiki jest wyzwalana przez żądanie HTTP, który wywołuje funkcję platformy Azure i zwraca wynik w postaci odpowiedź HTTP.

   ![Nowe rozwiązanie niewymagające użycia serwera](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Następnie można wdrożyć swoje rozwiązanie na platformie Azure. Należy to zrobić, aby można było otworzyć szablon wdrażania i przejrzyj zasoby dla aplikacji bez użycia serwera.

## <a name="deploy-your-solution"></a>Zacznij wdrażać swoje rozwiązanie

Zanim będzie można otworzyć aplikację logiki w Projektancie aplikacji logiki w programie Visual Studio, musi mieć grupę zasobów platformy Azure, która została już wdrożona na platformie Azure. Projektant następnie można utworzyć połączenia z zasobami i usługami w aplikacji logiki. Dla tego zadania wykonaj następujące kroki, aby wdrożyć swoje rozwiązanie w programie Visual Studio do witryny Azure portal:

1. W oknie Solution Explorer z menu skrótów projektu zasobów, wybierz **Wdróż** > **New**.

   ![Utwórz nowe wdrożenie dla grupy zasobów](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Jeśli jeszcze nie jest wybrana, wybierz swoją subskrypcję platformy Azure i grupę zasobów, do której chcesz wdrożyć. Następnie wybierz **Wdróż**.

   ![Ustawienia wdrożenia](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Jeśli **Edytuj parametry** pojawi się okno, podaj nazwy zasobów do użycia w aplikacji logiki i aplikacji funkcji platformy Azure we wdrożeniu, a następnie Zapisz ustawienia. Upewnij się, że używasz globalnie unikatową nazwę aplikacji funkcji.

   ![Podaj nazwy dla aplikacji logiki i aplikacji funkcji](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Po uruchomieniu programu Visual Studio wdrożenia w grupie zasobów określonej stan wdrożenia tego rozwiązania jest wyświetlany w programie Visual Studio **dane wyjściowe** okna. 
   Po zakończeniu wdrożenia aplikacja logiki jest aktywna w witrynie Azure portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Edytuj aplikację logiki w programie Visual Studio

Aby edytować aplikację logiki po wdrożeniu, należy otworzyć aplikację logiki przy użyciu projektanta aplikacji logiki w programie Visual Studio.

1. W Eksploratorze rozwiązań, przejdź do menu skrótów w pliku azuredeploy.json wybierz **Otwórz za pomocą projektanta aplikacji logiki**.

   ![Otwórz azuredeploy.json w Projektancie aplikacji logiki](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019 r, sprawdź, czy najnowsze aktualizacje dla programu Visual Studio.

1. Po **właściwości aplikacji logiki** pojawi się okno, w obszarze **subskrypcji**, wybierz swoją subskrypcję platformy Azure, jeśli nie została jeszcze wybrana. W obszarze **grupy zasobów**, wybierz grupę zasobów i lokalizacji, w którym wdrożono rozwiązanie, a następnie wybierz **OK**.

   ![Właściwości aplikacji logiki](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otwarciu Projektant aplikacji logiki możesz kontynuować dodawanie kroków lub zmiana przepływu pracy i zapisać aktualizacje.

   ![Aplikacja logiki otwarty w Projektancie aplikacji logiki](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Utwórz projekt usługi Azure Functions

Do tworzenia projektu funkcji i funkcji przy użyciu języka JavaScript, Python, F#, programu PowerShell, usługi Batch lub powłoki Bash, wykonaj kroki opisane w [pracy przy użyciu podstawowych narzędzi usługi Azure Functions](../azure-functions/functions-run-local.md). Tworzenie funkcji platformy Azure przy użyciu C# wewnątrz rozwiązania, należy użyć C# biblioteki klas, wykonując kroki opisane w [publikowania bibliotekę klas platformy .NET, co aplikacja funkcji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Wdrażanie funkcji w programie Visual Studio

Szablon wdrożenia służy do wdrażania usługi Azure functions, które mają w swoim rozwiązaniu z repozytorium Git, który jest określony przez zmienne w pliku azuredeploy.json. Jeśli utworzysz i tworzenie projektu usługi Functions w swoim rozwiązaniu można sprawdzić ten projekt do kontroli źródła Git (na przykład, GitHub lub DevOps platformy Azure) i następnie zaktualizuj `repo` zmiennej, tak aby ten szablon służy do wdrażania funkcji platformy Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Zarządzanie aplikacjami logiki i Widok historii uruchamiania

Dla usługi logic apps już wdrożonych na platformie Azure można nadal edytować, zarządzanie, wyświetlić historię uruchomień i wyłączyć te aplikacje z programu Visual Studio.

1. Z **widoku** w programie Visual Studio, otwórz menu **programu Cloud Explorer**.

1. W obszarze **wszystkie subskrypcje**, wybierz subskrypcję platformy Azure skojarzone z usługą logic apps, które chcesz zarządzać, a następnie wybierz **Zastosuj**.

1. W obszarze **Logic Apps**, wybierz swoją aplikację logiki. Wybierz z menu skrótów dla tej aplikacji, **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019 r, sprawdź, czy najnowsze aktualizacje dla programu Visual Studio.

Możesz teraz pobrać aplikacji logiki już opublikowane w projekcie grupy zasobów. Tak mimo że być może rozpoczęto aplikacji logiki w witrynie Azure portal, można nadal importować i zarządzanie tej aplikacji w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie aplikacjami logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md)
