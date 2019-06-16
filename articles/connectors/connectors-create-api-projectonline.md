---
title: Połącz się z projektem Online z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzuj przepływy pracy monitorowania, tworzenie i zarządzanie projekty usługi Project Online, zadaniami i zasobami za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105634"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Zarządzanie usługi Project Online projektami, zadaniami i zasobami za pomocą usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łączników usługi Project Online możesz utworzyć automatycznych zadań i przepływów pracy dla projektów, zadań i zasobów w usłudze Project Online za pośrednictwem usługi Office 365. Przepływy pracy mogą wykonywać te akcje i inne, na przykład:

* Monitor, gdy zostaną utworzone nowe projekty, zadania lub zasobów. Lub monitora podczas publikowania nowych projektów.
* Utwórz nowe projekty, zadania lub zasobów.
* Utwórz listę istniejących projektów lub zadania.
* Zapoznaj się z, sprawdź plik w folderze lub publikowanie projektów.

Project Online ułatwia planowanie, kategoryzowania i Zarządzaj projektami i inwestycjami dotyczącymi portfela projektów z niemal dowolnego miejsca na prawie każdym urządzeniu, zapewniając możliwości zarządzania projektem zaawansowane. Możesz użyć usługi Project Online wyzwalacze, które uzyskiwanie odpowiedzi z usługi Project Online i udostępnić dane wyjściowe innych działań. Akcje w aplikacjach logiki służy do wykonywania różnych zadań w usłudze Project Online. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Project Online i dostępne za pośrednictwem [konta usługi Office 365](https://www.office.com/), 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do danych usługi Project Online. Aby uruchomić z wyzwalaczem usługi Project Online [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby korzystać z usługi Project Online akcje, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-project-online"></a>Łączenie z usługą Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * W przypadku aplikacji logiki puste w polu wyszukiwania wprowadź "Project Online" jako filtr. 
   W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

     —lub—

   * Dla istniejących aplikacji logiki w ramach kroku, w której chcesz dodać akcję, wybierz **nowy krok**. W polu wyszukiwania jako filtr wprowadź "Project Online". W obszarze listy akcji wybierz akcję, którą chcesz.

1. Jeśli zostanie wyświetlony monit do logowania się w usłudze Project Online, zarejestruj się teraz.

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie z usługi Project Online i uzyskiwać dostęp do danych.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/projectonline/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)