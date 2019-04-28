---
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130062"
---
### <a name="prerequisites"></a>Wymagania wstępne
* [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) konta  

Przed skorzystaniem z kontem SFTP w aplikacji logiki, należy autoryzować aplikację logiki, aby nawiązać połączenie z kontem SFTP. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby nawiązać połączenie z kontem SFTP:  

1. Aby utworzyć połączenie SFTP, w Projektancie aplikacji logiki, wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *SFTP* w polu wyszukiwania. Wybierz **SFTP — po dodaniu lub zmodyfikowaniu pliku** wyzwalacza:  
   ![Obraz połączenia w trybie online SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Nie utworzono żadnych połączeń na używanie protokołu SFTP przed będzie pobrać monit podaj swoje poświadczenia SFTP. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych z kontem SFTP:  
   ![Obraz połączenia w trybie online SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:   
   ![Obraz połączenia w trybie online SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

