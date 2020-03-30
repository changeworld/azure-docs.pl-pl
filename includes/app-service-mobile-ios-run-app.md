---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240267"
---
1. Otwórz pobrany projekt klienta przy użyciu xcode.

2. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

3. W przypadku projektu Swift `ToDoTableViewController.swift` otwórz plik w tym folderze — ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Nazwa aplikacji `ZUMOAPPNAME`to .

4. W `viewDidLoad()` metodzie `ZUMOAPPURL` zastąp parametr publicznym punktem końcowym powyżej.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    Staje się
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. W przypadku projektu Objective-C `QSTodoService.m` otwórz plik w tym folderze — ZUMOAPPNAME/ZUMOAPPNAME. Nazwa aplikacji `ZUMOAPPNAME`to .

6. W `init` metodzie `ZUMOAPPURL` zastąp parametr publicznym punktem końcowym powyżej.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    Staje się
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Naciśnij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację w symulatorze systemu iOS.

8. W aplikacji kliknij ikonę**+** plus ( ), wpisz sensowny tekst, na przykład *Ukończ samouczek,* a następnie kliknij przycisk zapisz. To spowoduje wysłanie żądania POST do wdrożonej wcześniej wewnętrznej bazy danych Azure. Wewnętrzna baza danych wstawia dane z żądania do tabeli SQL TodoItem i zwraca informacje na temat przechowywanych od niedawna elementów do aplikacji mobilnej. W aplikacji mobilnej dane te są wyświetlane na liście.

   ![Szybki start aplikacji uruchomionej w systemie iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
