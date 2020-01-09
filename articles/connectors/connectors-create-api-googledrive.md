---
title: Połącz z usługą dysk Google
description: Automatyzowanie przepływów pracy służących do tworzenia plików dla usługi dysk Google i zarządzania nimi za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666843"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Tworzenie plików dla usługi dysk Google i zarządzanie nimi za pomocą Azure Logic Apps

Połącz się z usługą dysk Google, aby tworzyć pliki, uzyskiwać wiersze i nie tylko. Za pomocą usługi dysk Google można: 

* Utwórz przepływ biznesowy na podstawie danych uzyskanych z wyszukiwania. 
* Użyj akcji, aby przeszukiwać obrazy, przeszukiwać wiadomości i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. Możesz na przykład wyszukać film wideo, a następnie użyć usługi Twitter do opublikowania tego wideo w kanale usługi Twitter.

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Utwórz połączenie z usługą dysk Google

Po dodaniu tego łącznika do aplikacji logiki należy autoryzować Aplikacje logiki, aby nawiązać połączenie z dyskiem Google.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

Po utworzeniu połączenia należy wprowadzić właściwości dysku Google, takie jak ścieżka do folderu lub nazwa pliku. 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie struktury Swagger łącznika, przejrzyj [stronę odwołania łącznika](/connectors/googledrive/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](apis-list.md)
