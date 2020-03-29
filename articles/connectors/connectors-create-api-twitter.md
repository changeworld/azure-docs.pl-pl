---
title: Łączenie się z aplikacjami Azure Logic Apps z funkcją Twitter
description: Automatyzuj zadania i przepływy pracy, które monitorują tweety i zarządzają nimi, a także utrzymuj dane o obserwujących, obserwowanych użytkownikach, innych użytkownikach, osiach czasu i innych urządzeniach z konta na Twitterze za pomocą usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789090"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorowanie twittera i zarządzanie nim przy użyciu aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika Twittera można tworzyć zautomatyzowane zadania i przepływy pracy, które monitorują i zarządzają danymi, na których Ci zależy w serwisie Twitter, takimi jak tweety, obserwatorzy, użytkownicy i obserwowani użytkownicy, osie czasu i inne, a także inne działania, na przykład:

* Monitoruj, publikuj i szukaj tweetów.
* Uzyskaj dane, takie jak obserwujący, obserwowani użytkownicy, osie czasu i inne.

Możesz użyć wyzwalaczy, które otrzymują odpowiedzi z twojego konta na Twitterze i udostępniają dane wyjściowe innym działaniom. Możesz użyć akcji, które wykonują zadania na koncie twitterowym. Możesz również mieć inne akcje używać danych wyjściowych z akcji Twitter. Na przykład, gdy pojawi się nowy tweet z określonym hasztagiem, możesz wysyłać wiadomości za pomocą łącznika Slack. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto na Twitterze i poświadczenia użytkownika

   Poświadczenia autoryzują aplikację logiki do tworzenia połączenia i uzyskiwania dostępu do konta na Twitterze.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do swojego konta na Twitterze. Aby rozpocząć od wyzwalacza Twittera, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Twitter, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalacz **cyklu.**

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logicznych w polu wyszukiwania wpisz "twitter" jako filtr. 
   W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

     — lub —

   * W przypadku istniejących aplikacji logiki: 
   
     * W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 

       — lub —

     * Między krokami, w których chcesz dodać akcję, przesuń wskaźnik myszy na strzałkę między krokami. 
     Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wpisz "twitter" jako filtr. 
       W obszarze listy akcje wybierz odpowiednią akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do serwisu Twitter, zaloguj się teraz, aby autoryzować dostęp do aplikacji logiki.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Wyzwalacz Twitter: Po opublikowaniu nowego tweeta

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki, gdy wyzwalacz wykryje nowy tweet, na przykład z hashtagiem #Seattle. Na przykład, gdy te tweety zostaną znalezione, możesz dodać plik z zawartością tweetów do magazynu, na przykład konto Dropbox za pomocą łącznika Dropbox. 

Opcjonalnie możesz dołączyć warunek, że kwalifikujące się tweety muszą pochodzić od użytkowników z co najmniej określoną liczbą obserwujących.

**Przykład przedsiębiorstwa:** Możesz użyć tego wyzwalacza do monitorowania tweetów o firmie i przekazywania zawartości tweetów do bazy danych SQL.

### <a name="twitter-action-post-a-tweet"></a>Akcja na Twitterze: Opublikuj tweet

Ta akcja publikuje tweet, ale możesz skonfigurować akcję tak, aby tweet zawierał zawartość tweetów znalezionych przez wcześniej opisany wyzwalacz. 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/twitterconnector/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
