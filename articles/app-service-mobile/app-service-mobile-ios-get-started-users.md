---
title: Dodawanie uwierzytelniania w systemie iOS z usługą Azure Mobile Apps
description: Dowiedz się, jak używać usługi Azure Mobile Apps, uwierzytelniać użytkowników aplikacji systemu iOS za pomocą różnych dostawców tożsamości, obejmującej usługi AAD, Google, Facebook, Twitter i Microsoft.
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
ms.openlocfilehash: 8c1c52790065015977add7e32a06063057b24dad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128153"
---
# <a name="add-authentication-to-your-ios-app"></a>Dodawanie uwierzytelniania do aplikacji systemu iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym samouczku dodasz uwierzytelniania [Przewodnik Szybki start dla systemu iOS] projektu za pomocą dostawcy tożsamości obsługiwanych. Ten samouczek opiera się na [Przewodnik Szybki start dla systemu iOS] — samouczek, należy najpierw wykonać.

## <a name="register"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usługi App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonych zewnętrznego przekierowania

Bezpieczne uwierzytelnianie wymaga, zdefiniuj nowy schemat adresu URL dla aplikacji.  Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania.  W tym samouczku używamy schemat adresu URL _appname_ w całym.  Można jednak użyć dowolnego wybranego schematu URL.  Powinien on być unikatowy dla twojej aplikacji mobilnej.  Aby włączyć przekierowywanie po stronie serwera th:

1. W [Azure Portal], wybierz swoją usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. Kliknij przycisk **usługi Azure Active Directory** w obszarze **dostawców uwierzytelniania** sekcji.

4. Ustaw **tryb zarządzania** do **zaawansowane**.

5. W **dozwolone zewnętrzne adresy URL przekierowania**, wprowadź `appname://easyauth.callback`.  _Appname_ w tym ciągu jest schemat adresu URL aplikacji mobilnej.  Powinien on być zgodny z normalnej Specyfikacja adresu URL dla protokołu (litery użytkowania i tylko cyfry oraz rozpoczynać się literą).  Należy zapamiętać, ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnych za pomocą schemat adresu URL w kilku miejscach.

6. Kliknij przycisk **OK**.

7. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Xcode, naciśnij klawisz **Uruchom** Aby uruchomić aplikację. Zgłaszany jest wyjątek, ponieważ aplikacja próbuje uzyskać dostęp do wewnętrznej bazy danych jako użytkownik nieuwierzytelniony, ale *TodoItem* tabeli teraz wymaga uwierzytelnienia.

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

    Zmiana *google* do *microsoftaccount*, *twitter*, *facebook*, lub *windowsazureactivedirectory* Jeśli nie używasz Google jako dostawcy tożsamości. Jeśli korzystasz z usługi Facebook, musisz najpierw [domen Facebook dozwolonych] [ 1] w swojej aplikacji.

    Zastąp **urlScheme** unikatową nazwą aplikacji.  UrlScheme powinna być taka sama jak protokół schemat adresu URL, który określiłeś w **dozwolone zewnętrzne adresy URL przekierowania** pola w witrynie Azure portal. UrlScheme umożliwia przez wywołanie zwrotne uwierzytelniania przejdź z powrotem do aplikacji po zakończeniu żądania uwierzytelnienia.

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

   Dodaj następujący kod bezpośrednio przed przeczytaniem wiersza `#pragma mark - Core Data stack`.  Zastąp _appname_ wartością urlScheme, która została użyta w kroku 1.

5. Otwórz `AppName-Info.plist` pliku (zastępowanie AppName nazwą aplikacji) i Dodaj następujący kod:

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

    Ten kod będzie umieszczona wewnątrz `<dict>` elementu.  Zastąp _appname_ ciągu (w ramach macierzy **CFBundleURLSchemes**) nazwą aplikacji, które wybrano w kroku 1.  Można również wprowadzenie tych zmian w plist Edytor — kliknij pozycję `AppName-Info.plist` pliku w programie XCode, aby otworzyć Edytor plist.

    Zastąp `com.microsoft.azure.zumo` ciągu dla **CFBundleURLName** za pomocą usługi Apple identyfikator pakietu.

6. Naciśnij klawisz *Uruchom* Aby uruchomić aplikację, a następnie zaloguj się. Po użytkownik jest zalogowany, można wyświetlić listę zadań do wykonania i aktualizacje.

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

    Zmiana *google* do *microsoftaccount*, *twitter*, *facebook*, lub *windowsazureactivedirectory* Jeśli nie używasz Google jako dostawcy tożsamości. Jeśli korzystasz z usługi Facebook, musisz najpierw [domen Facebook dozwolonych] [ 1] w swojej aplikacji.

    Zastąp **urlScheme** unikatową nazwą aplikacji.  UrlScheme powinna być taka sama jak protokół schemat adresu URL, który określiłeś w **dozwolone zewnętrzne adresy URL przekierowania** pola w witrynie Azure portal. UrlScheme umożliwia przez wywołanie zwrotne uwierzytelniania przejdź z powrotem do aplikacji po zakończeniu żądania uwierzytelnienia.

2. Usuń wiersze `self.refreshControl?.beginRefreshing()` i `self.onRefresh(self.refreshControl)` na końcu `viewDidLoad()` w *ToDoTableViewController.swift*. Dodaj wywołanie do `loginAndGetData()` zamiast nich:

    ```swift
    loginAndGetData()
    ```

3. Otwórz `AppDelegate.swift` pliku i Dodaj następujący wiersz do `AppDelegate` klasy:

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

    Zastąp _appname_ wartością urlScheme, która została użyta w kroku 1.

4. Otwórz `AppName-Info.plist` pliku (zastępowanie AppName nazwą aplikacji) i Dodaj następujący kod:

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

    Ten kod będzie umieszczona wewnątrz `<dict>` elementu.  Zastąp _appname_ ciągu (w ramach macierzy **CFBundleURLSchemes**) nazwą aplikacji, które wybrano w kroku 1.  Można również wprowadzenie tych zmian w plist Edytor — kliknij pozycję `AppName-Info.plist` pliku w programie XCode, aby otworzyć Edytor plist.

    Zastąp `com.microsoft.azure.zumo` ciągu dla **CFBundleURLName** za pomocą usługi Apple identyfikator pakietu.

5. Naciśnij klawisz *Uruchom* Aby uruchomić aplikację, a następnie zaloguj się. Po użytkownik jest zalogowany, można wyświetlić listę zadań do wykonania i aktualizacje.

Uwierzytelnianie usługi App Service przy użyciu komunikacji Inter-App jabłka.  Aby uzyskać więcej informacji na ten temat, zapoznaj się [dokumentacji firmy Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[Przewodnik Szybki start dla systemu iOS]: app-service-mobile-ios-get-started.md

