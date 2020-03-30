---
title: Eksportowanie przepływów z automatyzacji zasilania do aplikacji logiki azure
description: Migrowanie przepływów z automatyzacji zasilania do aplikacji logiki platformy Azure przez eksportowanie jako szablonów usługi Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428881"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Eksportowanie przepływów z usługi Power Automate i wdrażanie w usłudze Azure Logic Apps

Aby rozszerzyć i rozszerzyć możliwości przepływu, można przeprowadzić migrację z [automatyki power](https://flow.microsoft.com) do [aplikacji Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Można wyeksportować przepływ jako szablon usługi Azure Resource Manager dla aplikacji logiki, wdrożyć ten szablon aplikacji logiki do grupy zasobów platformy Azure, a następnie otworzyć tę aplikację logiki w Logic App Designer.

> [!NOTE]
> Nie wszystkie łączniki automatyzacji zasilania są dostępne w usłudze Azure Logic Apps. Można importować przepływy, które mają [równoważne łączniki](../connectors/apis-list.md) w usłudze Azure Logic Apps. Na przykład button wyzwalacz, złącze zatwierdzania i łącznik powiadomień są specyficzne dla automatyzacji zasilania.
>
> Przepływy oparte na openapi eksportowane z usługi Power Automate nie są obecnie obsługiwane do wdrażania jako szablony aplikacji logiki. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Przepływ, który chcesz wyeksportować z automatyki zasilania

## <a name="export-a-flow"></a>Eksportowanie przepływu

1. Zaloguj się do [automatyki zasilania](https://flow.microsoft.com)i wybierz **pozycję Moje przepływy**. Znajdź i wybierz swój przepływ. Na pasku narzędzi wybierz przycisk elipsy (**...**). Wybierz **szablon Eksportuj** > **aplikacje logiki (json)**.

   ![Eksportowanie przepływu](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Zapisz szablon w żądanej lokalizacji.

Aby uzyskać więcej informacji, zobacz [Dorastanie do usługi Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Wdrażanie szablonu przy użyciu portalu Azure

1. Zaloguj się w [witrynie Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź "wdrożenie szablonu". Wybierz **pozycję Wdrażanie szablonów (wdrażanie przy użyciu szablonów niestandardowych),** a następnie wybierz pozycję **Utwórz**.

   ![Wybierz "Wdrożenie szablonu"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. W obszarze **Wdrożenie niestandardowe**wybierz pozycję **Zbuduj własny szablon w edytorze**.

   ![Wybierz "Zbuduj własny szablon w edytorze"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na pasku narzędzi **Edytowanie szablonu** wybierz pozycję **Załaduj plik**. Znajdź i wybierz szablon JSON wyeksportowany z automatu zasilania, a następnie wybierz pozycję **Otwórz**.

   ![Wybierz "Załaduj plik"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Po tym, jak edytor pokaże JSON, parametry i zasoby w szablonie, wybierz pozycję **Zapisz**.
  
   ![Zapisywanie szablonu](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Teraz określ te parametry wejściowe dla szablonu:

   * Subskrypcja platformy Azure do użycia w rozliczeniach
   * Grupa zasobów platformy Azure
   * Lokalizacja dla grupy zasobów platformy Azure
   * Nazwa zasobu aplikacji logiki
   * Lokalizacja zasobu aplikacji logiki, jeśli różni się od grupy zasobów platformy Azure
   * Nazwa wszystkich wcześniej utworzonych połączeń, których aplikacja logiki może ponownie użyć

      Jeśli tworzysz pierwszą aplikację logiki, wszystkie połączenia są tworzone jako nowe, dzięki czemu można zaakceptować nazwy domyślne. W przeciwnym razie można określić nazwy wcześniej utworzonych połączeń, których można używać w wielu aplikacjach logiki.

   Po podaniem tych informacji dla szablonu, przejrzyj i zaakceptuj Warunki korzystania z portalu Azure Marketplace w celu utworzenia niezbędnych zasobów platformy Azure i odpowiedniego rozliczenia subskrypcji platformy Azure, a następnie wybierz pozycję **Zakup**.
  
   ![Określanie parametrów wejściowych dla szablonu](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Platforma Azure wdraża szablon jako aplikację logiki do określonej grupy zasobów. Wszystkie aplikacje logiki, które są migrowane z automatyzacji zasilania są wdrażane w stanie wyłączonym.

1. Przed aktywacją aplikacji logiki należy autoryzować nowe połączenia, wykonując następujące kroki:

   1. Otwórz utworzoną aplikację logiki. W menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki**.

      Każde połączenie wymagające autoryzacji zawiera ikonę ostrzeżenia:

      ![Ikona ostrzeżenia](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Dla każdego kroku, który wymaga autoryzowanego połączenia, rozwiń ten krok i wybierz pozycję **Dodaj nowy**.

      ![Dodawanie nowego połączenia](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Zaloguj się do każdej usługi lub podaj poświadczenia niezbędne do autoryzacji połączenia.

1. Zapisz aplikację logiki. Gdy będziesz gotowy do aktywacji aplikacji logiki, w menu aplikacji logiki wybierz **pozycję Przegląd**, a następnie wybierz pozycję **Włącz**.

   ![Włącz aplikację logiki](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Aby uniknąć uruchamiania zduplikowanych przepływów pracy, upewnij się, że dezaktywujesz lub usuniesz oryginalny przepływ.

## <a name="deploy-template-by-using-visual-studio"></a>Wdrażanie szablonu przy użyciu programu Visual Studio

Jeśli program Visual Studio został skonfigurowany z [wymaganiami wstępnymi](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) do tworzenia aplikacji logiki, można wdrożyć eksportowany szablon z programu Visual Studio do usługi Azure Logic Apps.

1. W programie Visual Studio otwórz plik szablonu wyeksportowany z automatu programu Power Automate.

1. W programie Visual Studio utwórz projekt grupy zasobów platformy Azure i wybierz szablon **aplikacji logiki,** wykonując kroki opisane w [przewodniku Szybki start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)za pomocą aplikacji Azure Logic — Visual Studio , na przykład:

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. W Eksploratorze rozwiązań otwórz plik **LogicApp.json,** jeśli plik nie jest jeszcze otwarty.

1. Skopiuj zawartość z wyeksportowanego szablonu i zastąp zawartość pliku **LogicApp.json.**

1. Przed wdrożeniem aplikacji logiki należy autoryzować wszystkie nowe połączenia, wykonując następujące kroki:

   1. Otwórz menu skrótów **LogicApp.json,** a następnie wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**.

      ![Otwórz szablon za pomocą projektanta aplikacji logiki](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Jeśli zostanie wyświetlony monit, wybierz subskrypcję platformy Azure i grupę zasobów, których chcesz użyć do wdrażania aplikacji logiki.

      ![Wybieranie subskrypcji platformy Azure i grupy zasobów](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Po aplikacji logiki pojawia się w projektancie, wszystkie połączenia, które wymagają autoryzacji pokaż ikony ostrzeżenia:

      ![Połączenia z ikonami ostrzegawczymi](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Dla każdego kroku, który wymaga autoryzowanego połączenia, rozwiń ten krok i wybierz pozycję **Dodaj nowy**.

      ![Dodawanie nowego połączenia](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Zaloguj się do każdej usługi lub podaj poświadczenia niezbędne do autoryzacji połączenia.

   1. Zapisz rozwiązanie przed wdrożeniem aplikacji logiki.

1. W Eksploratorze rozwiązań otwórz menu skrótów projektu i wybierz pozycję **Wdrażaj** > **nowy**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

1. Po wyświetleniu monitu potwierdź subskrypcję platformy Azure, grupę zasobów platformy Azure i inne ustawienia, których chcesz użyć do wdrożenia, takie jak [plik parametrów](../azure-resource-manager/templates/parameter-files.md) używany do przekazywania wartości parametrów szablonu, a następnie wybierz pozycję **Wdrażanie**.

   ![Potwierdzanie ustawień wdrażania](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Jeśli zostanie **wyświetlene** pole Edytuj parametry, podaj nazwę zasobu aplikacji logiki na platformie Azure i wybierz pozycję **Zapisz**.  

   ![Edytowanie parametrów wdrażania](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure. Przykład:

   ![Okno danych wyjściowych](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Jeśli jakiekolwiek połączenia w aplikacji logiki wymagają danych wejściowych od Ciebie, okno programu PowerShell otwiera się w tle i monituje o wszelkie niezbędne hasła lub klucze tajne. Po wprowadzeniu tych informacji wdrażanie będzie kontynuowane.

   ![Uwierzytelnianie połączeń](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Po zakończeniu wdrażania aplikacja logiki jest publikowana, ale nie jest aktywowana w witrynie Azure portal.

1. Gdy będziesz gotowy, aby aktywować aplikację logiki w witrynie Azure portal, znajdź i otwórz aplikację logiki w Logic App Designer. W menu aplikacji logiki wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Włącz**.

1. Aby uniknąć uruchamiania zduplikowanych przepływów pracy, upewnij się, że dezaktywujesz lub usuniesz oryginalny przepływ.

Aby uzyskać więcej informacji na temat tych kroków wdrażania, zobacz [Szybki start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy za pomocą aplikacji Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [łącznikach dla aplikacji logiki azure](../connectors/apis-list.md)
* Dowiedz się więcej o [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md)
