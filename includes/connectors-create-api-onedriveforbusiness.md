---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 48cd41d432c5969fc4128b055ca61fc86a57bdd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149656"
---
### <a name="prerequisites"></a>Wymagania wstępne
* A [OneDrive](http://OneDrive.com) konta 

Zanim użyjesz usługi OneDrive dla firm konta w aplikacji logiki, należy zezwolić aplikacji logiki, aby nawiązać połączenie z kontem usługi OneDrive dla firm. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby nawiązać połączenie z kontem usługi OneDrive dla firm:

1. Aby utworzyć połączenie do usługi OneDrive dla firm, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *usługi OneDrive dla firm* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Nie utworzono żadnych połączeń w usłudze OneDrive dla firm, zanim będzie pobrać monit zapewnia usługi OneDrive dla firm poświadczeń. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do usługi OneDrive dla danych konto firmowe:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Podaj w usłudze OneDrive dla firm, nazwę użytkownika i hasło do autoryzowania aplikacji logiki:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

