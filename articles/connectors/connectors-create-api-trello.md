---
title: Łączenie się z aplikacjami Trello z usługi Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy monitorujące listy, tablice i karty oraz zarządzające nimi w projektach Trello przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789141"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorowanie trello i zarządzanie nim za pomocą aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika Trello można tworzyć zautomatyzowane zadania i przepływy pracy, które monitorują listy, karty, karty, tablice, członków zespołu i tak dalej, na przykład:

* Monitoruj, kiedy nowe karty są dodawane do tablic i list. 
* Twórz tablice, karty i listy, otrzymyuj je i zarządzaj nimi.
* Dodaj komentarze i członków do kart.
* Tablice list, etykiety tablic, karty na tablicach, komentarze do kart, członkowie kart, członkowie zespołu i zespoły, w których jesteś członkiem. 
* Zdobądź zespoły.

Możesz użyć wyzwalaczy, które otrzymują odpowiedzi z twojego konta Trello i udostępniają dane wyjściowe innym działaniom. Możesz użyć akcji, które wykonują zadania z twoim kontem Trello. Można również mieć inne akcje użyć danych wyjściowych z trello akcji. Na przykład po dodaniu nowej karty do tablicy lub listy można wysyłać wiadomości za pomocą łącznika Slack. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto Trello i poświadczenia użytkownika

  Poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta Trello.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Trello. Aby rozpocząć od wyzwalacza Trello, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Trello, uruchom aplikację logiki za pomocą wyzwalacza, na przykład wyzwalacza **cyklu.**

## <a name="connect-to-trello"></a>Połącz się z Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logicznych w polu wyszukiwania wpisz "trello" jako filtr. W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 
   W polu wyszukiwania wpisz "trello" jako filtr. 
   W obszarze listy akcje wybierz odpowiednią akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do aplikacji Trello, zaprośuj dostęp do aplikacji logiki i zaloguj się.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/trello/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)