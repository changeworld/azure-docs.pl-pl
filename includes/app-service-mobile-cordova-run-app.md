---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 3217383b105c022aef42d8000f3a41cefea542fe
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133042"
---
1. Odwiedź witrynę [Azure Portal].
2. Kliknij pozycję **App Services** > utworzone zaplecze.
3. W ustawieniach aplikacji mobilnej kliknij kolejno opcje **Szybki start** > **Cordova**.
![Witryna Azure Portal z wyróżnioną pozycją Mobile Apps Quickstart][quickstart]
4. W obszarze **Konfigurowanie aplikacji klienta** wybierz opcję **Utwórz nową aplikację**, a następnie kliknij przycisk **Pobierz**.
2. Rozpakuj pobrany plik ZIP do katalogu na dysku twardym, przejdź do pliku rozwiązań (sln) i otwórz go za pomocą programu Visual Studio.
3. W programie Visual Studio wybierz platformę rozwiązania (Android, iOS lub Windows) z listy rozwijanej obok strzałki rozpoczęcia. Wybierz konkretny emulator lub konkretne urządzenie wdrożenia, klikając listę rozwijaną zielonej strzałki. Możesz użyć domyślnej platformy Android i emulatora Ripple. Samouczki bardziej zaawansowane (np. powiadomienia wypychane) będą wymagać wybrania obsługiwanego urządzenia lub emulatora.
4. Aby skompilować i uruchomić aplikację systemu Cordova, naciśnij klawisz F5 lub kliknij zieloną strzałkę. Jeśli w emulatorze widzisz okno dialogowe zabezpieczeń żądające dostępu do sieci, zaakceptuj je.
5. Po uruchomieniu aplikacji na urządzeniu lub w emulatorze wpisz znaczący tekst w obszarze **Wprowadź nowy tekst**, taki jak *Ukończ samouczek*, a następnie kliknij przycisk **Dodaj**.

Zaplecze wstawia dane z żądania do tabeli TodoItem bazy danych SQL Database i zwraca do aplikacji mobilnej informacje na temat przechowywanych od niedawna elementów. W aplikacji mobilnej dane te są wyświetlane na liście.

Kroki od 3 do 5 możesz powtórzyć dla innych platform.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
