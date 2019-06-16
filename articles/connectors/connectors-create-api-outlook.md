---
title: Nawiązać połączenie z usługi Outlook.com — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzanie poczty e-mail, kalendarzy i kontakty z usługi Outlook.com interfejsów API REST i usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105806"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Zarządzanie poczty e-mail, kalendarzy i kontakty w usłudze Outlook.com w usłudze Azure Logic Apps

W tym artykule pokazano, jak tworzyć i zarządzać na konto usługi Outlook.com w aplikacji logiki z łącznikiem usługi Box. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływów pracy na potrzeby konto usługi Outlook.com, na przykład:

* Wyślij wiadomość e-mail. 
* Planowanie spotkania.
* Dodawanie kontaktów. 

Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto usługi Outlook.com](https://outlook.live.com/owa/)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Outlook.com. Aby uruchomić aplikację logiki z wyzwalaczem programu Outlook, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Nawiązać połączenie z usługi Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegóły techniczne, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku struktury Swagger łącznika, zobacz [strona referencyjna łącznika](/connectors/outlook/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)