---
title: Łączenie się z usługą SMTP z aplikacji Logika platformy Azure
description: Automatyzuj zadania i przepływy pracy, które wysyłają wiadomości e-mail za pośrednictwem konta SMTP (Simple Mail Transfer Protocol) przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 60acd128495176cd0a90418c61edf53bdcd88e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647580"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Wysyłanie wiadomości e-mail z konta SMTP za pomocą usługi Azure Logic Apps

Aplikacja Azure Logic Apps i łącznik SMTP (Simple Mail Transfer Protocol) umożliwiają tworzenie zautomatyzowanych zadań i przepływów pracy, które wysyłają wiadomości e-mail z konta SMTP. Można również użyć innych akcji przy użyciu danych wyjściowych z akcji SMTP. Na przykład po wysłaniu wiadomości e-mail przez protokół SMTP można powiadomić zespół w aplikacji Slack za pomocą łącznika Slack. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto SMTP i poświadczenia użytkownika

  Poświadczenia autoryzują aplikację logiki do tworzenia połączenia i uzyskiwania dostępu do konta SMTP.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SMTP. Aby użyć akcji SMTP, uruchom aplikację logiki za pomocą wyzwalacza, takiego jak wyzwalacz Salesforce, jeśli masz konto Salesforce.

  Na przykład można uruchomić aplikację logiki z **when rekord jest tworzony** Salesforce wyzwalacza. 
  Ten wyzwalacz jest uruchamiany za każdym razem, gdy w salesforce tworzony jest nowy rekord, taki jak potencjalny klient. 
  Następnie można wykonać ten wyzwalacz za pomocą akcji **SMTP Wyślij wiadomość e-mail.** W ten sposób po utworzeniu nowego rekordu aplikacja logiki wysyła wiadomość e-mail z konta SMTP o nowym rekordzie.

## <a name="connect-to-smtp"></a>Łączenie się z usługą SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W obszarze ostatniego kroku, w którym chcesz dodać akcję SMTP, wybierz pozycję **Nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "smtp" jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

1. Po wyświetleniu monitu podaj te informacje o połączeniu:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa połączenia** | Tak | Nazwa połączenia z serwerem SMTP | 
   | **Adres serwera SMTP** | Tak | Adres serwera SMTP | 
   | **Nazwa użytkownika** | Tak | Twoja nazwa użytkownika dla twojego konta SMTP | 
   | **Hasło** | Tak | Twoje hasło do konta SMTP | 
   | **Port serwera SMTP** | Nie | Określony port na serwerze SMTP, którego chcesz użyć | 
   | **Włączyć SSL?** | Nie | Włącz lub wyłącz szyfrowanie SSL. | 
   |||| 

1. Podaj szczegóły niezbędne do wybranej akcji. 

1. Zapisz aplikację logiki lub kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/smtpconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)