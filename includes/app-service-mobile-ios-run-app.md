---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890993"
---
1. Odwiedź witrynę [Azure Portal] na komputerze Mac. Kliknij przycisk **Wszystkie usługi** > **App Services** >utworzone zaplecze. W ustawieniach aplikacji mobilnej wybierz preferowany język:

    - Objective-C &ndash; **Szybki start** > **iOS (Objective-C)**
    - Swift &ndash; **Szybki start** > **iOS (Swift)**

    W obszarze **3. Skonfiguruj aplikację kliencką** i kliknij przycisk **Pobierz**. Spowoduje to pobranie kompletnego projektu systemu Xcode wstępnie skonfigurowanego do połączenia z wewnętrzną bazą danych. Otwórz projekt za pomocą środowiska Xcode.

1. Naciśnij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację w symulatorze systemu iOS.

1. W aplikacji kliknij ikonę plusa (**+**), wpisz łatwy do rozpoznania tekst (np. *Ukończenie samouczka*), a następnie kliknij przycisk Zapisz. To spowoduje wysłanie żądania POST do wdrożonej wcześniej wewnętrznej bazy danych Azure. Wewnętrzna baza danych wstawia dane z żądania do tabeli SQL TodoItem i zwraca informacje na temat przechowywanych od niedawna elementów do aplikacji mobilnej. W aplikacji mobilnej dane te są wyświetlane na liście.

   ![Szybki start aplikacji uruchomionej w systemie iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
