---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149741"
---
#### <a name="prerequisites"></a>Wymagania wstępne
* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* A [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) konta 

Przed skorzystaniem z kontem usługi OneDrive w aplikacji logiki, Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem usługi OneDrive.  Możesz to łatwo zrobić w aplikacji logiki w witrynie Azure portal. 

Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem usługi OneDrive wykonując następujące czynności:

1. Tworzenie aplikacji logiki. W Projektancie aplikacji logiki wybierz **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej listy i w polu wyszukiwania wprowadź "usługi onedrive". Wybierz jedną z wyzwalaczy i akcji:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Jeśli nie utworzono jeszcze żadnych połączeń w usłudze OneDrive, wyświetlany jest monit o zalogowanie się przy użyciu poświadczeń usługi OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Wybierz **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz **Zaloguj**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Te poświadczenia są używane, aby autoryzować aplikację logiki, aby nawiązać połączenie i dostęp do danych na swoim koncie usługi OneDrive. 
4. Wybierz **tak** Aby autoryzować aplikację logiki, aby użyć swojego konta usługi OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Należy zauważyć, że połączenie zostało utworzone. Teraz wykonaj kroki opisane w aplikacji logiki:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

