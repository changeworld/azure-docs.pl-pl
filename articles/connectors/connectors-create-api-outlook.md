---
title: Nawiązywanie połączenia z usługą Outlook.com-Azure Logic Apps | Microsoft Docs
description: Zarządzanie pocztą e-mail, kalendarzami i kontaktami przy użyciu interfejsów API REST Outlook.com i Azure Logic Apps
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
ms.openlocfilehash: 4586255e96647267bc913f2bc054610163e16bd3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050880"
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

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)