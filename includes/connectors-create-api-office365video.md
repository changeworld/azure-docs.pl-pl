---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c0dcba2dd003dfebdd9ce67bf4d78082c32824ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789493"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [wideo usługi Office 365](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6)  

Aby można było korzystać z konta wideo usługi Office 365 w aplikacji Logika, należy autoryzować aplikację Logika, aby połączyć się z kontem wideo usługi Office 365. Na szczęście można to łatwo zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Oto kroki, aby autoryzować aplikację Logika do łączenia się z kontem wideo usługi Office 365:  

1. Aby utworzyć połączenie z usługą Wideo usługi Office 365, w projektancie aplikacji Logika wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie wprowadź w polu wyszukiwania pozycję Wideo usługi Office *365.* Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![Krok tworzenia połączenia wideo w usłudze Office 365](./media/connectors-create-api-office365video/office365video-1.png)  
2. Jeśli nie utworzono wcześniej żadnych połączeń z usługą Wideo usługi Office 365, zostanie wyświetlony monit o podanie poświadczeń wideo usługi Office 365. Te poświadczenia będą używane do autoryzowania aplikacji logiki w celu nawiązania połączenia się z danymi konta wideo usługi Office 365 i uzyskiwania do nich dostępu:  
   ![Krok tworzenia połączenia wideo w usłudze Office 365](./media/connectors-create-api-office365video/office365video-2.png)  
3. Podaj poświadczenia, aby połączyć się z wideo usługi Office 365:  
   ![Krok tworzenia połączenia wideo w usłudze Office 365](./media/connectors-create-api-office365video/office365video-3.png)  
4. Zwróć uwagę, że połączenie zostało utworzone i możesz teraz swobodnie wykonać inne kroki w aplikacji logiki:  
   ![Krok tworzenia połączenia wideo w usłudze Office 365](./media/connectors-create-api-office365video/office365video-4.png)  

