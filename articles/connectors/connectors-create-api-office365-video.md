---
title: Nawiązać połączenie z wideo w usłudze Office 365 — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzaj wideo za pomocą interfejsów API REST wideo usługi Office 365 i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c10a2aa097b63fd3751be01bbfeb6097080bbb9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105823"
---
# <a name="get-started-with-the-office365-video-connector"></a>Rozpoczynanie pracy z usługą łącznik wideo Office365
Połączyć się z pakietu Office 365 wideo, aby uzyskać informacje na temat usługi Office 365, wideo, Pobierz listę filmy wideo i inne. Z wideo w usłudze Office 365 możesz wykonywać następujące czynności:

* Tworzenie przepływu biznesowych na podstawie danych, który jest pobierany z wideo w usłudze Office 365. 
* Za pomocą akcji, które Sprawdź stan portalu wideo, Pobierz listę wszystkich wideo w kanału i nie tylko. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Można na przykład korzystania z łącznika wyszukiwania Bing, wyszukiwanie wideo usługi Office 365, a następnie użyć łącznik wideo w usłudze Office 365, aby uzyskać informacje na temat tego wideo. Jeśli wideo spełnia Twoje wymagania, możesz zadać ten film wideo w serwisie Facebook. 

Możesz rozpocząć od teraz tworzenie aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Utwórz połączenie łącznik wideo Office365
Po dodaniu tego łącznika do aplikacji logiki, należy zalogować się do konta usługi Office 365 Video i zezwolić aplikacji logiki, aby połączyć się z kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Po utworzeniu połączenia, wprowadź właściwości wideo usługi Office 365, takich jak nazwa dzierżawy, lub kanału identyfikatora. 


## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).