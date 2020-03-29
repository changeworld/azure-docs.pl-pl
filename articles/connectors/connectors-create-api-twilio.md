---
title: Łączenie się z usługą Twilio za pomocą aplikacji Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy, które zarządzają globalnymi wiadomościami SMS, MMS i IP za pośrednictwem konta usługi Twilio przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789107"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Zarządzanie wiadomościami w usłudze Twilio za pomocą aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika usługi Twilio można tworzyć zautomatyzowane zadania i przepływy pracy, które odbierają, wysyłają i wyświetlają wiadomości w usłudze Twilio, które obejmują globalne wiadomości SMS, MMS i IP. Za pomocą tych akcji można wykonywać zadania za pomocą konta usługi Twilio. Można również mieć inne akcje użyć danych wyjściowych z akcji usługi Twilio. Na przykład po odebraniu nowej wiadomości można wysłać zawartość wiadomości za pomocą łącznika Zapasów. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Od [Twilio](https://www.twilio.com/): 

  * Identyfikator konta usługi Twilio i [token uwierzytelniania,](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)które można znaleźć na pulpicie nawigacyjnym usługi Twilio

    Poświadczenia autoryzować aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta usługi Twilio z aplikacji logiki. 
    Jeśli korzystasz z konta próbnego usługi Twilio, możesz wysyłać wiadomości SMS tylko na *zweryfikowane* numery telefonów.

  * Zweryfikowany numer telefonu usługi Twilio, który może wysyłać wiadomości SMS

  * Zweryfikowany numer telefonu usługi Twilio, który może odbierać wiadomości SMS

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Twilio. Aby użyć akcji usługi Twilio, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-twilio"></a>Łączenie się z urządzeniem Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

     * W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 

       — lub —

     * Między krokami, w których chcesz dodać akcję, przesuń wskaźnik myszy na strzałkę między krokami. 
     Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wpisz "twilio" jako filtr. 
       W obszarze listy akcje wybierz odpowiednią akcję.

1. Podaj niezbędne szczegóły połączenia, a następnie wybierz pozycję **Utwórz**:

   * Nazwa używana dla połączenia
   * Identyfikator konta usługi Twilio 
   * Token dostępu usługi Twilio (uwierzytelnianie)

1. Podaj szczegóły niezbędne do wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/twilio/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)