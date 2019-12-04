---
title: Połącz z usługą Box
description: Tworzenie plików i zarządzanie nimi za pomocą interfejsów API REST w usłudze Box i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 38e061e918d445de07961af1789891f44c59090a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789868"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Utwórz pliki i zarządzaj nimi w usłudze Box przy użyciu Azure Logic Apps

W tym artykule pokazano, jak można tworzyć pliki i zarządzać nimi w usłudze Box z poziomu aplikacji logiki za pomocą łącznika Box. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy związane z zarządzaniem plikami i innymi akcjami, na przykład:

* Utwórz przepływ biznesowy na podstawie danych uzyskanych z usługi Box.

* Wyzwalaj zautomatyzowane zadania i przepływy pracy po utworzeniu lub zaktualizowaniu pliku.

* Uruchom akcję, która kopiuje plik lub usuwa plik.

  Po otrzymaniu odpowiedzi te akcje stają się dostępne dla innych akcji. 
  Na przykład po zmianie pliku w polu można wysłać ten plik w wiadomości e-mail przy użyciu pakietu Office 365.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto w usłudze [Box](https://www.box.com/home)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta w usłudze Box. Aby uruchomić aplikację logiki przy użyciu wyzwalacza Box, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Jeśli dopiero zaczynasz w usłudze Logic Apps, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/box/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)