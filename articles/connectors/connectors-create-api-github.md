---
title: Uzyskiwanie dostępu do repozytorium Usługi GitHub, monitorowanie ich i zarządzanie nimi
description: Monitoruj zdarzenia usługi GitHub i zarządzaj repozytorium Usługi GitHub, tworząc zautomatyzowane przepływy pracy za pomocą aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378453"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitorowanie repozytorium usługi GitHub i zarządzanie nim przy użyciu aplikacji Azure Logic Apps

GitHub to internetowa usługa hostingowa repozytorium Git, która oferuje wszystkie funkcje kontroli wersji rozproszonej i zarządzania kodem źródłowym (SCM) w Git oraz inne funkcje.

Aby rozpocząć pracę z łącznikiem GitHub, [najpierw utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Tworzenie połączenia z usługą GitHub

Aby użyć łącznika GitHub w aplikacji logiki, należy najpierw utworzyć *połączenie,* a następnie podać szczegóły dla tych właściwości: 

| Właściwość | Wymagany | Opis | 
| -------- | -------- | ----------- | 
| Token | Tak | Podaj swoje poświadczenia GitHub. |

Po utworzeniu połączenia można wykonać akcje i nasłuchiwali wyzwalaczy opisanych w tym artykule.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną łącznika](/connectors/github/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)