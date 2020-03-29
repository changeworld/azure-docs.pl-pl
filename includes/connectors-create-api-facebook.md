---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c94e7d1fb5c42a0246b38c88eb097c75ec8ca4e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789859"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [na Facebooku](https://www.facebook.com/) 

Aby można było korzystać z konta na Facebooku w aplikacji Logic, musisz autoryzować aplikację Logic, aby połączyć się z kontem na Facebooku. Na szczęście można to łatwo zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Oto kroki, aby autoryzować aplikację Logic do łączenia się z kontem na Facebooku:

1. Aby utworzyć połączenie z Facebookiem, w projektancie aplikacji Logika wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie wprowadź pozycję *Facebook* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![facebook krok 1](./media/connectors-create-api-facebook/facebook-1.png)
2. Jeśli nie utworzyłeś wcześniej żadnych połączeń z Facebookiem, zostanie wyświetlony monit o podanie poświadczeń na Facebooku. Te poświadczenia będą używane do autoryzowania aplikacji Logic w celu nawiązania połączenia się z danymi konta na Facebooku i uzyskiwania do nich dostępu:  
   ![facebook krok 2](./media/connectors-create-api-facebook/facebook-2.png)
3. Podaj nazwę użytkownika i hasło facebooka, aby autoryzować aplikację Logic:  
   ![facebook krok 3](./media/connectors-create-api-facebook/facebook-3.png)   
4. Zwróć uwagę, że połączenie zostało utworzone i możesz teraz swobodnie wykonać inne kroki w aplikacji logiki:  
   ![facebook krok 4](./media/connectors-create-api-facebook/facebook-4.png)   

