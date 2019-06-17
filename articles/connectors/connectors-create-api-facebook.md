---
title: Łączenie z serwisem Facebook — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzaj oś czasu i strony za pomocą interfejsów API REST usługi Facebook i Azure Logic Apps
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
ms.openlocfilehash: 25595127d913d3cd093e0af3d7916e33fc7cb352
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105979"
---
# <a name="get-started-with-the-facebook-connector"></a>Rozpoczynanie pracy z usługą łącznika serwisu Facebook
Łączenie z serwisem Facebook i publikować na osi czasu, uzyskiwanie kanału informacyjnego strony i nie tylko. Za pomocą usługi Facebook możesz wykonywać następujące czynności:

* Tworzenie przepływu biznesowych na podstawie danych, który jest pobierany z usługi Facebook. 
* Po otrzymaniu nowego wpisu, należy użyć wyzwalacza.
* Akcje użycia, które wpis na Twojej osi czasu, uzyskiwanie kanału informacyjnego strony i nie tylko. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład po nowy wpis na Twojej osi czasu, można wykonać tego wpisu i Wypchnij go do Twojego kanału informacyjnego usługi Twitter. 

Możesz rozpocząć od teraz tworzenie aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Utworzenie połączenia z usługi Facebook
Po dodaniu tego łącznika do aplikacji logiki, należy autoryzować logic apps, aby nawiązać połączenie z usługi Facebook.

1. Zaloguj się do konta w serwisie Facebook
2. Wybierz **Autoryzuj**i Zezwól aplikacji logiki, aby łączenie i korzystanie z usługi Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/facebook/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).