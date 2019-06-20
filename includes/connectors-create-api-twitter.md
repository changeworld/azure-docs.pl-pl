---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: c2e311a85430abdad4736e8c88d96b18d182ecf9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205776"
---
### <a name="prerequisites"></a>Wymagania wstępne
* Konto w serwisie Twitter 

Zanim użyjesz kontem w usłudze Twitter w aplikacji logiki, należy autoryzować aplikację logiki, aby nawiązać połączenie z kontem w usłudze Twitter. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby nawiązać połączenie z kontem w usłudze Twitter:

1. Aby utworzyć połączenie do usługi Twitter, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *Twitter* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Obraz łączenia z serwisem Twitter 0](./media/connectors-create-api-twitter/twitter-0.png)
2. Nie utworzono żadnych połączeń z serwisem Twitter, zanim będzie pobrać monit podaj swoje poświadczenia Twittera. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych na Twoim koncie usługi Twitter:  
   ![Obraz łączenia z serwisem Twitter 1](./media/connectors-create-api-twitter/twitter-1.png)  
3. Podaj nazwę użytkownika usługi Twitter i hasło, aby autoryzować aplikację logiki:  
   ![Obraz łączenia z serwisem Twitter 2](./media/connectors-create-api-twitter/twitter-2.png)  
4. Upewnij się, Twoje autoryzacji:  
   ![Obraz łączenia z serwisem Twitter 3](./media/connectors-create-api-twitter/twitter-3.png)  
5. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Obraz łączenia z serwisem Twitter 4](./media/connectors-create-api-twitter/twitter-4.png)

