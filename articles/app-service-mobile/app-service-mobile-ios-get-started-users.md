---
title: Dodawanie uwierzytelniania w systemie iOS przy użyciu usługi Azure Mobile Apps
description: Dowiedz się, jak za pomocą usługi Azure Mobile Apps uwierzytelniać użytkowników aplikacji systemu iOS za pomocą różnych dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 674d5f783f43011ba154b668cea4ec41f6a945f5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025284"
---
# <a name="add-authentication-to-your-ios-app"></a>Dodawanie uwierzytelniania do aplikacji systemu iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

W tym samouczku dodasz uwierzytelnianie do projektu [iOS — Szybki Start] przy użyciu obsługiwanego dostawcy tożsamości. Ten samouczek jest oparty na samouczku [iOS — Szybki Start] , który należy wykonać w pierwszej kolejności.

## <a name="register"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych zewnętrznych adresów URL przekierowania

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji.  Dzięki temu system uwierzytelniania może przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania.  W tym samouczku używamy _w systemie adresu_ URL.  Można jednak użyć dowolnego wybranego schematu adresów URL.  Powinna być unikatowa dla aplikacji mobilnej.  Aby włączyć przekierowanie po stronie serwera:

1. W [Azure Portal]wybierz App Service.

2. Kliknij opcję menu **uwierzytelnianie/autoryzacja** .

3. Kliknij **Azure Active Directory** w sekcji **dostawcy uwierzytelniania** .

4. Ustaw **tryb zarządzania** na **Zaawansowane**.

5. W polu **dozwolone adresy URL zewnętrznych przekierowań**wprowadź wartość `appname://easyauth.callback`.  Nazwa _użytkownika w tym_ ciągu to schemat adresu URL aplikacji mobilnej.  Powinna być zgodna ze specyfikacją normalnych adresów URL dla protokołu (używaj tylko liter i cyfr i zaczynać się od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej przy użyciu schematu adresu URL w kilku miejscach.

6. Kliknij przycisk **OK**.

7. Kliknij przycisk **Save** (Zapisz).

## <a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Xcode naciśnij pozycję **Uruchom** , aby uruchomić aplikację. Wyjątek jest zgłaszany, ponieważ aplikacja próbuje uzyskać dostęp do zaplecza jako nieuwierzytelniony użytkownik, ale tabela *TodoItem* wymaga teraz uwierzytelniania.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
**Cel-C**:

1. Na komputerze Mac Otwórz *QSTodoListViewController. m* w Xcode i Dodaj następującą metodę:

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

    Zmień firmę *Google* na *MicrosoftAccount*, *Twitter*, *Facebook*lub *windowsazureactivedirectory* , jeśli nie używasz usługi Google jako dostawcy tożsamości. W przypadku korzystania z serwisu Facebook należy [dozwolonych domeny usługi Facebook][1] w swojej aplikacji.

    Zastąp **urlScheme** unikatową nazwą aplikacji.  UrlScheme powinna być taka sama jak protokół schematu URL określony w polu **dozwolone zewnętrzne adresy URL przekierowań** w Azure Portal. UrlScheme jest używany przez wywołanie zwrotne uwierzytelniania do przełączenia z powrotem do aplikacji po zakończeniu żądania uwierzytelnienia.

2. Zastąp `[self refresh]` w `viewDidLoad` w *QSTodoListViewController. m* następującym kodem:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Otwórz plik `QSAppDelegate.h` i Dodaj następujący kod:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Otwórz plik `QSAppDelegate.m` i Dodaj następujący kod:

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

   Dodaj ten kod bezpośrednio przed odczytaniem wiersza `#pragma mark - Core Data stack`.  Zastąp wartość _nazwa_aplikacji_ wartością urlScheme użytą w kroku 1.

5. Otwórz plik `AppName-Info.plist` (zastępowanie nazwy aplikacji) i Dodaj następujący kod:

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

    Ten kod powinien być umieszczony wewnątrz elementu `<dict>`.  Zastąp ciąg _nazwa_aplikacji_ (w tablicy for **CFBundleURLSchemes**) nazwą aplikacji wybraną w kroku 1.  Możesz również wprowadzić te zmiany w edytorze plist — kliknij plik `AppName-Info.plist` w XCode, aby otworzyć Edytor plist.

    Zastąp ciąg `com.microsoft.azure.zumo` **CFBundleURLName** identyfikatorem pakietu Apple.

6. Naciśnij przycisk *Uruchom* , aby uruchomić aplikację, a następnie zaloguj się. Gdy użytkownik jest zalogowany, powinien być w stanie wyświetlić listę zadań do wykonania i wprowadzić aktualizacje.

**Kod SWIFT**:

1. Na komputerze Mac Otwórz *ToDoTableViewController. Swift* w Xcode i Dodaj następującą metodę:

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

    Zmień firmę *Google* na *MicrosoftAccount*, *Twitter*, *Facebook*lub *windowsazureactivedirectory* , jeśli nie używasz usługi Google jako dostawcy tożsamości. W przypadku korzystania z serwisu Facebook należy [dozwolonych domeny usługi Facebook][1] w swojej aplikacji.

    Zastąp **urlScheme** unikatową nazwą aplikacji.  UrlScheme powinna być taka sama jak protokół schematu URL określony w polu **dozwolone zewnętrzne adresy URL przekierowań** w Azure Portal. UrlScheme jest używany przez wywołanie zwrotne uwierzytelniania do przełączenia z powrotem do aplikacji po zakończeniu żądania uwierzytelnienia.

2. Usuń wiersze `self.refreshControl?.beginRefreshing()` i `self.onRefresh(self.refreshControl)` na końcu `viewDidLoad()` w *ToDoTableViewController. Swift*. Dodaj wywołanie do `loginAndGetData()` w ich miejscu:

    ```swift
    loginAndGetData()
    ```

3. Otwórz plik `AppDelegate.swift` i Dodaj następujący wiersz do klasy `AppDelegate`:

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

    Zastąp wartość _nazwa_aplikacji_ wartością urlScheme użytą w kroku 1.

4. Otwórz plik `AppName-Info.plist` (zastępowanie nazwy aplikacji) i Dodaj następujący kod:

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

    Ten kod powinien być umieszczony wewnątrz elementu `<dict>`.  Zastąp ciąg _nazwa_aplikacji_ (w tablicy for **CFBundleURLSchemes**) nazwą aplikacji wybraną w kroku 1.  Możesz również wprowadzić te zmiany w edytorze plist — kliknij plik `AppName-Info.plist` w XCode, aby otworzyć Edytor plist.

    Zastąp ciąg `com.microsoft.azure.zumo` **CFBundleURLName** identyfikatorem pakietu Apple.

5. Naciśnij przycisk *Uruchom* , aby uruchomić aplikację, a następnie zaloguj się. Gdy użytkownik jest zalogowany, powinien być w stanie wyświetlić listę zadań do wykonania i wprowadzić aktualizacje.

Uwierzytelnianie App Service używa komunikacji między aplikacjami.  Aby uzyskać więcej informacji na temat tego tematu, zapoznaj się z [dokumentacją firmy Apple][2] .
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[iOS — Szybki Start]: app-service-mobile-ios-get-started.md

