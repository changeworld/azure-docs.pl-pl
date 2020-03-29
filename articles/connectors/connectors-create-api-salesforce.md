---
title: Łączenie się z salesforce z usługi Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy monitoruj, tworzą i zarządzają rekordami i zadaniami Salesforce przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789294"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorowanie, tworzenie zasobów Salesforce i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika Salesforce można tworzyć zautomatyzowane zadania i przepływy pracy dla zasobów Salesforce, takich jak rekordy, zadania i obiekty, na przykład:

* Monitoruj, kiedy rekordy są tworzone lub zmieniane. 
* Tworzenie, otrzymywania i zarządzania zadaniami i rekordami, w tym akcjami wstawiania, aktualizowania i usuwania.

Można użyć wyzwalaczy Salesforce, które otrzymują odpowiedzi od Salesforce i udostępniają dane wyjściowe innym działaniom. Akcje w aplikacjach logiki umożliwiają wykonywanie zadań z zasobami Salesforce. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Konto Salesforce](https://salesforce.com/)

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Salesforce. Aby rozpocząć od wyzwalacza Salesforce, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Salesforce, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-salesforce"></a>Nawiązywanie połączenia z usługą Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "salesforce" jako filtr. 
   W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

     — lub —

   * W przypadku istniejących aplikacji logiki w kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. W polu wyszukiwania wpisz "salesforce" jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do Salesforce, zaloguj się teraz i zezwól na dostęp.

   Poświadczenia autoryzują aplikację logiki do tworzenia połączenia z Salesforce i uzyskiwania dostępu do danych.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/salesforce/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)