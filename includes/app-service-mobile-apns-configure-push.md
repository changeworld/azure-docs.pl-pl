---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 25bb5cfb0073f7533faddec43b38fb5031b82a43
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811611"
---
1. Na komputerze Mac, należy uruchomić **dostęp do pęku kluczy**. Na pasku nawigacyjnym po lewej stronie w obszarze **kategorii**, otwórz **moje certyfikaty**. Znajdź certyfikat SSL, który został pobrany w poprzedniej sekcji, a następnie ujawnić jego zawartość. Wybierz tylko certyfikat (nie należy wybierać klucz prywatny). Następnie [go wyeksportować](https://support.apple.com/kb/PH20122?locale=en_US).
2. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **Przeglądaj wszystkie** > **App Services**. Następnie wybierz usługę Mobile Apps zapleczem. 
3. W obszarze **ustawienia**, wybierz opcję **wypychania usługi App Service**. Następnie wybierz nazwę Centrum powiadomień. 
4. Przejdź do **Apple Push Notification Services** > **Przekaż certyfikat**. Przekazywanie pliku p12, wybierając poprawny **tryb** (w zależności od tego, czy certyfikat klienta SSL z wcześniejszego to piaskownica lub produkcji). Zapisz zmiany.

Usługa jest teraz skonfigurowane do pracy za pomocą powiadomień wypychanych w systemie iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
