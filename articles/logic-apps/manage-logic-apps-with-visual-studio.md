---
title: Edytowanie aplikacji logiki i zarządzanie nimi przy użyciu programu Visual Studio w Eksploratorze chmury
description: Edytowanie, aktualizowanie, zarządzanie, dodawanie do kontroli źródła i wdrażanie aplikacji logicznych przy użyciu programu Visual Studio za pomocą Eksploratora chmury
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803194"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Zarządzanie aplikacjami logiki w programie Visual Studio

Chociaż można tworzyć, edytować, zarządzać i wdrażać aplikacje logiki w [witrynie Azure Portal,](https://portal.azure.com)można również użyć programu Visual Studio, jeśli chcesz dodać aplikacje logiki do kontroli źródła, publikować różne wersje i tworzyć szablony [usługi Azure Resource Manager](../azure-resource-manager/management/overview.md) dla różnych środowisk wdrażania. Za pomocą programu Visual Studio Cloud Explorer można znaleźć aplikacje logiki i zarządzać nimi wraz z innymi zasobami platformy Azure. Na przykład można otwierać, pobierać, edytować, uruchamiać, wyświetlać historię uruchamiania, wyłączać i włączać aplikacje logiki, które są już wdrożone w witrynie Azure Portal. Jeśli jesteś nowy w pracy z usługi Azure Logic Apps w programie Visual Studio, dowiedz się, [jak tworzyć aplikacje logiki za pomocą programu Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

Można również [zarządzać aplikacjami logiki w witrynie Azure portal](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Wdrażanie lub publikowanie aplikacji logiki z programu Visual Studio zastępuje wersję tej aplikacji w witrynie Azure Portal. Jeśli więc wprowadzać zmiany w witrynie Azure portal, które chcesz zachować, upewnij się, że [odświeżyć aplikację logiki w programie Visual Studio](#refresh) z witryny Azure portal przed następnym wdrożeniem lub opublikowaniem z programu Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio 2019, 2017 lub 2015 — wersja społecznościowa lub większa](https://aka.ms/download-visual-studio). Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Podczas instalowania programu Visual Studio 2019 lub 2017 upewnij się, że wybierzesz obciążenie **deweloperskie platformy Azure.**
    > Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami skojarzonymi z kontami platformy Azure w Eksploratorze chmury programu Visual Studio.](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)

    Aby zainstalować Cloud Explorer dla programu Visual Studio 2015, [pobierz Cloud Explorer z portalu Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami skojarzonymi z kontami platformy Azure w Eksploratorze chmury programu Visual Studio (2015).](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)

  * [Zestaw SDK platformy Azure (2.9.1 lub nowszy)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Najnowsze narzędzia azure logiki aplikacji dla rozszerzenia programu Visual Studio dla wersji, która ma:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do sieci Web podczas korzystania z wbudowanego projektanta aplikacji logiki

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Znajdowanie aplikacji logiki

W programie Visual Studio można znaleźć wszystkie aplikacje logiki, które są skojarzone z subskrypcją platformy Azure i są wdrażane w witrynie Azure portal przy użyciu Cloud Explorer.

1. Otwórz program Visual Studio. W menu **Widok** wybierz polecenie **Cloud Explorer**.

1. W Eksploratorze chmury wybierz pozycję **Zarządzanie kontem**. Wybierz subskrypcję platformy Azure skojarzoną z aplikacjami logiki, a następnie wybierz pozycję **Zastosuj**. Przykład:

   ![Wybierz "Zarządzanie kontem"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Na podstawie tego, czy wyszukujesz według **grup zasobów,** czy **typów zasobów,** wykonaj następujące czynności:

   * **Grupy zasobów:** W ramach subskrypcji platformy Azure Eksplorator chmury pokazuje wszystkie grupy zasobów skojarzone z tą subskrypcją. Rozwiń grupę zasobów zawierającą aplikację logiki, a następnie wybierz aplikację logiki.

   * **Typy zasobów:** W ramach subskrypcji platformy Azure rozwiń węzeł **Aplikacje logiki**. Po Tym, jak Eksplorator chmury pokazuje wszystkie wdrożone aplikacje logiki, które są skojarzone z subskrypcją, wybierz aplikację logiki.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otwórz w programie Visual Studio

W programie Visual Studio można otworzyć aplikacje logiki wcześniej utworzone i wdrożone bezpośrednio za pośrednictwem witryny Azure portal lub jako projekty grupy zasobów platformy Azure w programie Visual Studio.

1. Otwórz Cloud Explorer i znajdź aplikację logiki.

1. W menu skrótów aplikacji logiki wybierz polecenie **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   W tym przykładzie pokazano aplikacje logiki według typu zasobu, więc aplikacje logiki są wyświetlane w sekcji **Aplikacje logiki.**

   ![Otwieranie wdrożonej aplikacji logiki z witryny Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otwarciu aplikacji logiki w Logic Apps Designer, u dołu projektanta, można wybrać **widok kodu,** dzięki czemu można przejrzeć podstawową strukturę definicji aplikacji logiki. Jeśli chcesz utworzyć szablon wdrożenia dla aplikacji logiki, dowiedz się, [jak pobrać szablon usługi Azure Resource Manager](#download-logic-app) dla tej aplikacji logiki. Dowiedz się więcej o [szablonach Menedżera zasobów](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Pobieranie z platformy Azure

Możesz pobrać aplikacje logiki z [witryny Azure portal](https://portal.azure.com) i zapisać je jako szablony [usługi Azure Resource Manager.](../azure-resource-manager/management/overview.md) Następnie można lokalnie edytować szablony za pomocą programu Visual Studio i dostosowywać aplikacje logiki dla różnych środowisk wdrażania.  Pobieranie aplikacji logiki automatycznie *parametryzuje* ich definicje wewnątrz [szablonów Menedżera zasobów,](../azure-resource-manager/templates/overview.md)które również używają notacji obiektów JavaScript (JSON).

1. W programie Visual Studio otwórz Eksploratora chmury. Znajdź i wybierz aplikację logiki, którą chcesz pobrać z platformy Azure.

1. W menu skrótów tej aplikacji wybierz polecenie **Otwórz za pomocą edytora aplikacji Logika**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   Projektant aplikacji logiki otwiera i pokazuje aplikację logiki. Aby przejrzeć podstawową definicję i strukturę aplikacji logiki, u dołu projektanta wybierz pozycję **Widok kodu**.

1. Na pasku narzędzi projektanta wybierz pozycję **Pobierz**.

   ![Pobieranie aplikacji logiki z witryny Azure portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Po wyświetleniu monitu o lokalizację przejdź do tej lokalizacji i zapisz szablon Menedżera zasobów dla definicji aplikacji logiki w formacie pliku JSON (json).

   Definicja aplikacji logiki `resources` jest wyświetlana w podsekcji wewnątrz szablonu Menedżera zasobów. Teraz można edytować definicję aplikacji logiki i szablon Menedżera zasobów za pomocą programu Visual Studio. Można również dodać szablon jako [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do rozwiązania programu Visual Studio. Dowiedz się więcej o [projektach grupy zasobów platformy Azure dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Łącze do konta integracji

Aby utworzyć aplikacje logiki dla scenariuszy integracji przedsiębiorstwa między firmami (B2B), można połączyć aplikację logiki z wcześniej [utworzonym kontem integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) które istnieje w tym samym regionie co aplikacja logiki. Konto integracji zawiera artefakty B2B, takie jak partnerzy handlowi, umowy, schematy i mapy, a także umożliwia aplikacji logiki używanie łączników B2B do sprawdzania poprawności XML i kodowania lub dekodowania plików płaskich. Chociaż [można utworzyć to łącze przy użyciu portalu Azure portal,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)można również użyć programu Visual Studio po spełnienie wymagań [wstępnych,](#requirements)a aplikacja logiki istnieje jako plik JSON (json) wewnątrz [projektu grupy zasobów platformy Azure.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Dowiedz się więcej o [projektach grupy zasobów platformy Azure dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. W programie Visual Studio otwórz projekt grupy zasobów platformy Azure zawierający aplikację logiki.

1. W Eksploratorze rozwiązań otwórz menu skrótów **<nazwa>.json** i wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwórz plik .json aplikacji logiki za pomocą projektanta aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio i rozszerzenia Narzędzia Azure Logic Apps Tools.

1. Upewnij się, że projektant aplikacji logiki ma fokus, wybierając kartę projektanta lub powierzchni, tak aby w oknie Właściwości jest wyświetlana właściwość **Konto integracji** dla aplikacji logiki.

   ![Okno Właściwości — właściwość "Konto integracji"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Jeśli okno Właściwości nie jest jeszcze otwarte, z menu **Widok** wybierz polecenie **Okno Właściwości**. (Klawiatura: Naciśnij klawisz F4)

1. Otwórz listę właściwości **Konto integracji** i wybierz konto integracji, które chcesz połączyć z aplikacją logiki, na przykład:

   ![Otwórz listę właściwości "Konto integracji"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Po zakończeniu pamiętaj, aby zapisać rozwiązanie programu Visual Studio.

Po ustawieniu właściwości **konto integracji** w programie Visual Studio i zapisać aplikację logiki jako szablon usługi Azure Resource Manager, szablon ten zawiera również deklarację parametrów dla wybranego konta integracji. Aby uzyskać więcej informacji na temat parametrów szablonu i aplikacji logiki, zobacz [Omówienie: Automatyzacja wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Zmienianie lokalizacji wdrożenia

W programie Visual Studio, jeśli aplikacja logiki istnieje jako plik JSON (json) w [projekcie grupy zasobów platformy Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) który służy do automatyzacji wdrażania, ta aplikacja logiki jest ustawiona na typ lokalizacji i określonej lokalizacji. Ta lokalizacja jest regionem platformy Azure lub istniejącym [środowiskiem usługi integracji (ISE).](connect-virtual-network-vnet-isolated-environment.md)

Aby zmienić typ lokalizacji lub lokalizację aplikacji logiki, należy otworzyć plik definicji przepływu pracy aplikacji logiki (json) z Eksploratora rozwiązań przy użyciu projektanta aplikacji logiki. Nie można zmienić tych właściwości przy użyciu Cloud Explorer.

> [!IMPORTANT]
> Zmiana typu lokalizacji z **Region na** Środowisko [**usługi integracji**](connect-virtual-network-vnet-isolated-environment-overview.md) wpływa na [model cenowy](logic-apps-pricing.md#fixed-pricing) aplikacji logiki, który jest używany do rozliczeń, [limitów,](logic-apps-limits-and-config.md#integration-account-limits) [obsługi kont integracji](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)i tak dalej. Przed wybraniem innego typu lokalizacji upewnij się, że rozumiesz wynikowy wpływ na aplikację logiki.

1. W programie Visual Studio otwórz projekt grupy zasobów platformy Azure zawierający aplikację logiki.

1. W Eksploratorze `<logic-app-name>.json` rozwiązań otwórz menu skrótów pliku i wybierz pozycję **Otwórz za pomocą projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwórz plik .json aplikacji logiki za pomocą projektanta aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio i rozszerzenia Narzędzia Azure Logic Apps Tools.

1. Upewnij się, że projektant aplikacji logiki fokus, wybierając kartę projektanta lub powierzchni, tak aby właściwości okna pokazuje **Wybierz typ lokalizacji** i **lokalizacja** właściwości dla aplikacji logiki. Typ lokalizacji projektu jest ustawiony na **środowisko regionu** lub **usługi integracji.**

   ![Okno Właściwości — "Wybierz typ lokalizacji" & właściwości "Lokalizacja"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Jeśli okno Właściwości nie jest jeszcze otwarte, z menu **Widok** wybierz polecenie **Okno Właściwości**. (Klawiatura: Naciśnij klawisz F4)

1. Aby zmienić typ lokalizacji, otwórz listę właściwości **Wybierz typ lokalizacji** i wybierz odpowiedni typ lokalizacji.

   Na przykład, jeśli typem lokalizacji jest **Środowisko usługi integracji,** można wybrać **region**.

   ![Właściwość "Wybierz typ lokalizacji" — zmień typ lokalizacji](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Aby zmienić konkretną lokalizację, otwórz listę właściwości **Lokalizacja.** Na podstawie typu lokalizacji wybierz odpowiednią lokalizację, na przykład:

   * Wybierz inny region platformy Azure:

     ![Otwórz listę właściwości "Lokalizacja", wybierz inny region platformy Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Wybierz inny ISE:

     ![Otwórz listę właściwości "Lokalizacja", wybierz inną ise](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Po zakończeniu pamiętaj, aby zapisać rozwiązanie programu Visual Studio.

Po zmianie typu lokalizacji lub lokalizacji w programie Visual Studio i zapisać aplikację logiki jako szablon usługi Azure Resource Manager, szablon ten zawiera również deklaracje parametrów dla tego typu lokalizacji i lokalizacji. Aby uzyskać więcej informacji na temat parametrów szablonu i aplikacji logiki, zobacz [Omówienie: Automatyzacja wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Odświeżanie z platformy Azure

Jeśli edytujesz aplikację logiki w witrynie Azure portal i chcesz zachować te zmiany, upewnij się, że odświeżysz wersję tej aplikacji w programie Visual Studio z tymi zmianami.

* W programie Visual Studio na pasku narzędzi Projektanta aplikacji logiki wybierz pozycję **Odśwież**.

  — lub —

* W Eksploratorze chmury programu Visual Studio otwórz menu skrótów aplikacji logiki i wybierz pozycję **Odśwież**.

![Odświeżanie aplikacji logiki z aktualizacjami](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publikowanie aktualizacji aplikacji logiki

Gdy będziesz gotowy do wdrożenia aktualizacji aplikacji logiki z programu Visual Studio na platformie Azure, na pasku narzędzi Projektanta aplikacji logiki wybierz pozycję **Publikuj**.

![Publikowanie zaktualizowanej aplikacji logiki w witrynie Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Ręczne uruchamianie aplikacji logiki

Można ręcznie wyzwolić aplikację logiki wdrożoną na platformie Azure z programu Visual Studio. Na pasku narzędzi Projektanta aplikacji logiki wybierz pozycję **Uruchom wyzwalacz**.

![Ręczne uruchamianie wyzwalacza aplikacji logiki](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Aby sprawdzić stan i zdiagnozować problemy z uruchomień aplikacji logiki, można przejrzeć szczegóły, takie jak dane wejściowe i wyjściowe, dla tych uruchamia się w programie Visual Studio.

1. W Eksploratorze chmury otwórz menu skrótów aplikacji logiki i wybierz pozycję **Otwórz historię uruchamiania**.

   ![Otwieranie historii uruchamiania aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Aby wyświetlić szczegóły określonego przebiegu, kliknij dwukrotnie bieg. Przykład:

   ![Wyświetlanie informacji o określonym przebiegu](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Aby posortować tabelę według właściwości, wybierz nagłówek kolumny dla tej właściwości.

1. Rozwiń kroki, których dane wejściowe i wyjściowe chcesz przejrzeć, na przykład:

   ![Wyświetlanie wejść i wyjść dla każdego kroku](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

Bez usuwania aplikacji logiki, można zatrzymać wyzwalacza od uruchamiania następnym razem, gdy warunek wyzwalacza jest spełniony. Wyłączenie aplikacji logiki uniemożliwia aparat aplikacji logiki tworzenia i uruchamiania przyszłych wystąpień przepływu pracy dla aplikacji logiki. W Eksploratorze chmury otwórz menu skrótów aplikacji logiki i wybierz pozycję **Wyłącz**.

![Wyłączanie aplikacji logiki w Eksploratorze chmury](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Po wyłączeniu aplikacji logiki nie zostaną utworzyć żadnych nowych uruchomień. Wszystkie przebiegi w toku i oczekujące będą kontynuowane do czasu ich zakończenia, co może zająć trochę czasu.

Aby ponownie aktywować aplikację logiki, w Eksploratorze chmury otwórz menu skrótów aplikacji logiki i wybierz pozycję **Włącz**.

![Włączanie aplikacji logiki w Eksploratorze chmury](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Usuwanie aplikacji logiki

Aby usunąć aplikację logiki z witryny Azure portal, w Eksploratorze chmury otwórz menu skrótów aplikacji logiki i wybierz pozycję **Usuń**.

![Usuwanie aplikacji logiki z witryny Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po otwarciu projektu aplikacji logiki w Logic Apps Designer, może nie być dostępna opcja wyboru subskrypcji platformy Azure. Zamiast tego aplikacja logiki zostanie otwarta z subskrypcją platformy Azure, która nie jest tą, której chcesz użyć. To zachowanie występuje, ponieważ po otwarciu pliku .json aplikacji logiki, Visual Studio buforuje pierwszą wybraną subskrypcję do wykorzystania w przyszłości. Aby rozwiązać ten problem, spróbuj wykonać jedną z następujących czynności:

* Zmień nazwę pliku .json aplikacji logiki. Pamięć podręczna subskrypcji zależy od nazwy pliku.

* Aby usunąć wcześniej wybrane subskrypcje dla *wszystkich* aplikacji logiki w rozwiązaniu, usuń ukryty folder ustawień programu Visual Studio (.vs) w katalogu rozwiązania. Ta lokalizacja przechowuje informacje o subskrypcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak zarządzać wdrożonymi aplikacjami logiki za pomocą programu Visual Studio. Następnie dowiedz się więcej o dostosowywaniu definicji aplikacji logiki do wdrożenia:

> [!div class="nextstepaction"]
> [Definicje aplikacji logiki autora w json](../logic-apps/logic-apps-author-definitions.md)
