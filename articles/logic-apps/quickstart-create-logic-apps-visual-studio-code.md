---
title: Tworzenie zautomatyzowanych przepływów pracy i zarządzanie nimi za pomocą Visual Studio Code-Azure Logic Apps
description: Przewodnik Szybki Start dotyczący tworzenia aplikacji logiki i zarządzania nimi za pomocą notacji JSON w Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: f0b568289a50b9883e6e0f1cba6b793c9e6e90e8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051730"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Szybki start: Tworzenie automatycznych przepływów pracy aplikacji logiki i zarządzanie nimi — Visual Studio Code

Dzięki [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i Visual Studio Code można tworzyć i zarządzać aplikacjami logiki, które ułatwiają automatyzację zadań, przepływów pracy i procesów integracji aplikacji, danych, systemów i usług w różnych organizacjach i przedsiębiorstwach. Ten przewodnik Szybki Start przedstawia sposób tworzenia i edytowania definicji przepływu pracy aplikacji logiki przez pracę z schematem definicji przepływu pracy w JavaScript Object Notation (JSON) za pośrednictwem środowiska opartego na kodzie. Możesz również korzystać z istniejących aplikacji logiki, które są już wdrożone na platformie Azure. 

Chociaż można wykonać te same zadania w Azure Portal (https://portal.azure.com) i w programie Visual Studio, można szybciej rozpocząć pracę w Visual Studio Code, gdy znasz już definicje aplikacji logiki i chcesz pracować bezpośrednio w kodzie. Można na przykład wyłączyć, włączyć, usunąć i odświeżać już utworzone aplikacje logiki. Ponadto można korzystać z usługi Logic Apps i kont integracji z dowolnego platformy deweloperskiej, w której działa Visual Studio Code, takich jak Linux, Windows i Mac.

W tym artykule można utworzyć tę samą aplikację logiki jak w [przewodniku szybki start dotyczący tworzenia aplikacji logiki w Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), która koncentruje się na założeniach podstawowych. W Visual Studio Code aplikacja logiki wygląda podobnie do tego przykładu:

![Ukończona aplikacja logiki](./media/create-logic-apps-visual-studio-code/overview.png)

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [definicji przepływu pracy aplikacji logiki](../logic-apps/logic-apps-workflow-definition-language.md) i ich struktury, która używa JavaScript Object Notation (JSON) 

  Jeśli dopiero zaczynasz Logic Apps, wypróbuj Przewodnik Szybki Start, który przeprowadzi Cię przez proces [tworzenia pierwszej aplikacji logiki w Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), co pozwala uzyskać więcej informacji na temat podstawowych pojęć. 

* Dostęp do sieci Web w celu logowania się do platformy Azure i subskrypcji platformy Azure

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz: 

  * [Visual Studio Code wersja 1.25.1 lub nowsza]("https://code.visualstudio.com/), która jest bezpłatna

  * Visual Studio Code rozszerzenie dla Azure Logic Apps

    To rozszerzenie można pobrać i zainstalować z poziomu [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) lub bezpośrednio z poziomu Visual Studio Code. 
    Przed zainstalowaniem upewnij się, że Załaduj ponownie Visual Studio Code. 

    ![Znajdź "Visual Studio Code rozszerzenie dla Azure Logic Apps"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Aby sprawdzić, czy rozszerzenie zostało poprawnie zainstalowane, ikona platformy Azure pojawia się na pasku narzędzi Visual Studio Code. 

    ![Rozszerzenie zainstalowane](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Aby uzyskać więcej informacji, zobacz temat [rozszerzanie witryny Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Możesz również wyświetlić i przesłać wkład do wersji "open source" tego rozszerzenia, odwiedzając [rozszerzenie Azure Logic Apps dla Visual Studio Code w witrynie GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Otwórz program Visual Studio Code. Na pasku narzędzi Visual Studio Code wybierz ikonę platformy Azure. 

   ![Wybierz ikonę platformy Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. W oknie Azure w obszarze **Logic Apps**wybierz pozycję **Zaloguj się do platformy Azure**. 

   ![Wybierz pozycję "Zaloguj się do platformy Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Teraz zostanie wyświetlony monit o zalogowanie się przy użyciu podanego kodu uwierzytelniania. 

1. Skopiuj kod uwierzytelniania, a następnie wybierz polecenie **kopiuj & Otwórz**, co spowoduje otwarcie nowego okna przeglądarki.

   ![Monit logowania](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Wprowadź kod uwierzytelniania. Po wyświetleniu monitu wybierz pozycję **Kontynuuj**.

   ![Wprowadź kod](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Wybierz swoje konto platformy Azure. Po zalogowaniu możesz zamknąć przeglądarkę i wrócić do Visual Studio Code.

   W oknie platformy Azure okienko Logic Apps i konta integracji zawierają teraz subskrypcje platformy Azure na Twoim koncie. 

   ![Wybieranie subskrypcji](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Jeśli nie widzisz oczekiwanych subskrypcji, obok pozycji etykieta **Logic Apps** wybierz **pozycję Wybierz subskrypcje** (ikona filtru). Znajdź i wybierz subskrypcje, których chcesz użyć.

1. Aby wyświetlić wszystkie istniejące aplikacje logiki lub konta integracji w ramach subskrypcji platformy Azure, rozwiń swoją subskrypcję.

   ![Wyświetlanie aplikacji logiki i kont integracji](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

1. Jeśli nie zalogowano się w ramach subskrypcji platformy Azure z poziomu Visual Studio Code, wykonaj kroki opisane w tym artykule, aby [zalogować się teraz](#sign-in-azure).

1. W menu kontekstowym subskrypcji wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Utwórz"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Z listy, która zawiera grupy zasobów platformy Azure w ramach subskrypcji, wybierz istniejącą grupę zasobów lub **Utwórz nową grupę zasobów**. 

   Ten przykład tworzy nową grupę zasobów:

   ![Utwórz nową grupę zasobów](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Podaj nazwę grupy zasobów platformy Azure, a następnie naciśnij klawisz ENTER.

   ![Nadaj nazwę grupie zasobów](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Wybierz lokalizację centrum danych, dla którego chcesz zapisać metadane aplikacji logiki.

   ![Wybierz lokalizację](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Podaj nazwę aplikacji logiki, a następnie naciśnij klawisz ENTER.

   ![Nazwij aplikację logiki](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Nowa aplikacja logiki zostanie teraz wyświetlona w oknie platformy Azure w ramach subskrypcji platformy Azure. Teraz można rozpocząć tworzenie definicji przepływu pracy aplikacji logiki.

1. Z menu skrótów aplikacji logiki wybierz pozycję **Otwórz w edytorze**. 

   ![Otwórz aplikację logiki w edytorze](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code otwiera szablon definicji przepływu pracy aplikacji logiki (plik. logicapp. JSON), dzięki czemu można rozpocząć tworzenie przepływu pracy aplikacji logiki.

   ![Nowa definicja przepływu pracy aplikacji logiki](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. W pliku szablonu definicji przepływu pracy aplikacji logiki Rozpocznij tworzenie definicji przepływu pracy aplikacji logiki. Aby uzyskać informacje techniczne, zobacz [schemat języka definicji przepływu pracy dla Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Oto przykładowa definicja logiki. Zazwyczaj elementy JSON są wyświetlane alfabetycznie w poszczególnych sekcjach, ale ten przykład pokazuje te elementy w kolejności, w jakiej są wyświetlane kroki aplikacji logiki w projektancie.

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
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Gdy skończysz, Zapisz plik definicji aplikacji logiki. Gdy Visual Studio Code zostanie wyświetlony komunikat z prośbą o potwierdzenie przekazania definicji aplikacji logiki do subskrypcji platformy Azure, wybierz pozycję **Przekaż**.

   ![Przekazywanie nowej aplikacji logiki](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Gdy Visual Studio Code opublikuje aplikację logiki na platformie Azure, możesz teraz znaleźć aplikację na żywo i uruchomić ją w Azure Portal. 

   ![Aplikacja logiki opublikowana w Azure Portal](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Edytuj aplikację logiki

Aby móc korzystać z istniejącej aplikacji logiki, która została już wdrożona na platformie Azure, możesz otworzyć plik definicji przepływu pracy tej aplikacji w Visual Studio Code.

1. Jeśli nie zalogowano się w ramach subskrypcji platformy Azure z poziomu Visual Studio Code, wykonaj kroki opisane w tym artykule, aby [zalogować się teraz](#sign-in-azure).

1. W oknie platformy Azure w obszarze **Logic Apps**Rozwiń swoją subskrypcję platformy Azure i wybierz żądaną aplikację logiki. 

1. Z menu aplikacji logiki wybierz pozycję **Otwórz w edytorze**. Lub obok nazwy aplikacji logiki wybierz ikonę edycji.

   ![Otwieranie edytora dla istniejącej aplikacji logiki](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code otwiera plik. logicapp. JSON dla definicji przepływu pracy aplikacji logiki.

   ![Otwarto definicję przepływu pracy aplikacji logiki](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Wprowadź zmiany w definicji aplikacji logiki.

1. Gdy wszystko będzie gotowe, zapisz zmiany.

1. Gdy Visual Studio Code zostanie wyświetlony komunikat z prośbą o aktualizację definicji aplikacji logiki w ramach subskrypcji platformy Azure, wybierz pozycję **Przekaż**. 

   ![Przekazywanie zmian](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> * [Tworzenie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)