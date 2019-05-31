---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240267"
---
1. Otwórz projekt klienta pobrane za pomocą środowiska Xcode.

2. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do aplikacji mobilnej, który został utworzony. Na `Overview` bloku, zwróć uwagę na adres URL, który jest publiczny punkt końcowy dla twojej aplikacji mobilnej. Przykład — nazwa witryny, dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

3. Swift projektu, otwórz plik `ToDoTableViewController.swift` w tym folderze - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Nazwa aplikacji jest `ZUMOAPPNAME`.

4. W `viewDidLoad()` metody, Zastąp `ZUMOAPPURL` parametru z publicznym punktem końcowym powyżej.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    staje się
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Dla projektów języka Objective-C, otwórz plik `QSTodoService.m` w tym folderze - ZUMOAPPNAME/ZUMOAPPNAME. Nazwa aplikacji jest `ZUMOAPPNAME`.

6. W `init` metody, Zastąp `ZUMOAPPURL` parametru z publicznym punktem końcowym powyżej.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    staje się
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Naciśnij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację w symulatorze systemu iOS.

8. W aplikacji kliknij ikonę plusa ( **+** ), wpisz łatwy do rozpoznania tekst (np. *Ukończenie samouczka*), a następnie kliknij przycisk Zapisz. To spowoduje wysłanie żądania POST do wdrożonej wcześniej wewnętrznej bazy danych Azure. Wewnętrzna baza danych wstawia dane z żądania do tabeli SQL TodoItem i zwraca informacje na temat przechowywanych od niedawna elementów do aplikacji mobilnej. W aplikacji mobilnej dane te są wyświetlane na liście.

   ![Szybki start aplikacji uruchomionej w systemie iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
