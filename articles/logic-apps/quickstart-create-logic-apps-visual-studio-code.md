---
title: Tworzenie i zarządzanie nimi zautomatyzowane przepływy pracy za pomocą programu Visual Studio Code — Azure Logic Apps | Dokumentacja firmy Microsoft
description: 'Szybki Start: tworzenie i zarządzanie aplikacjami logiki w formacie JSON w programie Visual Studio Code (VS Code)'
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60512247"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Szybki start: Tworzenie i zarządzanie nimi przepływów pracy z aplikacji zautomatyzowanej logiki — Visual Studio Code

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i programu Visual Studio Code, możesz tworzyć i zarządzać aplikacje logiki, które ułatwiają Automatyzowanie zadań, przepływy pracy i procesy integrowania aplikacji, danych, systemów i usług w organizacji i przedsiębiorstw. Ten przewodnik Szybki Start przedstawia sposób tworzenia i edycji definicji przepływu pracy aplikacji logiki, pracując ze schematem definicji przepływu pracy w JavaScript Object Notation (JSON) za pośrednictwem środowiska pracy opartego na kodzie. Można również pracować na istniejących aplikacji logiki już wdrożone <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> w chmurze. 

Chociaż można wykonać te same czynności w <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a> i w programie Visual Studio, możesz szybciej rozpocząć pracę w programie Visual Studio Code, gdy znasz już definicji aplikacji logiki i chcą pracować bezpośrednio w kodzie. Na przykład można wyłączyć, włączyć, Usuń i odświeżanie aplikacji logiki zostały już utworzone. Ponadto można pracować nad logic apps i konta integracji z dowolnej platformy programowania, w którym jest uruchamiany program Visual Studio Code, takie jak Linux, Windows i Mac.

W tym artykule, można utworzyć sama aplikacja logiki, podobnie jak w [Szybki Start: tworzenie aplikacji logiki w witrynie Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), który poświęcona jest bardziej podstawowe pojęcia. W programie Visual Studio Code aplikacja logiki będzie wyglądać podobnie jak w tym przykładzie:

![Ukończona aplikacja logiki](./media/create-logic-apps-visual-studio-code/overview.png)

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawową wiedzę na temat o [definicji przepływu pracy aplikacji logiki](../logic-apps/logic-apps-workflow-definition-language.md) i ich struktury, która używa języka JavaScript Object Notation (JSON) 

  Jeśli dopiero zaczynasz pracę z usługi Logic Apps, spróbuj tego przewodnika Szybki Start przeprowadzi Cię przez [jak utworzyć swoją pierwszą aplikację logiki w witrynie Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), który poświęcona jest bardziej podstawowe pojęcia. 

* Dostęp do sieci web do logowania się na platformie Azure i Twojej subskrypcji platformy Azure

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz: 

  * <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code w wersji 1.25.1 lub nowszej</a>, które jest bezpłatne

  * Rozszerzenie programu Visual Studio Code dla usługi Azure Logic Apps

    Możesz pobrać i zainstalować to rozszerzenie z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) lub bezpośrednio z wewnątrz programu Visual Studio Code. 
    Upewnij się, że po zainstalowaniu ponownym załadowaniu programu Visual Studio Code. 

    ![Znajdź "Rozszerzenie programu Visual Studio Code dla usługi Azure Logic Apps"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Do Sprawdź, czy rozszerzenie zainstalowane poprawnie, Azure ikona pojawia się w pasku narzędzi programu Visual Studio Code. 

    ![Zainstalowane rozszerzenie](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Aby uzyskać więcej informacji, zobacz <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Portal Marketplace z rozszerzeniami</a>. Możesz również wyświetlić i wkłady do wersji typu open source to rozszerzenie, odwiedzając [rozszerzenia usługi Azure Logic Apps for Visual Studio Code w serwisie GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Otwórz program Visual Studio Code. Na pasku narzędzi programu Visual Studio Code wybierz ikonę platformy Azure. 

   ![Wybierz ikonę platformy Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. W oknie platformy Azure w ramach **Logic Apps**, wybierz opcję **logowanie do platformy Azure**. 

   ![Wybierz pozycję "Sign in Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Teraz wyświetlony monit logowania przy użyciu kodu podanego uwierzytelniania. 

1. Skopiuj kod uwierzytelniania, a następnie wybierz **Kopiuj & Otwórz**, która otwiera nowe okno przeglądarki.

   ![Monit logowania](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Wprowadź kod uwierzytelniania. Po wyświetleniu monitu wybierz **Kontynuuj**.

   ![Wprowadź kod](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Wybierz swoje konto platformy Azure. Po zalogowaniu, zamknij przeglądarkę i powrócić do programu Visual Studio Code.

   W oknie usługi Azure Logic Apps okienka i kont integracji pokazują teraz subskrypcji platformy Azure w ramach Twojego konta. 

   ![Wybieranie subskrypcji](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Jeśli nie widzisz subskrypcji oczekujesz, obok **Logic Apps** etykietę, wybierz **subskrypcje wybierz** (ikona filtru). Znajdź i wybierz subskrypcję, która ma.

1. Aby wyświetlić istniejących aplikacji logiki ani konta integracji w Twojej subskrypcji platformy Azure, rozwiń Twojej subskrypcji.

   ![Wyświetl logic apps i konta integracji](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

1. Jeśli jeszcze nie zalogowano się do subskrypcji platformy Azure z wewnątrz programu Visual Studio Code, postępuj zgodnie z instrukcjami w tym artykule, aby [Zaloguj się teraz](#sign-in-azure).

1. Z menu kontekstowego swoją subskrypcję, wybierz **Utwórz**.

   ![Wybierz pozycję "Utwórz"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Z listy, która wyświetla grupy zasobów platformy Azure w ramach subskrypcji, wybierz istniejącą grupę zasobów lub **Utwórz nową grupę zasobów**. 

   W tym przykładzie tworzy nową grupę zasobów:

   ![Utwórz nową grupę zasobów](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Podaj nazwę grupy zasobów platformy Azure, a następnie naciśnij klawisz ENTER.

   ![Określ nazwę grupy zasobów](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Wybierz lokalizację centrum danych, w którym można zapisać metadanych aplikacji logiki.

   ![Wybierz lokalizację](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Podaj nazwę aplikacji logiki, a następnie naciśnij klawisz ENTER.

   ![Nadaj nazwę aplikacji logiki](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Nową aplikację logiki pojawi się w oknie platformy Azure w ramach Twojej subskrypcji platformy Azure. Teraz możesz rozpocząć tworzenie definicji przepływu pracy aplikacji logiki.

1. Twoja aplikacja logiki menu skrótów i wybierz polecenie **otwarty w edytorze**. 

   ![Aplikacja logiki otwarte w edytorze](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code otwiera szablon definicji przepływu pracy aplikacji logiki (. plik logicapp.json), dzięki czemu możesz rozpocząć tworzenie przepływu pracy aplikacji logiki.

   ![Nową definicję przepływu pracy aplikacji logiki](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. W pliku szablonu logiki aplikacji przepływu pracy definicji start tworzenia definicji przepływu pracy aplikacji logiki. Aby uzyskać informacje techniczne, zobacz [schemat języka definicji przepływu pracy dla usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Oto przykład definicji logiki. Zwykle JSON elementy są wyświetlane w kolejności alfabetycznej w każdej sekcji, ale w tym przykładzie pokazano te elementy mniej więcej w kolejności kroków aplikacji logiki są wyświetlane w projektancie.

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

1. Gdy wszystko będzie gotowe, Zapisz plik definicji aplikacji logiki. Kiedy wyświetli monit o potwierdzenie, przekazywanie definicji aplikacji logiki do subskrypcji platformy Azure wybierz pozycję Visual Studio Code **przekazywanie**.

   ![Przekaż nową aplikację logiki](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Po programie Visual Studio Code publikuje aplikację logiki na platformie Azure, możesz znaleźć aplikacji, teraz na żywo i uruchomione w witrynie Azure portal. 

   ![Aplikacja logiki została opublikowana w witrynie Azure portal](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Edytowanie aplikacji logiki

Do pracy aplikacji logiki, która została już wdrożona na platformie Azure, można otworzyć plik definicji przepływu pracy dla tej aplikacji w programie Visual Studio Code.

1. Jeśli jeszcze nie zalogowano się do subskrypcji platformy Azure z wewnątrz programu Visual Studio Code, postępuj zgodnie z instrukcjami w tym artykule, aby [Zaloguj się teraz](#sign-in-azure).

1. W oknie platformy Azure w ramach **Logic Apps**, rozwinąć swoją subskrypcję platformy Azure i wybierz aplikację logiki. 

1. W menu aplikacji logiki, wybierz opcję **otwarty w edytorze**. Lub, obok nazwy aplikacji logiki, wybierz ikonę edycji.

   ![Otwórz edytor dla istniejących aplikacji logiki](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Zostanie otwarty w programie Visual Studio Code. plik logicapp.json dla definicji przepływu pracy aplikacji logiki.

   ![Definicja przepływu pracy aplikacji logiki otwarte](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Wprowadź zmiany w definicji aplikacji logiki.

1. Gdy wszystko będzie gotowe, zapisz zmiany.

1. Gdy program Visual Studio Code jest wyświetlany monit o aktualizacji definicji aplikacji logiki w ramach subskrypcji platformy Azure, wybierz pozycję **przekazywanie**. 

   ![Przekazywanie zmian](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="https://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

