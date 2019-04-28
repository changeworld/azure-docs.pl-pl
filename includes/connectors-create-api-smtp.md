---
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130015"
---
### <a name="prerequisites"></a>Wymagania wstępne
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) konta  

Zanim użyjesz swojego konta SMTP w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem SMTP. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem SMTP:  

1. Aby utworzyć połączenie SMTP, w Projektancie aplikacji logiki, wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *SMTP* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Nie utworzono żadnych połączeń SMTP przed będzie pobrać monit podaj poświadczenia usługi SMTP. Te poświadczenia będą służyć do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych konto SMTP:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

