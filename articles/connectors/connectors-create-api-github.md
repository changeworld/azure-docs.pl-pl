---
title: Łączenie z usługą GitHub — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Monitorowanie zdarzeń dotyczących usługi GitHub za pomocą interfejsów API REST usługi GitHub i Azure Logic Apps
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
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462605"
---
# <a name="connect-to-github"></a>Łączenie z usługą GitHub

GitHub to internetowy Git repozytorium hostingu usługa, która oferuje wszystkie kontroli rozproszonych poprawek i funkcji zarządzania (SCM) kodu źródłowego w usłudze Git oraz inne funkcje.

Aby rozpocząć pracę z łącznikiem usługi GitHub [najpierw utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Utworzenie połączenia z usługi GitHub

Do korzystania z łącznika usługi GitHub w aplikacji logiki, należy najpierw utworzyć *połączenia* a następnie podaj szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis | 
| -------- | -------- | ----------- | 
| Token | Yes | Wprowadź swoje poświadczenia usługi GitHub. |

Po utworzeniu połączenia, można wykonać akcje i nasłuchiwanie wyzwalaczy opisanych w tym artykule.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Dla wyzwalaczy i akcji zdefiniowanych w strukturze Swagger i jakiekolwiek ograniczenia, przejrzyj [szczegóły łącznika](/connectors/github/).

## <a name="find-more-connectors"></a>Znajdź więcej łączników

* Przegląd [lista łączników](apis-list.md).