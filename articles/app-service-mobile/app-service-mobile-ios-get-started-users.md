---
title: Dodawanie uwierzytelniania w systemie iOS w usłudze Azure Mobile Apps
description: Dowiedz się, jak używać usługi Azure Mobile Apps uwierzytelniać użytkowników aplikacji systemu iOS za pomocą różnych dostawców tożsamości, obejmującej AAD, Google, Facebook, Twitter i firmy Microsoft.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: e0eeee05ebad2e8148752f988bbbc2f6a0d7c296
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592698"
---
# <a name="add-authentication-to-your-ios-app"></a>Dodawanie uwierzytelniania do aplikacji systemu iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym samouczku, dodać authentication [iOS szybki start] projektu przy użyciu dostawcy tożsamości obsługiwane. W tym samouczku jest oparta na [iOS szybki start] — samouczek, należy najpierw wykonać.

## <a name="register"></a>Zarejestrować aplikację do uwierzytelniania i konfigurowanie usługi aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych przekierowania zewnętrzne adresy URL

Bezpieczne uwierzytelnianie wymaga Zdefiniuj nowy schemat adresu URL dla aplikacji.  Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania.  W tym samouczku używamy schemat adresu URL _appname_ w ciągu.  Można jednak użyć dowolnego wybranego schematu URL.  Powinien być unikatowy w aplikacji mobilnej.  Aby włączyć przekierowywanie po stronie serwera th:

1. W [portalu Azure], wybierz usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. Kliknij przycisk **usługi Azure Active Directory** w obszarze **dostawców uwierzytelniania** sekcji.

4. Ustaw **tryb zarządzania** do **zaawansowane**.

5. W **dozwolone zewnętrznych adresów URL przekierowań**, wprowadź `appname://easyauth.callback`.  _Appname_ ten ciąg jest schemat adresu URL dla aplikacji mobilnej.  Musi on być zgodny normalne Specyfikacja adresu URL dla protokołu (Użyj litery i tylko cyfry i rozpoczyna się od litery).  Należy zanotuj ciąg, który wybrany jako trzeba będzie dostosować kodu aplikacji mobilnej przy użyciu schemat adresu URL w kilku miejscach.

6. Kliknij przycisk **OK**.

7. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia dla uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Xcode, naciśnij klawisz **Uruchom** do uruchomienia aplikacji. Zgłoszony wyjątek, ponieważ aplikacja próbuje uzyskać dostęp do wewnętrznej bazy danych jako nieuwierzytelniony użytkownik, ale *TodoItem* tabeli teraz wymaga uwierzytelniania.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
**Objective-C**:

1. Na komputerze Mac Otwórz *QSTodoListViewController.m* w środowisku Xcode i dodaj następującą metodę:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Zmień *google* do *microsoftaccount*, *twitter*, *facebook*, lub *windowsazureactivedirectory* Jeśli nie używasz Google jako dostawcy tożsamości. Jeśli korzystasz z usługi Facebook, należy najpierw [dozwolonych domen Facebook] [ 1] w aplikacji.

    Zastąp **urlScheme** z unikatową nazwę aplikacji.  UrlScheme powinna być taka sama jak protokół schemat adresu URL, który określono w **dozwolone zewnętrznych adresów URL przekierowań** w portalu Azure. UrlScheme jest używany przez wywołanie zwrotne uwierzytelniania, aby powrócić do aplikacji po zakończeniu żądania uwierzytelnienia.

2. Zastąp `[self refresh]` w `viewDidLoad` w *QSTodoListViewController.m* następującym kodem:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Otwórz `QSAppDelegate.h` pliku i Dodaj następujący kod:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Otwórz `QSAppDelegate.m` pliku i Dodaj następujący kod:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Dodaj ten kod bezpośrednio przed przeczytaniem wiersza `#pragma mark - Core Data stack`.  Zastąp _appname_ o wartości urlScheme, który został użyty w kroku 1.

5. Otwórz `AppName-Info.plist` pliku (zastępując AppName z nazwą aplikacji) i Dodaj następujący kod:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Ten kod powinien być umieszczony wewnątrz `<dict>` elementu.  Zastąp _appname_ ciągu (w tablicy dla **CFBundleURLSchemes**) o nazwie aplikacji wybranymi w kroku 1.  Można wprowadzić te zmiany w plist Edytor — kliknięcie `AppName-Info.plist` w środowisku XCode, aby otworzyć Edytor plist.

    Zastąp `com.microsoft.azure.zumo` ciągu dla **CFBundleURLName** z Twojej firmy Apple identyfikator pakietu.

6. Naciśnij klawisz *Uruchom* Aby uruchomić aplikację, a następnie zaloguj. Gdy użytkownik jest zalogowany, należy umożliwić wyświetlanie listy zadań do wykonania i aktualizacje.

**Kod SWIFT**:

1. Na komputerze Mac Otwórz *ToDoTableViewController.swift* w środowisku Xcode i dodaj następującą metodę:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Zmień *google* do *microsoftaccount*, *twitter*, *facebook*, lub *windowsazureactivedirectory* Jeśli nie używasz Google jako dostawcy tożsamości. Jeśli korzystasz z usługi Facebook, należy najpierw [dozwolonych domen Facebook] [ 1] w aplikacji.

    Zastąp **urlScheme** z unikatową nazwę aplikacji.  UrlScheme powinna być taka sama jak protokół schemat adresu URL, który określono w **dozwolone zewnętrznych adresów URL przekierowań** w portalu Azure. UrlScheme jest używany przez wywołanie zwrotne uwierzytelniania, aby powrócić do aplikacji po zakończeniu żądania uwierzytelnienia.

2. Usuń wiersze `self.refreshControl?.beginRefreshing()` i `self.onRefresh(self.refreshControl)` na końcu `viewDidLoad()` w *ToDoTableViewController.swift*. Dodaj wywołanie do `loginAndGetData()` zamiast nich:

    ```swift
    loginAndGetData()
    ```

3. Otwórz `AppDelegate.swift` i Dodaj następujący wiersz do `AppDelegate` klasy:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Zastąp _appname_ o wartości urlScheme, który został użyty w kroku 1.

4. Otwórz `AppName-Info.plist` pliku (zastępując AppName z nazwą aplikacji) i Dodaj następujący kod:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Ten kod powinien być umieszczony wewnątrz `<dict>` elementu.  Zastąp _appname_ ciągu (w tablicy dla **CFBundleURLSchemes**) o nazwie aplikacji wybranymi w kroku 1.  Można wprowadzić te zmiany w plist Edytor — kliknięcie `AppName-Info.plist` w środowisku XCode, aby otworzyć Edytor plist.

    Zastąp `com.microsoft.azure.zumo` ciągu dla **CFBundleURLName** z Twojej firmy Apple identyfikator pakietu.

5. Naciśnij klawisz *Uruchom* Aby uruchomić aplikację, a następnie zaloguj. Gdy użytkownik jest zalogowany, należy umożliwić wyświetlanie listy zadań do wykonania i aktualizacje.

Usługi aplikacji — uwierzytelnianie przy użyciu jabłek komunikacji między aplikacjami.  Aby uzyskać więcej informacji na ten temat, zapoznaj się [dokumentacji firmy Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[portalu Azure]: https://portal.azure.com

[iOS szybki start]: app-service-mobile-ios-get-started.md

