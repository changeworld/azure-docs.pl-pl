---
title: Połącz się z pudełkiem
description: Automatyzuj zadania i przepływy pracy, które tworzą pliki w polu i zarządzają nimi przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666775"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Tworzenie plików w polu i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps

W tym artykule pokazano, jak można tworzyć pliki i zarządzać nimi z wewnątrz aplikacji logiki z łącznikiem Box. W ten sposób można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy do zarządzania plikami i innymi akcjami, na przykład:

* Zbuduj swój przepływ biznesowy na podstawie danych, które otrzymujesz z Box.

* Wyzwalanie zautomatyzowanych zadań i przepływu pracy podczas tworzenia lub aktualizowanie pliku.

* Uruchom akcję, która kopiuje plik lub usuwa plik.

  Gdy te akcje uzyskać odpowiedź, udostępniają dane wyjściowe dla innych akcji. 
  Na przykład po zmianie pliku w polu, można wysłać ten plik w wiadomości e-mail przy użyciu usługi Office 365.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto Box](https://www.box.com/home)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Box. Aby uruchomić aplikację logiki za pomocą wyzwalacza Box, potrzebujesz [pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Podstawowa wiedza na temat [tworzenia aplikacji logicznych](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/box/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)