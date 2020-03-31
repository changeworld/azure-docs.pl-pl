---
title: Automatyzacja zadań za pomocą programu Visual Studio Code
description: Tworzenie lub edytowanie aplikacji logiki leżącej u podstaw definicji JSON przy użyciu programu Visual Studio Code (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 819a60887743f39d3c2ffab3c955b2980cee2725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74784837"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Szybki start: tworzenie definicji przepływu pracy aplikacji logiki i zarządzanie nimi przy użyciu kodu programu Visual Studio

Dzięki [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md) i visual studio code można tworzyć aplikacje logiki i zarządzać nimi, które ułatwiają automatyzację zadań, przepływów pracy i procesów w celu integracji aplikacji, danych, systemów i usług w różnych organizacjach i przedsiębiorstwach. Ten przewodnik Szybki start pokazuje, jak można tworzyć i edytować podstawowe definicje przepływu pracy, które używają notacji obiektów JavaScript (JSON) dla aplikacji logiki za pomocą środowiska opartego na kodzie. Można również pracować nad istniejącymi aplikacjami logiki, które są już wdrożone na platformie Azure.

Chociaż można wykonywać te same zadania w [witrynie Azure portal](https://portal.azure.com) i visual studio, można rozpocząć szybciej w programie Visual Studio Code, gdy jesteś już zaznajomiony z definicjami aplikacji logiki i chcesz pracować bezpośrednio w kodzie. Na przykład można wyłączyć, włączyć, usunąć i odświeżyć już utworzone aplikacje logiki. Ponadto można pracować nad aplikacjami logiki i kont integracji z dowolnej platformy dewelopera, gdzie program Visual Studio Code działa, takich jak Linux, Windows i Mac.

W tym artykule można utworzyć tę samą aplikację logiki z tego [przewodnika Szybki start,](../logic-apps/quickstart-create-first-logic-app-workflow.md)który koncentruje się bardziej na podstawowych pojęć. W programie Visual Studio Code aplikacja logiki wygląda następująco:

![Przykładowa definicja przepływu pracy aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Jeśli nie masz konta platformy Azure i subskrypcji, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Podstawowa wiedza na temat [definicji przepływu pracy aplikacji logiki](../logic-apps/logic-apps-workflow-definition-language.md) i ich struktury zgodnie z opisem w języku JSON

  Jeśli jesteś nowy w logic apps, spróbuj tego [przewodnika Szybki start,](../logic-apps/quickstart-create-first-logic-app-workflow.md)który tworzy pierwsze aplikacje logiki w witrynie Azure portal i koncentruje się bardziej na podstawowych pojęć.

* Dostęp do sieci Web w celu zalogowania się na platformę Azure i subskrypcję platformy Azure

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Kod programu Visual Studio w wersji 1.25.1 lub nowszej](https://code.visualstudio.com/), który jest bezpłatny

  * Rozszerzenie kodu programu Visual Studio dla aplikacji logiki azure

    To rozszerzenie można pobrać i zainstalować z [portalu Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) lub bezpośrednio z programu Visual Studio Code. Upewnij się, że ponownie załadować program Visual Studio Code po zainstalowaniu.

    ![Znajdź "Rozszerzenie kodu programu Visual Studio dla aplikacji logiki azure"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Aby sprawdzić, czy rozszerzenie zostało poprawnie zainstalowane, wybierz ikonę platformy Azure, która pojawia się na pasku narzędzi kodu programu Visual Studio.

    ![Potwierdzanie poprawnie zainstalowanego rozszerzenia](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Aby uzyskać więcej informacji, zobacz [Rynek rozszerzeń](https://code.visualstudio.com/docs/editor/extension-gallery). Aby przyczynić się do wersji open source tego rozszerzenia, odwiedź [rozszerzenie Usługi Azure Logic Apps dla programu Visual Studio Code w usłudze GitHub.](https://github.com/Microsoft/vscode-azurelogicapps)

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Otwórz program Visual Studio Code. Na pasku narzędzi Kodu programu Visual Studio wybierz ikonę platformy Azure.

   ![Wybieranie ikony platformy Azure na pasku narzędzi Visual Studio Code](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. W oknie Platformy Azure w obszarze **Aplikacje logiki**wybierz pozycję **Zaloguj się na platformie Azure**. Gdy strona logowania firmy Microsoft wyświetli monit, zaloguj się za pomocą konta platformy Azure.

   ![Wybierz opcję "Zaloguj się na platformie Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Jeśli logowanie trwa dłużej niż zwykle, program Visual Studio Code monituje o zalogowanie się za pośrednictwem witryny uwierzytelniania firmy Microsoft, podając kod urządzenia. Aby zamiast tego zalogować się przy użyciu kodu, wybierz opcję **Użyj kodu urządzenia**.

      ![Kontynuuj z kodem urządzenia zamiast](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Aby skopiować kod, wybierz **pozycję Kopiuj & Otwórz**.

      ![Kopiowanie kodu logowania na platformie Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Aby otworzyć nowe okno przeglądarki i przejść do witryny uwierzytelniania, wybierz pozycję **Otwórz łącze**.

      ![Potwierdź otwarcie przeglądarki i przejście do witryny uwierzytelniania](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na stronie **Zaloguj się na konto** wprowadź kod uwierzytelniania i wybierz pozycję **Dalej**.

      ![Wprowadzanie kodu uwierzytelniania dla logowania się na platformie Azure](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Wybierz swoje konto platformy Azure. Po zalogowaniu się można zamknąć przeglądarkę i powrócić do programu Visual Studio Code.

   W okienku platformy Azure w sekcjach **Aplikacje logiki** i **konta integracji** są teraz wyświetlane subskrypcje platformy Azure skojarzone z Twoim kontem. Jeśli jednak nie widzisz oczekiwanych subskrypcji lub jeśli w sekcjach jest zbyt wiele subskrypcji, wykonaj następujące czynności:

   1. Przesuń wskaźnik myszy na etykietę **Aplikacje logiki.** Po wyświetleniu paska narzędzi wybierz **pozycję Wybierz subskrypcje** (ikona filtru).

      ![Znajdowanie lub filtrowanie subskrypcji platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Z wyświetlona lista wybierz subskrypcje, które mają być wyświetlane.

1. W obszarze **Aplikacje logiki**wybierz odpowiednią subskrypcję. Węzeł subskrypcji rozwija się i pokazuje wszystkie aplikacje logiki, które istnieją w tej subskrypcji.

   ![Wybierz subskrypcję platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > W obszarze **Konta integracji**wybranie subskrypcji powoduje wyświetlenie wszystkich kont integracji, które istnieją w tej subskrypcji.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Tworzenie nowej aplikacji logiki

1. Jeśli nie zalogowano się jeszcze do konta platformy Azure i subskrypcji z wewnątrz programu Visual Studio Code, wykonaj [poprzednie kroki, aby zalogować się teraz.](#sign-in-azure)

1. W programie Visual Studio w obszarze **Aplikacje logiki**otwórz menu skrótów subskrypcji i wybierz pozycję **Utwórz aplikację logiki**.

   ![Z menu subskrypcji wybierz "Utwórz aplikację logiki"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Pojawi się lista i pokazuje wszystkie grupy zasobów platformy Azure w ramach subskrypcji.

1. Z listy grup zasobów wybierz pozycję **Utwórz nową grupę zasobów** lub istniejącą grupę zasobów. W tym przykładzie utwórz nową grupę zasobów.

   ![Tworzenie nowej grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Podaj nazwę swojej grupy zasobów platformy Azure i naciśnij klawisz ENTER.

   ![Podaj nazwę swojej grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Wybierz region platformy Azure, w którym chcesz zapisać metadane aplikacji logiki.

   ![Wybieranie lokalizacji platformy Azure do zapisywania metadanych aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Podaj nazwę aplikacji logiki i naciśnij klawisz Enter.

   ![Podaj nazwę aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   W oknie platformy Azure w ramach subskrypcji platformy Azure zostanie wyświetlona nowa i pusta aplikacja logiki. Visual Studio Code otwiera również plik JSON (.logicapp.json), który zawiera definicję przepływu pracy szkieletu dla aplikacji logiki. Teraz możesz rozpocząć ręczne tworzenie definicji przepływu pracy aplikacji logiki w tym pliku JSON. Aby uzyskać informacje techniczne dotyczące struktury i składni definicji przepływu pracy, zobacz [schemat języka definicji przepływu pracy dla aplikacji Logika Azure .](../logic-apps/logic-apps-workflow-definition-language.md)

   ![Pusty plik JSON definicji przepływu pracy aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Na przykład w tym miejscu znajduje się definicja przepływu pracy aplikacji logiki przykładowej, która rozpoczyna się od wyzwalacza RSS i akcji programu Outlook usługi Office 365. Zazwyczaj elementy JSON pojawiają się alfabetycznie w każdej sekcji. Jednak w tym przykładzie pokazuje te elementy mniej więcej w kolejności, w aplikacji pojawiają się w projektancie.

   > [!IMPORTANT]
   > Jeśli chcesz ponownie użyć tej przykładowej definicji aplikacji logiki, potrzebujesz konta organizacyjnego usługi Office 365, @fabrikam.comna przykład . Upewnij się, że fikcyjny adres e-mail zastępuje się własnym adresem e-mail. Aby użyć innego łącznika poczty e-mail, takiego `Send_an_email_action` jak Outlook.com lub Gmail, zastąp akcję podobną akcją dostępną z [łącznika poczty e-mail obsługiwanego przez usługę Azure Logic Apps](../connectors/apis-list.md).

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. Po zakończeniu zapisz definicję przepływu pracy aplikacji logiki. (Menu plik > Zapisz lub naciśnij klawisze Ctrl+S)

1. Po wyświetleniu monitu o przekazanie aplikacji logiki do subskrypcji platformy Azure wybierz pozycję **Przekaż**.

   Ten krok publikuje aplikację logiki do [witryny Azure portal](https://portal.azure.com), który i sprawia, że logika na żywo i działa na platformie Azure.

   ![Przekazywanie nowej aplikacji logiki do subskrypcji platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Wyświetlanie aplikacji logiki w projektancie

W programie Visual Studio Code można otworzyć aplikację logiki w widoku projektu tylko do odczytu. Chociaż nie można edytować aplikacji logiki w projektancie, można wizualnie sprawdzić przepływ pracy aplikacji logiki przy użyciu widoku projektanta.

W oknie Platformy Azure w obszarze **Aplikacje logiki**otwórz menu skrótów aplikacji logiki i wybierz pozycję **Otwórz w projektancie**.

Projektant tylko do odczytu otwiera się w osobnym oknie i pokazuje przepływ pracy aplikacji logiki, na przykład:

![Wyświetlanie aplikacji logiki w projektancie tylko do odczytu](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Wyświetlanie w witrynie Azure portal

Aby przejrzeć aplikację logiki w witrynie Azure portal, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu tego samego konta platformy Azure i subskrypcji, która jest skojarzona z aplikacją logiki.

1. W polu wyszukiwania witryny Azure portal wprowadź nazwę aplikacji logiki. Z listy wyników wybierz aplikację logiki.

   ![Twoja nowa aplikacja logiki w witrynie Azure portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

W programie Visual Studio Code, jeśli edytujesz opublikowaną aplikację logiki i zapiszesz zmiany, *zastąpisz* już wdrożoną aplikację. Aby uniknąć złamania aplikacji logiki w produkcji i zminimalizować zakłócenia, najpierw dezaktywuj aplikację logiki. Następnie można reaktywne aplikacji logiki po potwierdzenie, że aplikacja logiki nadal działa.

1. Jeśli nie zalogowano się jeszcze do konta platformy Azure i subskrypcji z wewnątrz programu Visual Studio Code, wykonaj [poprzednie kroki, aby zalogować się teraz.](#sign-in-azure)

1. W oknie platformy Azure w obszarze **Aplikacje logiki**rozwiń subskrypcję platformy Azure, aby wyświetlić wszystkie aplikacje logiki w tej subskrypcji.

   1. Aby wyłączyć odpowiednią aplikację logiki, otwórz menu aplikacji logiki i wybierz pozycję **Wyłącz**.

      ![Wyłączanie aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Gdy będziesz gotowy do ponownego aktywowania aplikacji logiki, otwórz menu aplikacji logiki i wybierz pozycję **Włącz**.

      ![Włączanie aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Edytowanie wdrożonej aplikacji logiki

W programie Visual Studio Code można otworzyć i edytować definicję przepływu pracy dla już wdrożonej aplikacji logiki na platformie Azure.

> [!IMPORTANT] 
> Przed edycją aktywnie uruchomionej aplikacji logiki w produkcji należy unikać ryzyka związanego z przerwaniem tej aplikacji logiki i minimalizowanie zakłóceń, [najpierw wyłączając aplikację logiki](#disable-enable-logic-app).

1. Jeśli nie zalogowano się jeszcze do konta platformy Azure i subskrypcji z wewnątrz programu Visual Studio Code, wykonaj [poprzednie kroki, aby zalogować się teraz.](#sign-in-azure)

1. W oknie platformy Azure w obszarze **Aplikacje logiki**rozwiń subskrypcję platformy Azure i wybierz odpowiednią aplikację logiki.

1. Otwórz menu aplikacji logiki i wybierz pozycję **Otwórz w edytorze**. Lub obok nazwy aplikacji logiki wybierz ikonę edycji.

   ![Otwórz edytor dla istniejącej aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Program Visual Studio Code otwiera plik .logicapp.json w lokalnym folderze tymczasowym, dzięki czemu można wyświetlić definicję przepływu pracy aplikacji logiki.

   ![Wyświetlanie definicji przepływu pracy dla opublikowanej aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Wprowadzanie zmian w definicji przepływu pracy aplikacji logiki.

1. Gdy wszystko będzie gotowe, zapisz zmiany. (Menu plik > Zapisz lub naciśnij klawisze Ctrl+S)

1. Po wyświetleniu monitu o przekazanie zmian i *zastąpienie* istniejącej aplikacji logiki w witrynie Azure portal wybierz pozycję **Przekaż**.

   Ten krok publikuje aktualizacje aplikacji logiki w [witrynie Azure portal](https://portal.azure.com).

   ![Przekazywanie zmian do definicji aplikacji logiki na platformie Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Wyświetlanie lub promowanie innych wersji

W programie Visual Studio Code można otworzyć i przejrzeć wcześniejsze wersje aplikacji logiki. Można również podwyższyć wcześniejszą wersję do bieżącej wersji.

> [!IMPORTANT] 
> Przed zmianą aktywnie uruchomionej aplikacji logiki w produkcji należy unikać ryzyka związanego z przerwaniem tej aplikacji logiki i minimalizowanie zakłóceń, [najpierw wyłączając aplikację logiki.](#disable-enable-logic-app)

1. W oknie platformy Azure w obszarze **Aplikacje logiki**rozwiń subskrypcję platformy Azure, aby wyświetlić wszystkie aplikacje logiki w tej subskrypcji.

1. W ramach subskrypcji rozwiń aplikację logiki i rozwiń **pozycję Wersje**.

   Lista **Wersje** zawiera wcześniejsze wersje aplikacji logiki, jeśli istnieją.

   ![Poprzednie wersje aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Aby wyświetlić wcześniejszą wersję, wybierz jeden z tych kroków:

   * Aby wyświetlić definicję JSON, w obszarze **Wersje**wybierz numer wersji dla tej definicji. Możesz też otworzyć menu skrótów tej wersji i wybrać **pozycję Otwórz w edytorze**.

     Nowy plik zostanie otwarty na komputerze lokalnym i pokazuje definicję JSON tej wersji.

   * Aby wyświetlić wersję w widoku projektanta tylko do odczytu, otwórz menu skrótów tej wersji i wybierz pozycję **Otwórz w projektancie**.

1. Aby podwyższyć wcześniejszą wersję do bieżącej wersji, wykonaj następujące kroki:

   1. W obszarze **Wersje**otwórz menu skrótów wcześniejszej wersji i wybierz pozycję **Promuj**.

      ![Podwyższyj wcześniejszą wersję](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Aby kontynuować po wyświetleniu monitu o potwierdzenie kodu programu Visual Studio, wybierz opcję **Tak**.

      ![Potwierdź promowanie wcześniejszej wersji](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code promuje wybraną wersję do bieżącej wersji i przypisuje nowy numer do wersji promowanej. Poprzednia bieżąca wersja jest teraz wyświetlana w wersji promowanej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)