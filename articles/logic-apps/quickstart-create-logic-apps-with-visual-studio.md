---
title: Automatyzacja przepływów pracy zadań w programie Visual Studio
description: Tworzenie, planowanie i uruchamianie cyklicznych przepływów pracy dla integracji z przedsiębiorstwem przy użyciu aplikacji Azure Logic Apps i programu Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 693b2fd8ac7440b67f53de0aedb9a8268a90de76
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241654"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Szybki start: tworzenie zautomatyzowanych zadań, procesów i przepływów pracy za pomocą aplikacji Azure Logic Apps — Visual Studio

Za pomocą usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i programu Visual Studio można utworzyć przepływy pracy do automatyzacji zadań i procesów, które integrują aplikacje, dane, systemy i usługi w przedsiębiorstwach i organizacjach. Ten przewodnik Szybki start pokazuje, jak projektować i tworzyć te przepływy pracy, tworząc aplikacje logiki w programie Visual Studio i wdrażając te aplikacje na platformie Azure. Chociaż można wykonywać te zadania w witrynie Azure portal, visual studio umożliwia dodawanie aplikacji logiki do kontroli źródła, publikowanie różnych wersji i tworzenie szablonów usługi Azure Resource Manager dla różnych środowisk wdrażania.

Jeśli jesteś nowym użytkownikiem usługi Azure Logic Apps i potrzebujesz tylko podstawowych pojęć, wypróbuj [przewodnik Szybki start do tworzenia aplikacji logiki w witrynie Azure portal.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Projektant aplikacji logiki działa podobnie w witrynie Azure portal i visual studio.

W tym przewodniku Szybki start utworzysz tę samą aplikację logiki za pomocą programu Visual Studio, co przewodnik Szybki start w portalu Azure. Ta aplikacja logiki monitoruje kanał RSS witryny i wysyła wiadomości e-mail dla każdego nowego elementu w tym kanale. Twoja ukończona aplikacja logiki wygląda jak ten przepływ pracy wysokiego poziomu:

![Ukończona aplikacja logiki](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio 2019, 2017 lub 2015 — wersja społecznościowa lub większa](https://aka.ms/download-visual-studio). Ten przewodnik Szybki start korzysta ze społeczności programu Visual Studio 2017.

    > [!IMPORTANT]
    > Podczas instalowania programu Visual Studio 2019 lub 2017 upewnij się, że wybierzesz obciążenie **deweloperskie platformy Azure.**

  * [Zestaw Microsoft Azure SDK dla platformy .NET (2.9.1 lub nowszych)](https://azure.microsoft.com/downloads/). Dowiedz się więcej na temat [zestawu Azure SDK dla platformy .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Najnowsze narzędzia azure logiki aplikacji dla rozszerzenia programu Visual Studio dla wersji, która ma:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do Internetu podczas korzystania z osadzonego Projektanta aplikacji logiki

  Projektant potrzebuje połączenia internetowego do tworzenia zasobów na platformie Azure i odczytywania właściwości i danych z łączników w aplikacji logiki. Na przykład w przypadku połączeń programu Dynamics CRM Online projektant sprawdza wystąpienie programu CRM pod kątem właściwości domyślnych i niestandardowych.

* Konto e-mail obsługiwane przez usługę Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców przejrzyj [listę łączników tutaj](https://docs.microsoft.com/connectors/). W tym przykładzie użyto usługi Office 365 Outlook. Jeśli używasz innego dostawcy, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

Aby rozpocząć, utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Uruchom program Visual Studio. Zaloguj się za pomocą konta platformy Azure.

1. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**. (Klawiatura: Ctrl + Shift + N)

   ![W menu „File” wybierz pozycję „Nowy” > „Projekt”](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Wybierz**grupę zasobów platformy** **Cloud** > Azure . Nazwij projekt, na przykład:

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Nazwy grup zasobów mogą zawierać tylko litery, cyfry,`.`kropki ( ), podkreślenia (`_`), łączniki (`-`i nawiasy (`(`, `)`), ale nie mogą *kończyć się* kropkami (`.`).
   >
   > Jeśli **chmura** lub **grupa zasobów platformy Azure** nie jest wyświetlana, upewnij się, że zainstalowano zestaw SDK platformy Azure dla programu Visual Studio.

   Jeśli używasz programu Visual Studio 2019, wykonaj następujące kroki:

   1. W polu **Utwórz nowy projekt** wybierz projekt **grupy zasobów platformy Azure** dla języka Visual C# lub Visual Basic. Wybierz **pozycję Dalej**.

   1. Podaj nazwę grupy zasobów platformy Azure, której chcesz użyć, i inne informacje o projekcie. Wybierz **pozycję Utwórz**.

1. Z listy szablonów wybierz szablon **aplikacji logiki.** Kliknij przycisk **OK**.

   ![Wybieranie szablonu aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Po utworzeniu projektu w programie Visual Studio Eksplorator rozwiązań otwiera i pokazuje rozwiązanie. W rozwiązaniu plik **LogicApp.json** nie tylko przechowuje definicję aplikacji logiki, ale jest również szablonem usługi Azure Resource Manager, którego można użyć do wdrożenia.

   ![Eksplorator rozwiązań pokazuje nowe rozwiązanie aplikacji logiki i pliku wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

Jeśli masz swój projekt grupy zasobów platformy Azure, utwórz aplikację logiki za pomocą szablonu **pustej aplikacji logiki.**

1. W Eksploratorze rozwiązań otwórz menu skrótów pliku **LogicApp.json.** Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwieranie pliku JSON aplikacji logiki za pomocą Projektanta aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   Program Visual Studio monituje o subskrypcję platformy Azure i grupę zasobów platformy Azure do tworzenia i wdrażania zasobów dla aplikacji logiki i połączeń.

1. W przypadku **subskrypcji**wybierz subskrypcję platformy Azure. W przypadku **grupy zasobów**wybierz pozycję **Utwórz nowy,** aby utworzyć inną grupę zasobów platformy Azure.

   ![Wybieranie subskrypcji platformy Azure, grupy zasobów i lokalizacji zasobu](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Ustawienie | Przykładowa wartość | Opis |
   | ------- | ------------- | ----------- |
   | Konto użytkownika | Fabrikam <br> sophia-owen@fabrikam.com | Konto używane podczas logań w programie Visual Studio |
   | **Subskrypcja** | Płatność zgodnie z rzeczywistym użyciem <br> (sophia-owen@fabrikam.com) | Nazwa Twojej subskrypcji platformy Azure i skojarzone konto |
   | **Grupa zasobów** | MyLogicApp-RG <br> (Zachodnie stany USA) | Grupa zasobów platformy Azure i lokalizacja do przechowywania i wdrażania zasobów aplikacji logiki |
   | **Lokalizacja** | **Tak samo jak grupa zasobów** | Typ lokalizacji i określona lokalizacja do wdrażania aplikacji logiki. Typ lokalizacji to region platformy Azure lub istniejące [środowisko usługi integracji (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>W przypadku tego przewodnika Szybki start należy zachować typ lokalizacji ustawiony na **Region** i lokalizację ustawioną na **Taką sam jak grupa zasobów**. <p>**Uwaga:** Po utworzeniu projektu grupy zasobów można [zmienić typ lokalizacji i lokalizację,](manage-logic-apps-with-visual-studio.md#change-location)ale inny typ lokalizacji wpływa na aplikację logiki na różne sposoby. |
   ||||

1. Projektant aplikacji logiki otwiera stronę, która pokazuje wprowadzenie wideo i często używane wyzwalacze. Przewiń w dół obok klipu wideo i wyzwala do **szablonów**i wybierz **pustą aplikację logiki**.

   ![Wybieranie pozycji „Pusta aplikacja logiki”](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Kompilowanie przepływu pracy aplikacji logiki

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS, który jest uruchamiany po wyświetleniu nowego elementu pliku danych. Każda aplikacja logiki rozpoczyna się od wyzwalacza, który jest uruchamiany po spełnieniu określonych kryteriów. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej przepływ pracy.

1. W logice app designer, w polu wyszukiwania, wybierz pozycję **Wszystkie**. W polu wyszukiwania wpisz "rss". Z listy wyzwalaczy wybierz ten wyzwalacz: **Po opublikowaniu elementu pliku danych**

   ![Kompilowanie aplikacji logiki przez dodanie wyzwalacza i akcji](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Po wyświetleniu wyzwalacza w projektancie, zakończ tworzenie aplikacji logiki, wykonując kroki przepływu pracy w [przewodniku Szybki start witryny Azure Portal,](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)a następnie wróć do tego artykułu. Gdy wszystko będzie gotowe, aplikacja logiki będzie wyglądać następująco:

   ![Ukończona aplikacja logiki](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Zapisz rozwiązanie programu Visual Studio. (Klawiatura: Ctrl+S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Wdrażanie aplikacji logiki na platformie Azure

Przed uruchomieniem i przetestowaniem aplikacji logiki należy wdrożyć aplikację na platformie Azure z programu Visual Studio.

1. W Eksploratorze rozwiązań w menu skrótów projektu wybierz polecenie **Wdrażanie** > **nowego**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

   ![Tworzenie wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. W przypadku tego wdrożenia zachowaj domyślną subskrypcję platformy Azure, grupę zasobów i inne ustawienia. Wybierz pozycję **Wdróż**.

   ![Wdrażanie aplikacji logiki do grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Jeśli zostanie **wyświetlene** pole Edytuj parametry, podaj nazwę zasobu dla aplikacji logiki. Zapisz ustawienia.

   ![Podawanie nazwy wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure.

   ![Dane wyjściowe stanu wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Jeśli wybrane łączniki wymagają danych wejściowych od Ciebie, w tle zostanie otwarte okno programu PowerShell z monitem o podanie niezbędnych haseł lub tajnych kluczy. Po wprowadzeniu tych informacji wdrażanie będzie kontynuowane.

   ![Okno programu PowerShell](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po zakończeniu wdrażania aplikacja logiki jest dostępna w witrynie Azure portal i działa zgodnie z określonym harmonogramem (co minutę). Jeśli wyzwalacz znajdzie nowe elementy pliku danych, wyzwalacz jest uruchamiany, co tworzy wystąpienie przepływu pracy, które uruchamia akcje aplikacji logiki. Aplikacja logiki wysyła wiadomości e-mail dla każdego nowego elementu. W przeciwnym razie jeśli wyzwalacz nie znajdzie nowych elementów, wyzwalacz nie uruchamia się i "pomija" tworzenie wystąpienia przepływu pracy. Aplikacja logiki czeka do następnego interwału przed sprawdzeniem.

   Oto przykładowe wiadomości e-mail, które wysyła ta aplikacja logiki. Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci.

   ![Program Outlook wysyła wiadomość e-mail dla każdego nowego elementu danych RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulacje, pomyślnie szesnasta i wdrożona aplikacja logiki w programie Visual Studio. Aby zarządzać aplikacją logiki i przeglądać historię jej uruchomień, zobacz [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Dodawanie nowej aplikacji logiki

Jeśli masz istniejący projekt grupy zasobów platformy Azure, możesz dodać nową pustą aplikację logiki do tego projektu przy użyciu okna Konspekt JSON.

1. W Eksploratorze `<logic-app-name>.json` rozwiązań otwórz plik.

1. Z menu **Widok** wybierz polecenie Inne**konspekt JSON** **systemu Windows** > .

1. Aby dodać zasób do pliku szablonu, wybierz pozycję **Dodaj zasób** u góry okna Konspektu JSON. W oknie Konspektu JSON otwórz menu **skrótów zasobów** i wybierz pozycję **Dodaj nowy zasób**.

   ![Okno Konspektu JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. W oknie dialogowym **Dodawanie zasobu** w `logic app`polu wyszukiwania znajdź i wybierz pozycję **Aplikacja logiki**. Nazwij aplikację logiki i wybierz pozycję **Dodaj**.

   ![Dodawanie zasobu](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z aplikacją logiki usuń grupę zasobów, która zawiera aplikację logiki i powiązanych zasobów.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) za pomocą konta używanego do tworzenia aplikacji logiki.

1. W menu portalu azure wybierz pozycję **Grupy zasobów**lub wyszukaj i wybierz **grupy zasobów** z dowolnej strony. Wybierz grupę zasobów aplikacji logiki.

1. Na stronie **Przegląd** wybierz pozycję **Usuń grupę zasobów**. Wprowadź nazwę grupy zasobów jako potwierdzenie i wybierz pozycję **Usuń**.

   ![„Grupy zasobów” > „Przegląd” > „Usuń grupę zasobów”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Usuń rozwiązanie programu Visual Studio z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

W tym artykule kompilowano, wdrożono i uruchomiono aplikację logiki przy użyciu programu Visual Studio. Aby dowiedzieć się więcej na temat zarządzania zaawansowanym wdrażaniem aplikacji w programie Visual Studio i wykonywania ich, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
