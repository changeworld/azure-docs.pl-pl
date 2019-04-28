---
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108985"
---
### <a name="prerequisites"></a>Wymagania wstępne
* [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) konta  

Zanim użyjesz konta FTP w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem FTP. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem FTP:  

1. Aby utworzyć połączenie z użyciem protokołu FTP, w Projektancie aplikacji logiki, wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *FTP* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Kroku tworzenia połączenia FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Nie utworzono żadnych połączeń FTP przed będzie pobrać monit podaj poświadczenia usługi FTP. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych na koncie FTP:  
   ![Kroku tworzenia połączenia FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Kroku tworzenia połączenia FTP](./media/connectors-create-api-ftp/ftp-3.png)  

