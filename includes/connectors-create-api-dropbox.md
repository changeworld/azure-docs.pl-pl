---
ms.openlocfilehash: 3f4430631a664f81f53f9df1f46ebc27c635de36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860206"
---
### <a name="prerequisites"></a>Wymagania wstępne
* A [Dropbox](https://www.Dropbox.com/) konta 

Zanim użyjesz Twoim koncie Dropbox w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi Dropbox. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem usługi Dropbox:

1. Aby utworzyć połączenie do usługi Dropbox, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *Dropbox* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Dropbox w kroku 1](./media/connectors-create-api-dropbox/dropbox-1.png)
2. Nie utworzono żadnych połączeń do usługi Dropbox, będzie pobrać monit podaj swoje poświadczenia usługi Dropbox. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych na Twoim koncie Dropbox:  
   ![Dropbox w kroku 2](./media/connectors-create-api-dropbox/dropbox-2.png)
3. Podaj, Dropbox, nazwę użytkownika i hasło, aby autoryzować aplikację logiki:  
   ![Dropbox w kroku 3](./media/connectors-create-api-dropbox/dropbox-3.png)   
4. Zezwolić aplikacji logiki do używania konta usługi Dropbox:  
   ![Dropbox w kroku 4](./media/connectors-create-api-dropbox/dropbox-4.png)
5. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Dropbox krok 5](./media/connectors-create-api-dropbox/dropbox-5.png)   

