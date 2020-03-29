---
title: Łączenie się z usługą Yammer za pomocą aplikacji Logika platformy Azure
description: Automatyzowanie zadań i przepływów pracy monitorujących, publikowanych i zarządzania wiadomościami, kanałami informacyjnymi i innymi usługami w usłudze Yammer przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194c08d034d44ba0a4472b3b516fc45d1d262d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789073"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorowanie konta usługi Yammer i zarządzanie nim przy użyciu usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika usługi Yammer można tworzyć zautomatyzowane zadania i przepływy pracy, które monitorują wiadomości, źródła danych i inne elementy na koncie usługi Yammer oraz nimi, a także inne akcje, na przykład:

* Monitoruj, gdy w obserwowanych kanałach informacyjnych i grupach pojawią się nowe wiadomości.
* Otrzymuuj wiadomości, grupy, sieci, dane użytkowników i inne.
* Publikuj i polub wiadomości.

Można użyć wyzwalaczy, które otrzymują odpowiedzi z konta Yammer i udostępnić dane wyjściowe innym działaniom. Można użyć akcji, które wykonują zadania przy użyciu konta usługi Yammer. Można również mieć inne akcje użyć danych wyjściowych z yammer akcji. Na przykład, gdy nowe wiadomości pojawiają się w kanałach informacyjnych lub grupach, można udostępnić te wiadomości z łącznika Slack. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto usługi Yammer i poświadczenia użytkownika

   Poświadczenia autoryzują aplikację logiki do tworzenia połączenia i uzyskiwania dostępu do konta usługi Yammer.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Yammer. Aby rozpocząć od wyzwalacza usługi Yammer, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Yammer, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-yammer"></a>Łączenie się z usługi Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "yammer" jako filtr. 
   W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz. 

     — lub —

   * W przypadku istniejących aplikacji logiki: 
   
     * W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. 

       — lub —

     * Między krokami, w których chcesz dodać akcję, przesuń wskaźnik myszy na strzałkę między krokami. 
     Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wpisz "yammer" jako filtr. 
       W obszarze listy akcje wybierz odpowiednią akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do usługi Yammer, zaloguj się teraz, aby zezwolić na dostęp.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/yammer/)łącznika .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)