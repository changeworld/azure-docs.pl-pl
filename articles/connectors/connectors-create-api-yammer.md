---
title: Łączenie z usługą Yammer z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorowanie, post i zarządzać wiadomości, kanałów informacyjnych i nie tylko w usłudze Yammer za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104735"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorowanie i zarządzanie kontem usługi Yammer za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik usługi Yammer można utworzyć zautomatyzowanym zadaniom i przepływów pracy, które monitorowanie i zarządzanie nimi wiadomości, kanałów informacyjnych i nie tylko na Twoim koncie usługi Yammer oraz inne czynności, na przykład:

* Monitor, gdy nowe komunikaty pojawią się w obserwowane kanały i grupy.
* Pobieranie wiadomości, grup, sieci i szczegóły dotyczące użytkowników.
* Opublikuj i komunikaty, takie jak.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z Twojego konta usługi Yammer i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonują zadania z Twoim kontem usługi Yammer. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji usługi Yammer. Na przykład gdy nowe komunikaty pojawią się w oknie źródła danych lub grupy, możesz udostępniać te komunikaty z łącznikiem usługi Slack. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Poświadczenia konta i użytkownika usługi Yammer

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta usługi Yammer.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Yammer. Aby uruchomić z wyzwalaczem usługi Yammer [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Yammer, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-yammer"></a>Łączenie z usługą Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * Puste logic apps, w polu wyszukiwania wprowadź "yammer" jako filtr. 
   W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

     — lub —

   * W przypadku istniejących aplikacji logiki: 
   
     * W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**. 

       — lub —

     * Między krokami, które chcesz dodać akcję wskaźnik myszy nad strzałką znajdującą się między krokami. 
     Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź "yammer" jako filtr. 
       W obszarze listy akcji wybierz akcję, którą chcesz.

1. Jeśli zostanie wyświetlony monit, aby zarejestrować się w usłudze Yammer, zaloguj się teraz logowania teraz, dzięki czemu można zezwolić na dostęp.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/yammer/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)