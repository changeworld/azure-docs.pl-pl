---
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119963"
---
#### <a name="prerequisites"></a>Wymagania wstępne
* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* A [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) konta 

Przed rozpoczęciem korzystania z konta usługi Dynamics w aplikacji logiki Autoryzuj aplikację logiki, aby połączyć się z kontem usługi CRM Online. Możesz to łatwo zrobić w aplikacji logiki w witrynie Azure portal. 

Autoryzuj aplikację logiki, aby połączyć się z kontem usługi CRM Online wykonując następujące czynności:

1. Tworzenie aplikacji logiki. W Projektancie aplikacji logiki wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej listy, a następnie wprowadź "dynamics" w polu wyszukiwania. Wybierz jedną z wyzwalaczy i akcji:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Jeśli nie utworzono jeszcze żadnych połączeń do usługi Dynamics, pojawia się monit o zalogowanie się przy użyciu poświadczeń usługi Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Wybierz **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz **Zaloguj**. 
   
    Te poświadczenia są używane, aby autoryzować aplikację logiki, aby nawiązać połączenie i dostęp do danych w ramach konta Dynamics. 
4. Należy zauważyć, że połączenie zostało utworzone. Teraz wykonaj kroki opisane w aplikacji logiki:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

