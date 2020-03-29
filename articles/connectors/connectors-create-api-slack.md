---
title: Łączenie się z slackem z aplikacji logiki azure
description: Automatyzowanie zadań i przepływów pracy monitorujących pliki i zarządzania kanałami, grupami i wiadomościami na koncie Slack przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789175"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorowanie slacka i zarządzanie nim za pomocą aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika Slack można tworzyć zautomatyzowane zadania i przepływy pracy, które monitorują pliki Slack i zarządzają kanałami Slack, wiadomościami, grupami i tak dalej, na przykład:

* Monitoruj, kiedy tworzone są nowe pliki.
* Tworzenie, wyświetlanie listy i dołączanie kanałów 
* Publikowanie wiadomości.
* Tworzenie grup i zestaw nie przeszkadzać.

Można użyć wyzwalaczy, które otrzymują odpowiedzi z konta Slack i udostępnić dane wyjściowe do innych akcji. Można użyć akcji, które wykonują zadania z kontem Slack. Można również mieć inne akcje użyć danych wyjściowych z Slack akcji. Na przykład po utworzeniu nowego pliku można wysyłać wiadomości e-mail za pomocą łącznika programu Outlook usługi Office 365. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto [Slack](https://slack.com/) i poświadczenia użytkownika

  Poświadczenia autoryzować aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta Slack.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Slack. Aby rozpocząć od wyzwalacza Slack, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Slack, uruchom aplikację logiki z wyzwalaczem, takim jak wyzwalacz Slack lub inny wyzwalacz, taki jak wyzwalacz **cyklu.**

## <a name="connect-to-slack"></a>Łączenie się z slackem

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "luz" jako filtr. W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 
   W polu wyszukiwania wpisz "slack" jako filtr. 
   W obszarze listy akcje wybierz odpowiednią akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do aplikacji Slack, zaloguj się do obszaru roboczego Slack. 

   ![Zaloguj się do obszaru roboczego Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autoryzuj dostęp do aplikacji logiki.

   ![Autoryzuj dostęp do Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Podaj niezbędne szczegóły dla wybranego wyzwalacza lub akcji. Aby kontynuować tworzenie przepływu pracy aplikacji logiki, dodaj więcej akcji.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/slack/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
