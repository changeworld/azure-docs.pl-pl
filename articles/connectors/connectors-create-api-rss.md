---
title: Łączenie się z źródłami danych RSS z usługi Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy monitorujące źródła danych RSS i zarządzające nimi przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789345"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Zarządzanie źródłami danych RSS przy użyciu aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika RSS można tworzyć zautomatyzowane zadania i przepływy pracy dla dowolnego źródła danych RSS, na przykład:

* Monitoruj, kiedy publikowane są elementy kanału informacyjnego RSS.
* Wyświetl listę wszystkich elementów kanału informacyjnego RSS.

RSS (Rich Site Summary), zwany także Really Simple Syndication, jest popularnym formatem syndykacji internetowej i jest używany do publikowania często aktualizowanych treści, takich jak wpisy na blogu i nagłówki wiadomości. Wielu wydawców treści udostępnia kanał RSS, aby użytkownicy mogli subskrybować tę zawartość. 

Można użyć wyzwalacza RSS, który pobiera odpowiedzi z kanału informacyjnego RSS i udostępnia dane wyjściowe innym działaniom. Akcji RSS w aplikacjach logiki można wykonać zadanie za pomocą kanału informacyjnego RSS. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Adres URL kanału informacyjnego RSS

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do źródła danych RSS. Aby rozpocząć od wyzwalacza RSS, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji RSS, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-an-rss-feed"></a>Łączenie się z kanałem RSS

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "rss" jako filtr. W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

     — lub —

   * W przypadku istniejących aplikacji logiki w kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. W polu wyszukiwania jako filtr wprowadź „rss”. W obszarze listy akcje wybierz odpowiednią akcję.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/rss/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)