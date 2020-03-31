---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183728"
---
1. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy projekt aplikacji ze Sklepu Windows. Następnie wybierz pozycję **Aplikacja Skojarzenia sklepu** > **ze Sklepem**.

    ![Kojarzenie aplikacji ze Sklepem Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. W kreatorze wybierz pozycję **Dalej**. Następnie zaloguj się za pomocą swojego konta Microsoft. W **obszarze Rezerwuj nową nazwę aplikacji**wpisz nazwę aplikacji, a następnie wybierz pozycję **Zarezerwuj**.
3. Po pomyślnym utworzeniu rejestracji aplikacji wybierz nową nazwę aplikacji. Wybierz **pozycję Dalej**, a następnie wybierz pozycję **Skojarz**. Ten proces dodaje wymagane informacje o rejestracji Sklepu Windows do manifestu aplikacji.
4. Powtórz kroki 1 i 3 dla projektu aplikacji ze Sklepu Windows Phone przy użyciu tej samej rejestracji, którą wcześniej utworzono dla aplikacji ze Sklepu Windows.  
5. Przejdź do [Centrum deweloperów systemu Windows,](https://dev.windows.com/en-us/overview)a następnie zaloguj się za pomocą swojego konta Microsoft. W **obszarze Moje aplikacje**wybierz nową rejestrację aplikacji. Następnie rozwiń**pozycję Powiadomienia wypychania** **usług** > .
6. Na stronie **Powiadomienia wypychane** w obszarze **Usługi powiadomień wypychanych systemu Windows (WNS) i Microsoft Azure Mobile Apps**wybierz pozycję **Witryna Usług Live**.  Zanotuj wartości **identyfikatora SID pakietu** i *bieżącą* wartość w **kluczu tajnym aplikacji**. 

    ![Ustawienie aplikacji w centrum deweloperów](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Klucz tajny aplikacji i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniaj nikomu tych wartości ani nie rozpowszechniaj ich za pomocą aplikacji.
   >
   >
