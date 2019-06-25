---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: fe726986b3f93ab3bb447b8973727a658ac1c706
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183493"
---
### <a name="prerequisites"></a>Wymagania wstępne
* A [połączeń z dyskiem Google](https://www.google.com/drive/) konta  

Aby korzystać z konta usługi GoogleDrive, w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi GoogleDrive. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem połączeń z dyskiem Google:  

1. Aby utworzyć połączenie do usługi GoogleDrive, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *połączeń z dyskiem Google* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Kroku tworzenia połączenia dla połączeń z dyskiem Google](./media/connectors-create-api-googledrive/googledrive-1.png)  
2. Nie utworzono żadnych połączeń do usługi GoogleDrive, zanim będzie pobrać monit podaj swoje poświadczenia usługi GoogleDrive. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych Twoje konto połączeń z dyskiem Google:  
   ![Kroku tworzenia połączenia dla połączeń z dyskiem Google](./media/connectors-create-api-googledrive/googledrive-2.png)  
3. Podaj swój adres e-mail połączeń z dyskiem Google:  
   ![Kroku tworzenia połączenia dla połączeń z dyskiem Google](./media/connectors-create-api-googledrive/googledrive-3.png)  
4. Podaj hasło połączeń z dyskiem Google, aby autoryzować aplikację logiki:  
   ![Kroku tworzenia połączenia dla połączeń z dyskiem Google](./media/connectors-create-api-googledrive/googledrive-4.png)
5. Zezwalaj na połączenie do usługi GoogleDrive  
   ![Kroku tworzenia połączenia dla połączeń z dyskiem Google](./media/connectors-create-api-googledrive/googledrive-5.png)  
6. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Kroku tworzenia połączenia dla połączeń z dyskiem Google](./media/connectors-create-api-googledrive/googledrive-6.png)  

