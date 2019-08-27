---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 6c905842778f43196ac463b99993a3cbcd436fa5
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036751"
---
### <a name="prerequisites"></a>Wymagania wstępne
* Konto w [usłudze OneDrive](https://OneDrive.com) 

Aby można było korzystać z konta usługi OneDrive dla firm w aplikacji logiki, należy autoryzować aplikację logiki w celu nawiązania połączenia z kontem usługi OneDrive dla firm. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem usługi OneDrive dla firm:

1. Aby utworzyć połączenie z usługą OneDrive dla firm, w Projektancie aplikacji logiki wybierz pozycję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie w polu wyszukiwania wprowadź wartość *OneDrive dla firm* . Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Jeśli wcześniej nie utworzono żadnych połączeń z usługą OneDrive dla firm, otrzymasz monit o podanie poświadczeń usługi OneDrive dla firm. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia i uzyskiwania dostępu do danych konta usługi OneDrive dla firm:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Podaj nazwę użytkownika i hasło usługi OneDrive dla firm, aby autoryzować aplikację logiki:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

