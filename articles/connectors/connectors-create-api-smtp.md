---
title: Nawiązywanie połączenia SMTP z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które Wyślij wiadomość e-mail za pośrednictwem konta usługi SMTP (Simple Mail Transfer Protocol) przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106154"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Wyślij wiadomość e-mail z konta usługi SMTP z usługą Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik Simple Mail Transfer Protocol (SMTP) można utworzyć zautomatyzowanym zadaniom i przepływów pracy, które Wyślij wiadomość e-mail z konta usługi SMTP. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji SMTP. Na przykład po usługi SMTP wysyła wiadomość e-mail, może generować powiadomienia zespołu w Slack z łącznikiem usługi Slack. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Poświadczenia konta i użytkownika SMTP

  Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta SMTP.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi SMTP. Aby użyć akcji SMTP, uruchomić swoją aplikację logiki z wyzwalaczem, takich jak Salesforce wyzwalacza, jeśli masz konto usługi Salesforce.

  Na przykład, można uruchomić aplikację logiki z **gdy rekord zostanie utworzony** wyzwalacza usługi Salesforce. 
  Wyzwalacza każdorazowo, utworzony nowy rekord zostanie zaktualizowany w usłudze Salesforce. 
  Następnie można wykonać tego wyzwalacza, za pomocą serwera SMTP **Wyślij wiadomość E-mail** akcji. W ten sposób, gdy tworzony jest nowy rekord, aplikacja logiki wysyła wiadomość e-mail z konta usługi SMTP o nowy rekord.

## <a name="connect-to-smtp"></a>Nawiązać połączenie SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W ostatnim kroku, której chcesz dodać akcję SMTP, wybierz **nowy krok**. 

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "smtp" jako filtr. W obszarze listy akcji wybierz akcję, którą chcesz.

1. Po wyświetleniu monitu podaj informacje o połączeniu:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa połączenia** | Yes | Nazwę połączenia z serwerem SMTP | 
   | **Adres serwera SMTP** | Yes | Adres serwera SMTP | 
   | **Nazwa użytkownika** | Yes | Nazwa użytkownika dla konta usługi SMTP | 
   | **Hasło** | Yes | Hasło dla konta usługi SMTP | 
   | **Port serwera SMTP** | Nie | Określonego portu na serwerze SMTP, którego chcesz użyć | 
   | **Włączyć SSL?** | Nie | Włącz lub wyłącz szyfrowanie SSL. | 
   |||| 

1. Podaj odpowiednie szczegóły wybranej akcji. 

1. Zapisz aplikację logiki, lub Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/smtpconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)