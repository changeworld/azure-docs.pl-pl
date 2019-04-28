---
ms.openlocfilehash: 845b27b8efd66de87ec08e0b5b81bcc332dffdfb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129970"
---
### <a name="prerequisites"></a>Wymagania wstępne
* Konta usługi Twilio
* Zweryfikowano numer telefonu usługi Twilio, który umożliwia odbieranie wiadomości SMS
* Zweryfikowano numer telefonu usługi Twilio, który można wysłać wiadomości SMS

> [!NOTE]
> Jeśli używasz konta wersji próbnej usługi Twilio, możesz wysyłać wiadomości SMS do **zweryfikować** numerów telefonów.  
> 
> 

W aplikacji logiki, można korzystać z konta usługi Twilio, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi Twilio. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby nawiązać połączenie konta usługi Twilio:

1. Aby utworzyć połączenie z platformą Twilio, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *Twilio* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Nie utworzono żadnych połączeń usługi Twilio, zanim będzie pobrać monit podaj swoje poświadczenia Twilio. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych konta usługi Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Będziesz potrzebować **identyfikator konta Twilio** i **token dostępu Twilio** na pulpicie nawigacyjnym usługi Twilio, więc logowanie do konta usługi Twilio do pobrania tych dwóch rodzajów informacji:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio oraz Logic apps używać różnych nazw w celu zidentyfikowania tych dwóch rodzajów informacji. Poniżej przedstawiono, jak należy zamapować je w oknie dialogowym aplikacji logiki: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Wybierz **utworzyć połączenie** przycisku:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

