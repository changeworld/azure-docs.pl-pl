---
title: Połącz z usługą dysk Google
description: Twórz pliki i zarządzaj nimi za pomocą interfejsów API REST usługi Google Drive i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 6310c3b7e5b84915fa336708bc702e94317ad04c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789694"
---
# <a name="get-started-with-the-google-drive-connector"></a>Wprowadzenie do łącznika usługi dysk Google

Połącz się z usługą dysk Google, aby tworzyć pliki, uzyskiwać wiersze i nie tylko. Za pomocą usługi dysk Google można: 

* Utwórz przepływ biznesowy na podstawie danych uzyskanych z wyszukiwania. 
* Użyj akcji, aby przeszukiwać obrazy, przeszukiwać wiadomości i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. Możesz na przykład wyszukać film wideo, a następnie użyć usługi Twitter do opublikowania tego wideo w kanale usługi Twitter.

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Utwórz połączenie z usługą dysk Google

Po dodaniu tego łącznika do aplikacji logiki należy autoryzować Aplikacje logiki, aby nawiązać połączenie z dyskiem Google.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

Po utworzeniu połączenia należy wprowadzić właściwości dysku Google, takie jak ścieżka do folderu lub nazwa pliku. 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/googledrive/).

## <a name="more-connectors"></a>Więcej łączników

Wróć do [listy interfejsów API](apis-list.md).
