---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 94f18b20d84a1929368053c44b4f07793e385b38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789566"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [MicrosoftTranslator](https://www.microsoft.com/translator)  

Aby można było używać konta MicrosoftTranslator w aplikacji logiki, należy autoryzować aplikację Logika, aby połączyć się z kontem MicrosoftTranslator. Na szczęście można to łatwo zrobić z poziomu aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono kroki, aby autoryzować aplikację Logic, aby połączyć się z kontem MicrosoftTranslator:  

1. Aby utworzyć połączenie z programem MicrosoftTranslator, w projektancie aplikacji Logika wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie wprowadź program *MicrosoftTranslator* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![Krok tworzenia połączenia microsofttranslator](./media/connectors-create-api-microsofttranslator/microsofttranslator-1.png)  
2. Jeśli nie utworzono żadnych połączeń z programem MicrosoftTranslator przed, zostanie wyświetlony monit o podanie poświadczeń MicrosoftTranslator. Te poświadczenia będą używane do autoryzowania aplikacji logiki do łączenia się z danymi konta MicrosoftTranslator i uzyskiwania do nich dostępu:  
   ![Krok tworzenia połączenia microsofttranslator](./media/connectors-create-api-microsofttranslator/microsofttranslator-2.png)  
3. Zwróć uwagę, że połączenie zostało utworzone i możesz teraz swobodnie wykonać inne kroki w aplikacji logiki:  
   ![Krok tworzenia połączenia microsofttranslator](./media/connectors-create-api-microsofttranslator/microsofttranslator-3.png)  

