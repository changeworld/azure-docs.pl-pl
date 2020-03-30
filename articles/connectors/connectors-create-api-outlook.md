---
title: Łączenie się z Outlook.com
description: Automatyzuj zadania i przepływy pracy, które zarządzają pocztą e-mail, kalendarzami i kontaktami w Outlook.com za pomocą usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707190"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Zarządzanie pocztą e-mail, kalendarzami i kontaktami w Outlook.com przy użyciu aplikacji Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika Outlook.com](/connectors/outlook/)można tworzyć zautomatyzowane zadania i przepływy pracy, które zarządzają Twoim @outlook.com lub @hotmail.com kontem, tworząc aplikacje logiki. Na przykład można zautomatyzować następujące zadania:

* Otrzyj, wysyłaj i odpowiadaj na wiadomości e-mail.
* Planowanie spotkań w kalendarzu.
* Dodawanie i edytowanie kontaktów.

Można użyć dowolnego wyzwalacza, aby uruchomić przepływ pracy, na przykład, gdy pojawia się nowa wiadomość e-mail, gdy element kalendarza jest aktualizowany lub gdy zdarzenie odbywa się w usłudze różnicy. Można użyć akcji, które reagują na zdarzenie wyzwalacza, na przykład wysłać wiadomość e-mail lub utworzyć nowe zdarzenie kalendarza.

> [!NOTE]
> Aby zautomatyzować zadania dla konta @fabrikam.onmicrosoft.comsłużbowego firmy Microsoft, takie jak , użyj [łącznika programu Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto Outlook.com](https://outlook.live.com/owa/)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Outlook.com. Aby uruchomić przepływ pracy za pomocą Outlook.com wyzwalacza, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby dodać akcję Outlook.com do przepływu pracy, aplikacja logiki musi już mieć wyzwalacz.

## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

[Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest zdarzeniem, które uruchamia przepływ pracy w aplikacji logiki. W tym przykładzie aplikacja logiki używa wyzwalacza "sondowania", który sprawdza dostępność nowej wiadomości e-mail na koncie e-mail na podstawie określonego interwału i częstotliwości.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz pustą aplikację logiki w projektancie aplikacji logiki.

1. W polu wyszukiwania wpisz "outlook.com" jako filtr. W tym przykładzie wybierz pozycję **Po nadejściu nowej wiadomości e-mail**.

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj Outlook.com poświadczenia, aby aplikacja logiki mogła połączyć się z Twoim kontem. W przeciwnym razie, jeśli połączenie już istnieje, podaj informacje dotyczące właściwości wyzwalacza:

1. W wyzwalaczu ustaw wartości **częstotliwość** i **interwał.**

   Na przykład, jeśli chcesz, aby wyzwalacz sondować co 15 minut, ustaw **częstotliwość** na **Minuta**i ustaw **interwał** na **15**.

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**, która zapisuje aplikację logiki.

Aby odpowiedzieć na wyzwalacz, dodaj kolejną akcję. Na przykład można dodać akcję **wysyłania wiadomości** usługi Twilio, która wysyła tekst po odebraniu wiadomości e-mail.

## <a name="add-an-action"></a>Dodawanie akcji

[Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest operacją, która jest uruchamiana przez przepływ pracy w aplikacji logiki. W tym przykładowym przykładzie aplikacja logiki wysyła wiadomość e-mail z konta Outlook.com. Można użyć danych wyjściowych z innego wyzwalacza, aby wypełnić akcję. Załóżmy na przykład, że aplikacja logiki używa SalesForce **Gdy obiekt jest tworzony** wyzwalacz. Możesz dodać Outlook.com Wyślij akcję **e-mail** i użyć danych wyjściowych z wyzwalacza SalesForce w wiadomości e-mail.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. Aby dodać akcję jako ostatni krok w przepływie pracy, wybierz pozycję **Nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między tymi krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "outlook.com" jako filtr. W tym przykładzie wybierz pozycję **Wyślij wiadomość e-mail**. 

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj Outlook.com poświadczenia, aby aplikacja logiki mogła połączyć się z Twoim kontem. W przeciwnym razie jeśli połączenie już istnieje, podaj informacje dotyczące właściwości akcji.

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**, która zapisuje aplikację logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger [łącznika,](/connectors/outlook/)zobacz stronę odwołania łącznika . 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
