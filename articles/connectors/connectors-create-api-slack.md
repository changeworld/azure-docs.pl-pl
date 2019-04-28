---
title: Nawiązywanie połączenia Slack z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorował pliki i zarządzać kanałów, grup i komunikaty na koncie usługi Slack za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105653"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorowanie i zarządzanie nimi Slack przy użyciu usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznika usługi Slack można utworzyć automatycznych zadań i przepływów pracy, które monitorują Slack plików i zarządzać kanałach Slack, wiadomości, grup i tak dalej, na przykład:

* Monitor, gdy zostaną utworzone nowe pliki.
* Utwórz listę i Dołącz do kanałów 
* Opublikuj wiadomości.
* Tworzenie grup i nie przeszkadzać zestawu.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z konta usługi Slack i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonują zadania przy użyciu konta Slack. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji Slack. Na przykład gdy nowy plik zostanie utworzony, możesz wysłać wiadomość e-mail za pośrednictwem łącznika Office 365 Outlook. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Twoje [Slack](https://slack.com/) konta i poświadczenia użytkownika

  Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do konta programu Slack.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Slack. Można uruchomić z wyzwalaczem Slack [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć Slack akcji, uruchom aplikację logiki z wyzwalaczem, takich jak Slack wyzwalacza lub innym, takich jak **cyklu** wyzwalacza.

## <a name="connect-to-slack"></a>Nawiązać połączenie z Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W przypadku aplikacji logiki puste w polu wyszukiwania wprowadź "slack" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

   — lub —

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**. 
   W polu wyszukiwania wprowadź "slack" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Jeśli zostanie wyświetlony monit, aby zarejestrować się w usłudze Slack, zaloguj się do obszaru roboczego usługi Slack. 

   ![Zaloguj się w usłudze Slack obszaru roboczego](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autoryzowanie dostępu dla aplikacji logiki.

   ![Autoryzowanie dostępu do Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji. Aby kontynuować tworzenie przepływu pracy aplikacji logiki, Dodaj więcej akcji.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/slack/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
