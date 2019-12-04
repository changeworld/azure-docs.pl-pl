---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 635a69fc8e75e3a6249728dc1df31cf839b345c6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789607"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* Konto [pakietu Office 365](https://office365.com)  

Przed skorzystaniem z konta Office 365 w aplikacji logiki Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem pakietu Office 365. Można to łatwo zrobić w aplikacji logiki na Azure Portal.  

Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem usługi Office 365, wykonując następujące czynności:

1. Utwórz aplikację logiki. W projektancie Logic Apps wybierz pozycję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź ciąg "Office 365" w polu wyszukiwania. Wybierz jeden z wyzwalaczy lub akcji:  
    ![krok tworzenia połączenia z pakietem Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Jeśli nie utworzono wcześniej żadnych połączeń z pakietem Office 365, zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń pakietu Office 365:  
    ![Krok tworzenia połączenia z pakietem Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Wybierz pozycję **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz pozycję **Zaloguj się**:  
    ![krok tworzenia połączenia z pakietem Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Te poświadczenia służą do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do konta Office 365. 
4. Zwróć uwagę na to, że połączenie zostało utworzone. Teraz wykonaj inne czynności w aplikacji logiki:   
    ![Krok tworzenia połączenia z pakietem Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

