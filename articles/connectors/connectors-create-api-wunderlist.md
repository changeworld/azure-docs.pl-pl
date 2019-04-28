---
title: Łączenie z usługą Wunderlist z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorowanie i zarządzanie nimi list, zadania, przypomnienia i tylko na Twoim koncie w aplikacji Wunderlist za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105670"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorowanie i zarządzanie nimi aplikacji Wunderlist za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik usługi Wunderlist można utworzyć zautomatyzowanym zadaniom i przepływy pracy umożliwiające monitorowanie i zarządzanie listami zadań do wykonania, zadania, przypomnienia i więcej ze swojego konta usługi Wunderlist, oraz inne czynności, na przykład:

* Monitor, gdy utworzone nowe zadania, gdy zadania są należne lub się zdarzyć, przypomnienia.
* Tworzenie i zarządzanie nimi, list, notatki, zadania i podzadania.
* Ustaw przypomnienia.
* Pobieranie listy, zadań, podzadań, przypomnienia, pliki, uwagi, komentarze i więcej.

[Wunderlist](https://www.wunderlist.com/) to usługa, która ułatwia planowanie, zarządzanie i zakończyć swoje projekty, listy zadań do wykonania i zadania — w dowolnym miejscu i urządzenia. Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z Twojego konta w usłudze Wunderlist i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonują zadania z Twoim kontem usługi Wunderlist. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji aplikacji Wunderlist. Na przykład gdy nowe zadania są wymagane, możesz zadać wiadomości z łącznikiem usługi Slack. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Poświadczenia konta i użytkownik aplikacji Wunderlist

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta usługi Wunderlist.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Yammer. Można uruchomić z wyzwalaczem usługi Wunderlist [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć działania programu Wunderlist, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-wunderlist"></a>Łączenie z usługą Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * W przypadku aplikacji logiki puste w polu wyszukiwania wprowadź "wunderlist" jako filtr. 
   W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

     — lub —

   * W przypadku istniejących aplikacji logiki: 
   
     * W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**. 

       — lub —

     * Między krokami, które chcesz dodać akcję wskaźnik myszy nad strzałką znajdującą się między krokami. 
     Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź "wunderlist" jako filtr. 
       W obszarze listy akcji wybierz akcję, którą chcesz.

1. Jeśli zostanie wyświetlony monit logować się do aplikacji Wunderlist, zarejestruj się teraz, dzięki czemu można zezwolić na dostęp.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/wunderlist/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)