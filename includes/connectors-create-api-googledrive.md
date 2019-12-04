---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 96943405f3fce02b8a07158f797dd204eb4bb8e2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789722"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [dyskiem Google](https://www.google.com/drive/)  

Aby można było używać konta dyskiem Google w aplikacji logiki, należy autoryzować aplikację logiki do łączenia się z kontem dyskiem Google. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem dyskiem Google:  

1. Aby utworzyć połączenie z usługą dyskiem Google, w Projektancie aplikacji logiki wybierz pozycję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź *dyskiem Google* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![krok tworzenia połączenia dyskiem Google](./media/connectors-create-api-googledrive/googledrive-1.png)  
2. Jeśli nie utworzono żadnych połączeń z dyskiem Google przed, otrzymasz monit o podanie poświadczeń dyskiem Google. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do danych konta dyskiem Google:  
   ![Krok tworzenia połączenia dyskiem Google](./media/connectors-create-api-googledrive/googledrive-2.png)  
3. Podaj adres e-mail dyskiem Google:  
   ![Krok tworzenia połączenia dyskiem Google](./media/connectors-create-api-googledrive/googledrive-3.png)  
4. Podaj hasło dyskiem Google, aby autoryzować aplikację logiki:  
   ![Krok tworzenia połączenia dyskiem Google](./media/connectors-create-api-googledrive/googledrive-4.png)
5. Zezwalaj na połączenie z usługą dyskiem Google  
   ![Krok tworzenia połączenia dyskiem Google](./media/connectors-create-api-googledrive/googledrive-5.png)  
6. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![Krok tworzenia połączenia dyskiem Google](./media/connectors-create-api-googledrive/googledrive-6.png)  

