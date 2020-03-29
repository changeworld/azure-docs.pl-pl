---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789526"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [użytkowników usługi Office 365](https://office365.com)  

Aby można było korzystać z konta Użytkownicy usługi Office 365 w aplikacji Logika, należy autoryzować aplikację Logika, aby połączyć się z kontem użytkowników usługi Office 365. Na szczęście można to łatwo zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Oto kroki, aby autoryzować aplikację Logika do łączenia się z kontem użytkowników usługi Office 365:  

1. Aby utworzyć połączenie z użytkownikami usługi Office 365, w projektancie aplikacji Logika wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie wprowadź w polu wyszukiwania pozycję *Użytkownicy usługi Office 365.* Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![Krok tworzenia połączenia użytkowników usługi Office 365](./media/connectors-create-api-office365users/office365users-1.png)  
2. Jeśli nie utworzono wcześniej żadnych połączeń z użytkownikami usługi Office 365, zostanie wyświetlony monit o podanie poświadczeń użytkowników usługi Office 365. Te poświadczenia będą używane do autoryzowania aplikacji Logika do łączenia się z danymi konta użytkowników usługi Office 365 i uzyskiwania do nich dostępu:  
   ![Krok tworzenia połączenia użytkowników usługi Office 365](./media/connectors-create-api-office365users/office365users-2.png)  
3. Podaj nazwę użytkownika i hasło użytkowników usługi Office 365, aby autoryzować aplikację Logika:  
   ![Krok tworzenia połączenia użytkowników usługi Office 365](./media/connectors-create-api-office365users/office365users-3.png)  
4. Zwróć uwagę, że połączenie zostało utworzone i możesz teraz swobodnie wykonać inne kroki w aplikacji logiki:  
   ![Krok tworzenia połączenia użytkowników usługi Office 365](./media/connectors-create-api-office365users/office365users-4.png)  

