---
title: Łączenie się z klipem wideo usługi Office 365
description: Automatyzuj zadania i przepływy pracy, które zarządzają klipami wideo w usłudze Wideo w usłudze Office 365, korzystając z aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665789"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Zarządzanie klipami wideo w usłudze Office365 Wideo przy użyciu aplikacji Azure Logic Apps

Połącz się z klipem wideo usługi Office 365, aby uzyskać informacje o klipie wideo z usługi Office 365, uzyskać listę klipów wideo i nie tylko. Dzięki usłudze Wideo usługi Office 365 możesz:

* Twórz przepływ biznesowy na podstawie danych, które otrzymujesz z usługi Wideo w usłudze Office 365. 

* Użyj akcji, które sprawdzają stan portalu wideo, otrzymują listę wszystkich filmów na kanale i nie tylko. Te akcje uzyskać odpowiedź, a następnie udostępnić dane wyjściowe dla innych akcji. 

Na przykład można użyć łącznika wyszukiwania Bing do wyszukiwania klipów wideo usługi Office 365, a następnie użyć łącznika wideo usługi Office 365, aby uzyskać informacje o tym klipie wideo. Jeśli film spełnia Twoje wymagania, możesz opublikować ten film na Facebooku.

Możesz rozpocząć, tworząc aplikację logiki teraz, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Łączenie się z klipem wideo usługi Office365

Po dodaniu tego łącznika do aplikacji logiki należy zalogować się do konta wideo usługi Office 365 i zezwolić aplikacjom logiki na łączenie się z kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Po utworzeniu połączenia należy wprowadzić właściwości wideo usługi Office 365, takie jak nazwa dzierżawy lub identyfikator kanału. 

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger, a także zobacz wszelkie limity w [szczegółach łącznika](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)