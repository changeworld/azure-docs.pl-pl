---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: ad543ca2cd92895b9042ba795591523f00feb70a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202707"
---
### <a name="prerequisites"></a>Wymagania wstępne
* Konto usługi Wunderlist  

Zanim użyjesz swojego konta usługi Wunderlist w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi Wunderlist. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem usługi Wunderlist:

1. Aby utworzyć połączenie do aplikacji Wunderlist, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *Wunderlist* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![](./media/connectors-create-api-wunderlist/wunderlist-0.png)
2. Nie utworzono żadnych połączeń do aplikacji Wunderlist, zanim będzie pobrać monit podaj swoje poświadczenia aplikacji Wunderlist. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych na koncie usługi Wunderlist:   
   ![](./media/connectors-create-api-wunderlist/wunderlist-1.png)  
3. Podaj swoje poświadczenia, a następnie wybierz przycisk logowania  
   ![](./media/connectors-create-api-wunderlist/wunderlist-2.png)  
4. Użytkownik będzie następnie informację, co aplikacja logiki będzie mieć uprawnienia do wykonania na Twoim koncie usługi Wunderlist. Jeśli wyrazisz zgodę, wybierz przycisk aby wskazać umowy. 
   ![](./media/connectors-create-api-wunderlist/wunderlist-4.png)  
5. Na koniec wybierz pozycję **Autoryzuj** przycisku  
   ![](./media/connectors-create-api-wunderlist/wunderlist-5.png)  

