---
title: Łączenie z usługą Twilio z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które zarządzać globalnego wiadomości SMS, wiadomości MMS i protokołu IP za pośrednictwem Twojego konta usługi Twilio za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: fab52236c701f10c8e8e23ac398362ca4583ea06
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104905"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Zarządzanie wiadomości w Twilio za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik usługi Twilio można utworzyć zautomatyzowanym zadaniom i przepływów pracy, które pobieranie, wysyłanie i wyświetlanie wiadomości usługi Twilio, obejmujących globalnego wiadomości SMS, wiadomości MMS i adresu IP. Te akcje służy do wykonywania zadań przy użyciu konta usługi Twilio. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji usługi Twilio. Na przykład gdy nadejdzie nowa wiadomość, możesz wysłać wiadomość zawartości z łącznikiem usługi Slack. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Z [Twilio](https://www.twilio.com/): 

  * Twój identyfikator konta Twilio i [token uwierzytelniania](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), który można znaleźć na pulpicie nawigacyjnym usługi Twilio

    Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do konta usługi Twilio, z aplikacji logiki. 
    Jeśli używasz konta wersji próbnej usługi Twilio, możesz wysłać wiadomości SMS tylko do *zweryfikować* numerów telefonów.

  * Zweryfikowano numer telefonu usługi Twilio, który można wysłać wiadomości SMS

  * Zweryfikowano numer telefonu usługi Twilio, który umożliwia odbieranie wiadomości SMS

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Twilio. Aby użyć akcji usługi Twilio, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-twilio"></a>Łączenie z usługą Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

     * W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**. 

       — lub —

     * Między krokami, które chcesz dodać akcję wskaźnik myszy nad strzałką znajdującą się między krokami. 
     Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź "twilio" jako filtr. 
       W obszarze listy akcji wybierz akcję, którą chcesz.

1. Podaj odpowiednie szczegóły połączenia, a następnie wybierz **Utwórz**:

   * Nazwa do użycia w celu nawiązania połączenia
   * Twój identyfikator konta Twilio 
   * Token dostępu (uwierzytelnianie) usługi Twilio

1. Podaj odpowiednie szczegóły wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/twilio/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)