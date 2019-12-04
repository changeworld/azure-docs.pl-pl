---
title: Łączenie z usługą GitHub
description: Monitoruj zdarzenia usługi GitHub za pomocą interfejsów API REST usługi GitHub i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789754"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Łączenie z usługą GitHub z Azure Logic Apps

GitHub to usługa hostingu repozytorium git oparta na sieci Web, która oferuje wszystkie rozproszone funkcje kontroli poprawek i zarządzania kodem źródłowym (SCM) w systemie Git oraz inne funkcje.

Aby rozpocząć pracę z łącznikiem usługi GitHub, [najpierw Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Utwórz połączenie z usługą GitHub

Aby skorzystać z łącznika usługi GitHub w aplikacji logiki, należy najpierw utworzyć *połączenie* , a następnie podać szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis | 
| -------- | -------- | ----------- | 
| Token | Tak | Podaj swoje poświadczenia usługi GitHub. |

Po utworzeniu połączenia można wykonać akcje i wysłuchać wyzwalacze opisane w tym artykule.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania łącznika](/connectors/github/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)