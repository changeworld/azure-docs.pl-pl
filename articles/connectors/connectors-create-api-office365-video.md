---
title: Łączenie z wideo pakietu Office 365
description: Zarządzaj filmami wideo za pomocą interfejsów API REST i Azure Logic Apps wideo pakietu Office 365
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: bf3c7c4faac3fcd4dd0c3f368fbe18a224ed591b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789492"
---
# <a name="get-started-with-the-office365-video-connector"></a>Wprowadzenie do łącznika wideo usługi Office 365

Połącz się z filmem wideo pakietu Office 365, aby uzyskać informacje na temat wideo z pakietem Office 365, uzyskać listę filmów wideo i wiele więcej. Film wideo z pakietem Office 365 umożliwia:

* Kompiluj swój przepływ biznesowy na podstawie danych uzyskanych z wideo pakietu Office 365. 

* Użyj akcji, które sprawdzają stan portalu wideo, uzyskaj listę wszystkich filmów wideo w kanale i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. 

Na przykład możesz użyć łącznika Wyszukiwanie Bing, aby wyszukać wideo pakietu Office 365, a następnie skorzystać z łącznika wideo pakietu Office 365, aby uzyskać informacje o tym filmie wideo. Jeśli film wideo spełnia Twoje wymagania, możesz ogłosić ten film wideo w serwisie Facebook.

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Utwórz połączenie z łącznikiem wideo usługi Office 365

Po dodaniu tego łącznika do aplikacji logiki należy zalogować się do konta wideo pakietu Office 365 i zezwolić aplikacjom logiki na łączenie się ze swoim kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Po utworzeniu połączenia należy wprowadzić właściwości wideo pakietu Office 365, takie jak nazwa dzierżawy lub identyfikator kanału. 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)