---
title: Łączenie się z usługą Project Online za pomocą aplikacji Azure Logic Apps
description: Automatyzuj przepływy pracy monitorujące, tworzące i zarządzające projektami, zadaniami i zasobami usługi Project Online przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 116922e018410f260b33f4dfd76e5983fbbcc0a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789328"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Zarządzanie projektami, zadaniami i zasobami usługi Project Online przy użyciu aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika usługi Project Online można tworzyć zautomatyzowane zadania i przepływy pracy dla projektów, zadań i zasobów w usłudze Project Online za pośrednictwem usługi Office 365. Przepływy pracy mogą wykonywać następujące akcje i inne, na przykład:

* Monitoruj, kiedy tworzone są nowe projekty, zadania lub zasoby. Lub monitorować, kiedy nowe projekty są publikowane.
* Tworzenie nowych projektów, zadań lub zasobów.
* Lista istniejących projektów lub zadań.
* Wyewidencjonuj, zaewidencjonuj lub opublikuj projekty.

Usługa Project Online pomaga planować, ustalać priorytety i zarządzać projektami i inwestycjami w portfele projektów z niemal dowolnego miejsca na prawie każdym urządzeniu, zapewniając zaawansowane możliwości zarządzania projektami. Można użyć wyzwalaczy usługi Project Online, które otrzymują odpowiedzi z usługi Project Online i udostępniają dane wyjściowe innym działaniom. Akcje w aplikacjach logiki umożliwiają wykonywanie różnych zadań w usłudze Project Online. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Usługa Project Online dostępna za pośrednictwem [konta usługi Office 365](https://www.office.com/), 

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do danych usługi Project Online. Aby rozpocząć od wyzwalacza usługi Project Online, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Project Online, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-project-online"></a>Łączenie się z usługą Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "Project Online" jako filtr. 
   W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

     — lub —

   * W przypadku istniejących aplikacji logiki w kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. W polu wyszukiwania wpisz "Project Online" jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do usługi Project Online, zaloguj się teraz.

   Poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie z usługą Project Online i uzyskać dostęp do danych.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/projectonline/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)