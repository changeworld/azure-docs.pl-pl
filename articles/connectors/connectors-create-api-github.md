---
title: Łączenie z usługą GitHub — Azure Logic Apps
description: Monitoruj zdarzenia usługi GitHub za pomocą interfejsów API REST usługi GitHub i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050890"
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