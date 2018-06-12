---
title: Nawiązać połączenia z serwisem Facebook — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie osi czasu i strony z interfejsów API REST usługi Facebook i usługi Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 985f3cf70a07b3080f34181e64c5bb1419d530bd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295010"
---
# <a name="get-started-with-the-facebook-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Facebook
Nawiązać połączenia z serwisem Facebook i publikować na osi czasu, Pobierz strony źródła danych i inne. Za pomocą usługi Facebook można:

* Tworzenie sieci przepływu biznesowe na podstawie danych otrzymywanych z usługi Facebook. 
* Po odebraniu nowego wpisu, należy użyć wyzwalacza.
* Użyj akcji, które post do osi czasu, Pobierz strony źródła danych i inne. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład gdy istnieje nowy wpis na osi czasu, można wykonać tego post i wypchnąć go do Twojego kanału informacyjnego usługi Twitter. 

Rozpoczynanie pracy przez teraz tworzenie aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Utwórz połączenie z usługi Facebook
Po dodaniu tego łącznika do aplikacji logiki, należy zezwolić aplikacji logiki do nawiązania połączenia z serwisem Facebook.

1. Zaloguj się do swojego konta w serwisie Facebook
2. Wybierz **autoryzacji**i umożliwia aplikacji logiki, aby podłączyć się i korzystać z usługi Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/facebook/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).