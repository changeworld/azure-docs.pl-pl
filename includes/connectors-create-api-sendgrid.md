---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b06c57f50f759677770839ae06cb05e4de36f84c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205807"
---
### <a name="prerequisites"></a>Wymagania wstępne
* A [SendGrid](https://www.SendGrid.com/) konta 

Zanim użyjesz swojego konta usługi SendGrid w aplikacji logiki, należy autoryzować aplikację logiki, aby nawiązać połączenie z Twojego konta SendGrid. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem usługi SendGrid:

1. Aby utworzyć połączenie usługi SendGrid, w Projektancie aplikacji logiki, wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *SendGrid* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Usługa SendGrid w kroku 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Nie utworzono żadnych połączeń z usługą SendGrid przed będzie pobrać monit podaj swoje poświadczenia usługi SendGrid. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do Twojego konta SendGrid danych:  
   ![Usługa SendGrid w kroku 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Usługa SendGrid w kroku 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

