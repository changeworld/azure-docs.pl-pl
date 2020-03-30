---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183729"
---
1. Na komputerze Mac uruchom program **Keychain Access**. Na lewym pasku nawigacyjnym w **obszarze Kategoria**otwórz pozycję **Moje certyfikaty**. Znajdź certyfikat SSL pobrany w poprzedniej sekcji, a następnie ujawnij jego zawartość. Wybierz tylko certyfikat (nie wybieraj klucza prywatnego). Następnie [wyeksportuj go](https://support.apple.com/kb/PH20122?locale=en_US).
2. W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Przeglądaj wszystkie** > **usługi aplikacji**. Następnie wybierz zaplecze aplikacji mobilnych. 
3. W obszarze **Ustawienia**wybierz pozycję **Wypychanie usługi aplikacji**. Następnie wybierz nazwę centrum powiadomień. 
4. Przejdź do > **certyfikatu przekazywania** **usług powiadomień push firmy Apple**. Przekaż plik .p12, wybierając właściwy **tryb** (w zależności od tego, czy certyfikat SSL klienta z wcześniejszego jest produkcyjny, czy piaskownicy). Zapisz wszelkie zmiany.

Usługa jest teraz skonfigurowana do pracy z powiadomieniami wypychaniem w iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
