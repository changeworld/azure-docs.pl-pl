---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149735"
---
#### <a name="prerequisites"></a>Wymagania wstępne
* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Usługi Office 365](https://office365.com) konta  

Przed rozpoczęciem korzystania z konta usługi Office 365 w aplikacji logiki Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem usługi Office 365. Możesz to łatwo zrobić w aplikacji logiki w witrynie Azure portal.  

Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem usługi Office 365 wykonując następujące czynności:

1. Tworzenie aplikacji logiki. W Projektancie aplikacji logiki wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej listy, a następnie wprowadź "office 365" w polu wyszukiwania. Wybierz jedną z wyzwalaczy i akcji:  
    ![Kroku tworzenia połączenia usługi Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Jeśli nie utworzono jeszcze żadnych połączeń do usługi Office 365, wyświetlany jest monit o zalogowanie się przy użyciu poświadczeń usługi Office 365:  
    ![Kroku tworzenia połączenia usługi Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Wybierz **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz **Zaloguj**:  
    ![Kroku tworzenia połączenia usługi Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Te poświadczenia są używane do autoryzowania Twojej aplikacji logiki, aby połączyć się i uzyskać dostępu do konta usługi Office 365. 
4. Należy zauważyć, że połączenie zostało utworzone. Teraz wykonaj kroki opisane w aplikacji logiki:   
    ![Kroku tworzenia połączenia usługi Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

