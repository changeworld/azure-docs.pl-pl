---
title: Nawiązywanie połączenia z usługą Outlook.com
description: Zarządzanie pocztą e-mail, kalendarzami i kontaktami przy użyciu interfejsów API REST Outlook.com i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789373"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Zarządzanie pocztą e-mail, kalendarzami i kontaktami w programie Outlook.com przy użyciu Azure Logic Apps

W tym artykule pokazano, jak utworzyć konto Outlook.com i zarządzać nim w ramach aplikacji logiki za pomocą łącznika Box. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy dla konta usługi Outlook.com, na przykład:

* Wyślij wiadomość e-mail. 
* Planowanie spotkań.
* Dodaj kontakty. 

Jeśli dopiero zaczynasz w usłudze Logic Apps, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto Outlook.com](https://outlook.live.com/owa/)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta Outlook.com. Do uruchomienia aplikacji logiki przy użyciu wyzwalacza programu Outlook potrzebna jest [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Nawiązywanie połączenia z usługą Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/outlook/). 

## <a name="get-support"></a>Uzyskaj pomoc techniczną

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)