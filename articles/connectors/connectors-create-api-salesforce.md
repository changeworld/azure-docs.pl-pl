---
title: Dowiedz się, jak użyć łącznika usług Salesforce w aplikacji logiki | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji logiki z usługą Azure App service. Łącznik Salesforce udostępnia interfejs API do pracy z obiektami usługi Salesforce.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4278837bb5653b66223374aa728bdc81b279fff7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237305"
---
# <a name="get-started-with-the-salesforce-connector"></a>Rozpoczynanie pracy z usługą łącznika usług Salesforce
Łącznik Salesforce udostępnia interfejs API do pracy z obiektami usługi Salesforce.

Aby użyć [każdy łącznik](apis-list.md), najpierw musisz utworzyć aplikację logiki. Możesz rozpocząć od [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-salesforce-connector"></a>Nawiązać połączenie z łącznika usług Salesforce
Zanim aplikacja logiki może uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. A [połączenia](connectors-overview.md) zapewnia łączność między aplikacją logiki i inną usługę.  

### <a name="create-a-connection-to-salesforce-connector"></a>Utwórz połączenie łącznika usług Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Użyj wyzwalacza łącznika usług Salesforce
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, zdefiniowane w aplikacji logiki. [Dowiedz się więcej na temat wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Dodaj warunek
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Użyj działania łącznika usług Salesforce
Akcja jest operacją przeprowadzanych przez przepływ pracy zdefiniowane w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/salesforce/). 

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

