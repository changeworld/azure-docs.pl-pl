---
title: Dodaj łącznik usługi Twilio w aplikacjach logiki platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie łącznika usługi Twilio za pomocą interfejsu API REST parametrów
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652009"
---
# <a name="get-started-with-the-twilio-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Twilio
Nawiązać połączenia usługi Twilio, wysyłanie i odbieranie globalnego wiadomości SMS, wiadomości MMS i adresu IP. Za pomocą usługi Twilio możesz wykonywać następujące czynności:

* Tworzenie przepływu biznesowych na podstawie danych, który jest pobierany z usługi Twilio. 
* Za pomocą akcji, które pobierają wiadomości, lista wiadomości i innych. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład po otrzymaniu nowej wiadomości usługi Twilio, możesz wykonać ten komunikat i przepływu pracy usługi Service Bus użyć. 

Zacznij od utworzenia aplikacji logiki; zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Utwórz połączenie z platformą Twilio
Po dodaniu tego łącznika do aplikacji logiki, wprowadź następujące wartości Twilio:

| Właściwość | Wymagane | Opis |
| --- | --- | --- |
| Identyfikator konta |Yes |Wprowadź identyfikator konta Twilio |
| Token dostępu |Yes |Wprowadź token dostępu Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Jeśli nie masz token dostępu Twilio, zobacz [tożsamości użytkowników i tokeny dostępu](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/twilio/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).