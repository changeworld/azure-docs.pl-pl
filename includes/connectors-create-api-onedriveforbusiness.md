---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3a0cb48e7fe5f3eef101f644e4f72fcfa2689d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789458"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [usługi OneDrive](https://OneDrive.com) 

Aby można było używać konta usługi OneDrive dla Firm w aplikacji Logika, należy autoryzować aplikację Logika, aby połączyć się z kontem usługi OneDrive dla Firm. Na szczęście można to łatwo zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Oto kroki, aby autoryzować aplikację Logic w celu połączenia się z kontem usługi OneDrive dla Firm:

1. Aby utworzyć połączenie z usługą OneDrive dla Firm, w projektancie aplikacji Logika wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie wprowadź w polu wyszukiwania usługę *OneDrive dla Firm.* Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Jeśli nie utworzono wcześniej żadnych połączeń z usługą OneDrive dla Firm, zostanie wyświetlony monit o podanie poświadczeń usługi OneDrive dla Firm. Te poświadczenia będą używane do autoryzowania aplikacji Logika do łączenia się z danymi konta usługi OneDrive dla Firm i uzyskiwania do nich dostępu:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Podaj nazwę użytkownika i hasło usługi OneDrive dla Firm, aby autoryzować aplikację Logika:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Zwróć uwagę, że połączenie zostało utworzone i możesz teraz swobodnie wykonać inne kroki w aplikacji logiki:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

