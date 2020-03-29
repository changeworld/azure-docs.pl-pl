---
title: Połącz się z SendGrid z aplikacji logiki azure
description: Automatyzuj zadania i przepływy pracy, które wysyłają wiadomości e-mail i zarządzają listami adresowymi w sendgrid przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 998020c5e39c8d50e8a14c74c43b7b435752f43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789311"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Wysyłanie wiadomości e-mail i zarządzanie listami adresowymi w sendgrid przy użyciu usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika SendGrid można tworzyć zautomatyzowane zadania i przepływy pracy, które wysyłają wiadomości e-mail i zarządzają listami adresatów, na przykład:

* Wyślij wiadomość e-mail.
* Dodawanie adresatów do list.
* Pobierz, dodaj i zarządzaj globalnym tłumieniem.

Akcje SendGrid w aplikacjach logiki umożliwiają wykonywanie tych zadań. Można również mieć inne akcje użyć danych wyjściowych z SendGrid akcji. 

Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, należy użyć oddzielnego wyzwalacza, takiego jak wyzwalacz **cyklu.** Jeśli na przykład regularnie dodajesz adresatów do list, możesz wysyłać wiadomości e-mail dotyczące adresatów i list przy użyciu łącznika programu Office 365 Outlook lub łącznika Outlook.com.
Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Konto SendGrid](https://www.sendgrid.com/) i [klucz interfejsu API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Klucz interfejsu API autoryzuje aplikację logiki do tworzenia połączenia i uzyskiwania dostępu do konta SendGrid.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SendGrid. Aby użyć akcji SendGrid, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-sendgrid"></a>Nawiązywanie połączenia z usługą SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 

     — lub —

   * Między krokami, w których chcesz dodać akcję, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "sendgrid" jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

1. Podaj nazwę połączenia. 

1. Wprowadź klucz interfejsu API SendGrid, a następnie wybierz pozycję **Utwórz**.

1. Podaj szczegóły niezbędne do wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/sendgrid/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)