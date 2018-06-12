---
title: Nawiązać GitHub — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Monitor zdarzeń GitHub z interfejsów API REST usługi GitHub i usługi Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295816"
---
# <a name="connect-to-github"></a>Nawiązać połączenia z usługi GitHub

GitHub jest oparte na sieci web Git repozytorium hostingu usługi, która oferuje wszystkie kontrolki rozproszonej poprawki i funkcje zarządzania (SCM) kodu źródłowego w usłudze Git i inne funkcje.

Aby rozpocząć pracę z łącznikiem usługi GitHub [najpierw utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Utwórz połączenie z usługi GitHub

Do korzystania z łącznika usługi GitHub w aplikacji logiki, należy najpierw utworzyć *połączenia* , a następnie podaj szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis | 
| -------- | -------- | ----------- | 
| Token | Yes | Wprowadź swoje poświadczenia usługi GitHub. |

Po utworzeniu połączenia, można wykonać akcje i nasłuchiwania wyzwalaczy opisane w tym artykule.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyzwalacze i akcje zdefiniowane w Swagger i żadnych limitów Przejrzyj [szczegóły łącznika](/connectors/github/).

## <a name="find-more-connectors"></a>Znajdź więcej łączników

* Przegląd [lista łączników](apis-list.md).