---
title: Nawiązywanie połączenia usługi Salesforce z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorowanie, tworzenie i zarządzanie rekordy usługi Salesforce i zadań za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106019"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorowanie, tworzenie i zarządzanie zasobami usługi Salesforce za pomocą usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika usług Salesforce możesz utworzyć automatycznych zadań i przepływów pracy dla zasobów usługi Salesforce, takich jak rekordy, zadań i obiekty, na przykład:

* Monitor, gdy rekordy są tworzone lub zmienione. 
* Tworzenie, pobieranie, zarządzanie zadania i rekordach, takie jak insert, update i usuwanie akcji.

Możesz użyć wyzwalaczy usługi Salesforce, które uzyskiwanie odpowiedzi z usług Salesforce i udostępnić dane wyjściowe innych działań. Akcje w aplikacjach logiki służy do wykonywania zadań przy użyciu zasobów usługi Salesforce. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* A [konta usług Salesforce](https://salesforce.com/)

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Salesforce. Można uruchomić z wyzwalaczem usługi Salesforce [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Salesforce, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-salesforce"></a>Łączenie z usługą Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * Puste logic apps w polu wyszukiwania wprowadź "salesforce" jako filtr. 
   W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

     —lub—

   * Dla istniejących aplikacji logiki w ramach kroku, w której chcesz dodać akcję, wybierz **nowy krok**. W polu wyszukiwania wprowadź "salesforce" jako filtr. W obszarze listy akcji wybierz akcję, którą chcesz.

1. Jeśli zostanie wyświetlony monit logować się do usługi Salesforce, zarejestruj się teraz i zezwolić na dostęp.

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie z usługą Salesforce i uzyskiwać dostęp do danych.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/salesforce/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)