---
title: Połącz się z Facebookiem
description: Automatyzuj zadania i przepływy pracy, które zarządzają osią czasu i strony facebooka za pomocą usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665806"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Zarządzanie osią czasu i stroną na Facebooku za pomocą usługi Azure Logic Apps

Połącz się z Facebookiem i publikuj na osi czasu, pobierz kanał strony i nie tylko. Dzięki Facebookowi możesz:

* Zbuduj przepływ swojej firmy na podstawie danych, które otrzymujesz z Facebooka. 
* Użyj wyzwalacza po odebraniu nowego wpisu.
* Użyj akcji, które publikują posty na osi czasu, pobierz kanał informacyjny strony i nie tylko. Te akcje uzyskać odpowiedź, a następnie udostępnić dane wyjściowe dla innych akcji. Na przykład, gdy na twojej osi czasu znajduje się nowy post, możesz go opublikować i przesunąć do kanału na Twitterze. 

Możesz rozpocząć, tworząc aplikację logiki teraz, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Tworzenie połączenia z Facebookiem

Po dodaniu tego łącznika do aplikacji logiki należy autoryzować aplikacje logiki, aby połączyć się z Facebookiem.

1. Zaloguj się na swoje konto na Facebooku.

2. Wybierz **pozycję Autoryzuj**i pozwól aplikacjom logiki łączyć się z Facebookiem i korzystać z niego. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/facebook/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)