---
title: Łączenie się z aplikacją Wunderlist z usługi Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy monitoruj listy, zadania, przypomnienia i inne elementy na koncie Wunderlist oraz zarządzają nimi, korzystając z usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789124"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorowanie aplikacji Wunderlist i zarządzanie nią przy użyciu aplikacji Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika Wunderlist można tworzyć zautomatyzowane zadania i przepływy pracy, które monitorują listy zadań, zadania, przypomnienia i inne elementy związane z wykonywaniem zadań, zadania, przypomnienia i inne elementy na koncie Wunderlist, a także inne akcje, na przykład:

* Monitoruj, kiedy tworzone są nowe zadania, gdy zadania są należne lub występują przypomnienia.
* Tworzenie list, notatek, zadań, podzadęć i innych zarządzania nimi oraz zarządzanie nimi.
* Ustawianie przypomnień.
* Pobierz listy, zadania, podzadacje, przypomnienia, pliki, notatki, komentarze i inne.

[Wunderlist](https://www.wunderlist.com/) to usługa, która pomaga planować, zarządzać i kończyć projekty, listy zadań do wykonania i zadania — na dowolnym urządzeniu i w dowolnym miejscu. Można użyć wyzwalaczy, które otrzymują odpowiedzi z konta Wunderlist i udostępnić dane wyjściowe dla innych akcji. Można użyć akcji, które wykonują zadania z kontem Wunderlist. Można również mieć inne akcje użyć danych wyjściowych z akcji Wunderlist. Na przykład, gdy nowe zadania są należne, można publikować wiadomości za pomocą łącznika Slack. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto Wunderlist i poświadczenia użytkownika

   Poświadczenia autoryzować aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta Wunderlist.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Yammer. Aby rozpocząć od wyzwalacza Wunderlist, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Wunderlist, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-wunderlist"></a>Łączenie się z wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logicznych w polu wyszukiwania wpisz "wunderlist" jako filtr. 
   W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

     — lub —

   * W przypadku istniejących aplikacji logiki: 
   
     * W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 

       — lub —

     * Między krokami, w których chcesz dodać akcję, przesuń wskaźnik myszy na strzałkę między krokami. 
     Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wpisz "wunderlist" jako filtr. 
       W obszarze listy akcje wybierz odpowiednią akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do Wunderlist, zaloguj się teraz, aby zezwolić na dostęp.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/wunderlist/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)