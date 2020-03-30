---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789658"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure; można utworzyć [darmowe konto](https://azure.microsoft.com/free)
* Konto [usługi OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Aby można było używać konta usługi OneDrive w aplikacji logiki, autoryzuj aplikację logiki, aby połączyć się z kontem usługi OneDrive.  Można to łatwo zrobić w aplikacji logiki w witrynie Azure portal. 

Autoryzuj aplikację logiki, aby połączyć się z kontem usługi OneDrive, wykonując następujące czynności:

1. Tworzenie aplikacji logiki. W projektancie aplikacji logiki wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie wprowadź "onedrive" w polu wyszukiwania. Wybierz jeden z wyzwalaczy lub akcji:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Jeśli nie utworzono wcześniej żadnych połączeń z usługą OneDrive, zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń usługi OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Wybierz pozycję **Zaloguj się**i wprowadź nazwę użytkownika i hasło. Wybierz **pozycję Zaloguj się:**  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Te poświadczenia są używane do autoryzowania aplikacji logiki do łączenia się z danymi na koncie usługi OneDrive i uzyskiwania do nich dostępu. 
4. Wybierz **pozycję Tak,** aby autoryzować aplikację logiki do korzystania z konta usługi OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Zwróć uwagę, że połączenie zostało utworzone. Teraz przejdź do innych kroków w aplikacji logiki:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

