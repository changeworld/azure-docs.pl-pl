---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: ebae0fac5edc0bb79e6a19d8bdc741960f0b0e20
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183489"
---
### <a name="prerequisites"></a>Wymagania wstępne
* [Użytkownicy usługi Office 365](https://office365.com) konta  

Zanim użyjesz swojego konta użytkowników usługi Office 365 w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem Użytkownicy usługi Office 365. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem Użytkownicy usługi Office 365:  

1. Aby utworzyć połączenie użytkownicy usługi Office 365, w Projektancie aplikacji logiki, wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *użytkownicy usługi Office 365* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Kroku tworzenia połączenia użytkownicy usługi Office 365](./media/connectors-create-api-office365users/office365users-1.png)  
2. Nie utworzono żadnych połączeń, aby użytkownicy usługi Office 365 przed będzie pobrać monit podaj poświadczenia usługi Office 365 Users. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych konta użytkowników usługi Office 365:  
   ![Kroku tworzenia połączenia użytkownicy usługi Office 365](./media/connectors-create-api-office365users/office365users-2.png)  
3. Podaj nazwę użytkownika w użytkownicy usługi Office 365 i hasło, aby autoryzować aplikację logiki:  
   ![Kroku tworzenia połączenia użytkownicy usługi Office 365](./media/connectors-create-api-office365users/office365users-3.png)  
4. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Kroku tworzenia połączenia użytkownicy usługi Office 365](./media/connectors-create-api-office365users/office365users-4.png)  

