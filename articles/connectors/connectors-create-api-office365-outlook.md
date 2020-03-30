---
title: Łączenie z usługą Office 365 Outlook
description: Automatyzuj zadania i przepływy pracy, które zarządzają pocztą e-mail, kontaktami i kalendarzami w usłudze Office 365 Outlook przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732713"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Zarządzanie pocztą e-mail, kontaktami i kalendarzami w programie Outlook usługi Office 365 przy użyciu usługi Azure Logic Apps

Za [pomocą aplikacji Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika programu Office 365 outlook](/connectors/office365connector/)można tworzyć zautomatyzowane zadania i przepływy pracy, które zarządzają kontem usługi Office 365, tworząc aplikacje logiki. Na przykład można zautomatyzować następujące zadania:

* Otrzyj, wysyłaj i odpowiadaj na wiadomości e-mail. 
* Planowanie spotkań w kalendarzu.
* Dodawanie i edytowanie kontaktów. 

Można użyć dowolnego wyzwalacza, aby uruchomić przepływ pracy, na przykład po nadejściu nowej wiadomości e-mail, gdy element kalendarza jest aktualizowany lub gdy zdarzenie ma miejsce w usłudze różnicy, takiej jak Salesforce. Można użyć akcji, które reagują na zdarzenie wyzwalacza, na przykład wysłać wiadomość e-mail lub utworzyć nowe zdarzenie kalendarza. 

> [!NOTE]
> Aby zautomatyzować zadania @outlook.com @hotmail.com dla lub konta, użyj [łącznika Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto usługi Office 365](https://www.office.com/)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta programu Office 365 Outlook. Aby uruchomić przepływ pracy za pomocą wyzwalacza programu Outlook usługi Office 365, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby dodać akcję programu Outlook usługi Office 365 do przepływu pracy, aplikacja logiki musi już mieć wyzwalacz.

## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

[Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest zdarzeniem, które uruchamia przepływ pracy w aplikacji logiki. W tym przykładzie aplikacja logiki używa wyzwalacza "sondowania", który sprawdza, czy na koncie e-mail jest aktualizowane zdarzenie kalendarza na podstawie określonego interwału i częstotliwości.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz pustą aplikację logiki w projektancie aplikacji logiki.

1. W polu wyszukiwania `office 365 outlook` wprowadź jako filtr. W tym przykładzie **wybieramy, kiedy wkrótce rozpocznie się nadchodzące wydarzenie**.
   
   ![Wybierz wyzwalacz, aby uruchomić aplikację logiki](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj poświadczenia usługi Office 365, aby aplikacja logiki mogła połączyć się z Twoim kontem. W przeciwnym razie jeśli połączenie już istnieje, podaj informacje dotyczące właściwości wyzwalacza.

   W tym przykładzie wybiera kalendarz, który sprawdza wyzwalacz, na przykład:

   ![Konfigurowanie właściwości wyzwalacza](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. W wyzwalaczu ustaw wartości **częstotliwość** i **interwał.** Aby dodać inne dostępne właściwości wyzwalacza, takie jak **strefa czasowa,** wybierz te właściwości z listy **Dodaj nowy parametr.**

   Na przykład, jeśli wyzwalacz ma sprawdzać kalendarz co 15 minut, ustaw `15` **częstotliwość** na **Minuta**i ustaw **interwał** na . 

   ![Ustawianie częstotliwości i interwału wyzwalacza](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Teraz dodaj akcję, która działa po uruchomieniu wyzwalacza. Na przykład można dodać akcję **wysyłania wiadomości** usługi Twilio, która wysyła tekst, gdy zdarzenie kalendarza rozpoczyna się w ciągu 15 minut.

## <a name="add-an-action"></a>Dodawanie akcji

[Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest operacją, która jest uruchamiana przez przepływ pracy w aplikacji logiki. W tym przykładowym przykładzie aplikacja logiki tworzy nowy kontakt w usłudze Office 365 Outlook. Można użyć danych wyjściowych z innego wyzwalacza lub akcji, aby utworzyć kontakt. Załóżmy na przykład, że aplikacja logiki używa wyzwalacza Dynamics 365, **Po utworzeniu rekordu**. Możesz dodać akcję kontaktu Tworzenie **kontaktów** programu Office 365 Outlook i użyć danych wyjściowych z wyzwalacza SalesForce, aby utworzyć nowy kontakt.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. Aby dodać akcję jako ostatni krok w przepływie pracy, wybierz pozycję **Nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między tymi krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania `office 365 outlook` wprowadź jako filtr. W tym przykładzie wybiera **pozycję Utwórz kontakt**.

   ![Wybierz akcję do uruchomienia w aplikacji logiki](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj poświadczenia usługi Office 365, aby aplikacja logiki mogła połączyć się z Twoim kontem. W przeciwnym razie, jeśli połączenie już istnieje, podaj informacje dotyczące właściwości akcji.

   W tym przykładzie wybiera folder kontaktów, w którym akcja tworzy nowy kontakt, na przykład:

   ![Konfigurowanie właściwości akcji](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Aby dodać inne dostępne właściwości akcji, wybierz te właściwości z listy **Dodaj nowy parametr.**

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, jak opisano w pliku Swagger [łącznika,](/connectors/office365connector/)zobacz stronę odwołania łącznika . 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
