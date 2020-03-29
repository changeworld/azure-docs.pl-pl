---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789773"
---
1. W [witrynie Azure portal](https://portal.azure.com)utwórz pustą aplikację logiki. 

2. W Logic Apps Designer wprowadź "github" jako filtr. 

3. Wybierz łącznik GitHub i wyzwalacz, którego chcesz użyć.

   ![Wybierz łącznik GitHub i wyzwalacz](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Wszystkie przepływy pracy aplikacji logiki musi zaczynać się od wyzwalacza. Akcje można wybierać tylko wtedy, gdy przepływ pracy logiki jest już uruchamiany z wyzwalaczem. 

4. Jeśli wcześniej nie utworzyłeś połączenia, wybierz pozycję **Zaloguj się,** aby po wyświetleniu monitu podać poświadczenia usługi GitHub.  

   ![Logowanie się przy użyciu poświadczeń usługi GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Aplikacja logiki używa tych poświadczeń do autoryzowania łączenia i uzyskiwania dostępu do danych dla konta GitHub. 

5. Podaj nazwę użytkownika i hasło usługi GitHub, a następnie potwierdź autoryzację.

   ![Podaj poświadczenia i potwierdzaj autoryzację](./media/connectors-create-api-github/github-connector-authorize.png)   

   Połączenie jest teraz tworzone w witrynie Azure portal i jest gotowe do użycia.

6. Kontynuuj definiowanie przepływu pracy aplikacji logiki.

   ![Dodawanie kolejnych akcji do przepływu pracy aplikacji logiki](./media/connectors-create-api-github/github-connector-logic-app.png)

