---
title: Łączenie z usługą Trello z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorują i zarządzać list, tablic i karty w projektach usługi Trello za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106171"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorowanie i zarządzanie nimi w usłudze Trello z usługą Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik usługi Trello można utworzyć zautomatyzowanym zadaniom i przepływów pracy, które monitorowanie i zarządzanie nimi listy usługi Trello, karty, tablice, członkowie zespołu i tak dalej, na przykład:

* Monitor po dodaniu nowej karty do tablice i listy. 
* Tworzenie, pobieranie i zarządzanie list, karty i tablice.
* Dodawanie komentarzy i elementów członkowskich, do kart.
* Wyświetl listę tablic, etykiety tablicy, kart na tablicach, komentarze karty, użytkowników karty, członków zespołu i zespoły, których jesteś członkiem. 
* Uzyskaj zespołów.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z Twojego konta usługi Trello i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonują zadania z Twoim kontem usługi Trello. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji usługi Trello. Na przykład gdy nowa karta jest dodawana do tablicy lub listy, możesz wysłać wiadomości z łącznikiem usługi Slack. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Poświadczenia konta i użytkownik usługi Trello

  Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta usługi Trello.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Trello. Aby uruchomić z wyzwalaczem usługi Trello [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Trello, uruchom aplikację logiki z wyzwalaczem, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-trello"></a>Łączenie z usługą Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W przypadku aplikacji logiki puste w polu wyszukiwania wprowadź "trello" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

   — lub —

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**. 
   W polu wyszukiwania wprowadź "trello" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Jeśli zostanie wyświetlony monit, aby zarejestrować się w usłudze Trello, autoryzowanie dostępu dla aplikacji logiki i zaloguj się.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/trello/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)