---
title: Dodawanie uwierzytelniania w iOS
description: Dowiedz się, jak używać aplikacji mobilnych platformy Azure do uwierzytelniania użytkowników aplikacji na iOS za pośrednictwem dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fd7860053e8c04ca9d5e355a721afd834835a441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459027"
---
# <a name="add-authentication-to-your-ios-app"></a>Dodawanie uwierzytelniania do aplikacji na iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym samouczku można dodać uwierzytelnianie do projektu [szybkiego uruchamiania systemu iOS] przy użyciu obsługiwanego dostawcy tożsamości. Ten samouczek jest oparty na samouczku [szybki start systemu iOS,] który należy wykonać najpierw.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Zarejestruj aplikację do uwierzytelniania i skonfiguruj usługę App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonego przekierowania zewnętrznego

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji.  Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania.  W tym samouczku używamy _appname_ schematu adresu URL w całym.  Możesz jednak użyć dowolnego schematu adresu URL, który wybierzesz.  Powinien być unikalny dla twojej aplikacji mobilnej.  Aby włączyć przekierowanie po stronie serwera:

1. W [witrynie Azure portal]wybierz usługę app service.

2. Kliknij opcję menu **Uwierzytelnianie / Autoryzacja.**

3. Kliknij **pozycję Usługa Azure Active Directory** w sekcji Dostawcy **uwierzytelniania.**

4. Ustaw **tryb zarządzania** na **Zaawansowane**.

5. W **adresach URL dozwolonego przekierowania zewnętrznego**wprowadź . `appname://easyauth.callback`  _Nazwa aplikacji_ w tym ciągu jest schemat url dla aplikacji mobilnej.  Powinien być zgodny ze specyfikacją normalnego adresu URL dla protokołu (użyj tylko liter i cyfr i zacznij od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej za pomocą schematu adresu URL w kilku miejscach.

6. Kliknij przycisk **OK**.

7. Kliknij przycisk **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W xcode naciśnij przycisk **Uruchom,** aby uruchomić aplikację. Wyjątek jest wywoływany, ponieważ aplikacja próbuje uzyskać dostęp do wewnętrznej bazy danych jako nieuwierzyfowany użytkownik, ale tabela *TodoItem* wymaga teraz uwierzytelniania.

## <a name="add-authentication-to-app"></a><a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
**Cel C**:

1. Na komputerze Mac otwórz *QSTodoListViewController.m* w xcode i dodaj następującą metodę:

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

    Zmień *google* na *microsoftaccount*, *twitter*, *facebook*, lub *windowsazureactivedirectory* jeśli nie używasz Google jako dostawcy tożsamości. Jeśli korzystasz z Facebooka, musisz [wpisać domeny Facebooka na białą listę][1] w aplikacji.

    Zastąp **urlScheme** unikatową nazwą aplikacji.  UrlScheme powinien być taki sam jak protokół schematu adresów URL określony w polu **Dozwolone adresy URL przekierowania zewnętrznego** w witrynie Azure portal. UrlScheme jest używany przez wywołanie zwrotne uwierzytelniania, aby przełączyć się z powrotem do aplikacji po zakończeniu żądania uwierzytelniania.

2. Zastąp `[self refresh]` w `viewDidLoad` *QSTodoListViewController.m* następującym kodem:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Otwórz plik `QSAppDelegate.h` i dodaj następujący kod:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Otwórz plik `QSAppDelegate.m` i dodaj następujący kod:

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

   Dodaj ten kod bezpośrednio przed `#pragma mark - Core Data stack`odczytem wiersza .  Zastąp _appname_ wartością urlScheme, która została użyta w kroku 1.

5. Otwórz `AppName-Info.plist` plik (zastępując AppName nazwą aplikacji) i dodaj następujący kod:

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

    Ten kod powinien być `<dict>` umieszczony wewnątrz elementu.  Zastąp ciąg _appname_ (w tablicy dla **CFBundleURLSchemes)** nazwą aplikacji wybraną w kroku 1.  Możesz również wprowadzić te zmiany w edytorze `AppName-Info.plist` plist - kliknij na plik w XCode, aby otworzyć edytor plist.

    Zastąp `com.microsoft.azure.zumo` ciąg **cfbundleuRLName** identyfikatorem pakietu Apple.

6. Naciśnij *przycisk Uruchom,* aby uruchomić aplikację, a następnie zaloguj się. Po zalogowaniu się powinieneś być w stanie wyświetlić listę Todo i dokonać aktualizacji.

**Szybki**:

1. Na komputerze Mac otwórz *plik ToDoTableViewController.swift* w programie Xcode i dodaj następującą metodę:

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

    Zmień *google* na *microsoftaccount*, *twitter*, *facebook*, lub *windowsazureactivedirectory* jeśli nie używasz Google jako dostawcy tożsamości. Jeśli korzystasz z Facebooka, musisz [wpisać domeny Facebooka na białą listę][1] w aplikacji.

    Zastąp **urlScheme** unikatową nazwą aplikacji.  UrlScheme powinien być taki sam jak protokół schematu adresów URL określony w polu **Dozwolone adresy URL przekierowania zewnętrznego** w witrynie Azure portal. UrlScheme jest używany przez wywołanie zwrotne uwierzytelniania, aby przełączyć się z powrotem do aplikacji po zakończeniu żądania uwierzytelniania.

2. Usuń `self.refreshControl?.beginRefreshing()` wiersze `self.onRefresh(self.refreshControl)` i na `viewDidLoad()` końcu w *ToDoTableViewController.swift*. Dodaj połączenie `loginAndGetData()` w ich miejsce:

    ```swift
    loginAndGetData()
    ```

3. Otwórz `AppDelegate.swift` plik i dodaj następujący `AppDelegate` wiersz do klasy:

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

4. Otwórz `AppName-Info.plist` plik (zastępując AppName nazwą aplikacji) i dodaj następujący kod:

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

    Ten kod powinien być `<dict>` umieszczony wewnątrz elementu.  Zastąp ciąg _appname_ (w tablicy dla **CFBundleURLSchemes)** nazwą aplikacji wybraną w kroku 1.  Możesz również wprowadzić te zmiany w edytorze `AppName-Info.plist` plist - kliknij na plik w XCode, aby otworzyć edytor plist.

    Zastąp `com.microsoft.azure.zumo` ciąg **cfbundleuRLName** identyfikatorem pakietu Apple.

5. Naciśnij *przycisk Uruchom,* aby uruchomić aplikację, a następnie zaloguj się. Po zalogowaniu się powinieneś być w stanie wyświetlić listę Todo i dokonać aktualizacji.

Uwierzytelnianie usługi aplikacji używa komunikacji między aplikacjami Apples.  Więcej informacji na ten temat można znaleźć w [dokumentacji Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Portal Azure]: https://portal.azure.com

[Szybki start systemu iOS]: app-service-mobile-ios-get-started.md

