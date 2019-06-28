---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183728"
---
1. W Eksploratorze rozwiązań w usłudze Visual Studio kliknij prawym przyciskiem myszy projekt aplikacji Windows Store. Następnie wybierz pozycję **Store** > **Skojarz aplikację ze Store**.

    ![Skojarz aplikację z Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. W kreatorze Wybierz **dalej**. Następnie zaloguj się przy użyciu konta Microsoft. W **Zarezerwuj nazwę nowej aplikacji**, wpisz nazwę aplikacji, a następnie wybierz **rezerwy**.
3. Po pomyślnym utworzeniu rejestracji aplikacji, wybierz nazwę nowej aplikacji. Wybierz **dalej**, a następnie wybierz pozycję **skojarzyć**. Ten proces powoduje dodanie wymaganych informacji dotyczących rejestracji Windows Store do manifestu aplikacji.
4. Powtórz kroki 1 i 3 dla projektu aplikacji Windows Phone Store przy użyciu tego samego rejestracji, wcześniej utworzone dla aplikacji Windows Store.  
5. Przejdź do [Centrum deweloperów Windows](https://dev.windows.com/en-us/overview), a następnie zaloguj się przy użyciu konta Microsoft. W **Moje aplikacje**, wybierz pozycję Rejestracja nowej aplikacji. Następnie rozwiń **usług** > **powiadomienia wypychane**.
6. Na **powiadomienia wypychane** w obszarze **Windows Push Notification usługi WNS i Microsoft Azure Mobile Apps**, wybierz opcję **witryna usług Live**.  Zanotuj wartości **identyfikator SID pakietu** i *bieżącego* wartość w **klucz tajny aplikacji**. 

    ![Ustawienia aplikacji w Centrum deweloperów](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Klucz tajny aplikacji i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniać tych wartości z dowolnymi osobami ani nie rozpowszechniaj ich razem z aplikacją.
   >
   >
