---
title: Łączenie z serwisem Twitter z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorowanie i zarządzanie tweety, a także uzyskać dane dotyczące obserwatorów, obserwowanych użytkowników, innych użytkowników, osie czasu i innymi za pomocą konta w usłudze Twitter przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104990"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorowanie i zarządzanie nimi Twitter przy użyciu usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika serwisu Twitter można utworzyć zautomatyzowanych zadań i przepływów pracy, które monitorują i zarządzania danymi, które Cię interesują w usłudze Twitter takich jak opublikuje obserwatorów, użytkowników i następnie użytkowników, osie czasu i uzyskać więcej informacji, oraz inne czynności, na przykład:

* Monitorowanie, post i wyszukiwanie tweetów.
* Pobierz dane, takie jak obserwatorów obserwowanych użytkowników, osie czasu i więcej.

Możesz użyć wyzwalaczy, które uzyskać odpowiedzi z kontem w usłudze Twitter i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonują zadania związane z kontem w usłudze Twitter. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji usługi Twitter. Na przykład gdy pojawi się nowy tweet z określonym hasztagiem, możesz wysłać wiadomości z łącznikiem usługi Slack. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Poświadczenia konta i użytkownika usługi Twitter

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i uzyskać dostępu do konta usługi Twitter.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Twitter. Aby uruchomić z wyzwalaczem usługi Twitter [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Twitter, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * Puste logic apps, w polu wyszukiwania wprowadź "twitter" jako filtr. 
   W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

     —lub—

   * W przypadku istniejących aplikacji logiki: 
   
     * W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**. 

       —lub—

     * Między krokami, które chcesz dodać akcję wskaźnik myszy nad strzałką znajdującą się między krokami. 
     Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź "twitter" jako filtr. 
       W obszarze listy akcji wybierz akcję, którą chcesz.

1. Jeśli zostanie wyświetlony monit logować się do usługi Twitter, zarejestruj się teraz, dzięki czemu można Autoryzowanie dostępu dla aplikacji logiki.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Wyzwalacz usługi Twitter: Po wysłaniu nowego tweetu

Ten wyzwalacz jest uruchamiany przepływ pracy aplikacji logiki, jeśli wyzwalacz wykryje nowy tweet, na przykład z hasztagiem, #Seattle. Na przykład gdy znajdują się te tweety, można dodać plik z zawartością tweetów do magazynu, takich jak konta usługi Dropbox za pomocą łącznika usługi Dropbox. 

Można opcjonalnie dodać warunek, który kwalifikujących się tweetów muszą pochodzić z użytkowników z co najmniej określonej liczbie obserwatorów.

**Przykład Enterprise**: Ten wyzwalacz umożliwia monitorowanie tweetów o Twojej firmie i przekaż zawartość tweetów do usługi SQL database.

### <a name="twitter-action-post-a-tweet"></a>Akcja Twitter: Opublikuj tweet

Ta akcja publikuje tweet, ale można skonfigurować akcję, dzięki czemu tweet zawiera zawartość z tweetów znaleziono wyzwalaczu opisany wcześniej. 

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/twitterconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
