---
title: Dodawanie powiadomień wypychanych do aplikacji Apache Cordova za pomocą funkcji Mobile Apps w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Mobile Apps do wysyłania powiadomień wypychanych do aplikacji Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114361"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Dodawanie powiadomień wypychanych do aplikacji Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W ramach tego samouczka, możesz dodać powiadomienia wypychane do [Szybki Start Apache Cordova] [ 5] projektu, dzięki czemu jest wysyłane powiadomienie push do urządzenia, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz pobrany projekt szybkiego startu serwera, należy pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracować z zestawu SDK serwera zaplecza .NET dla aplikacji mobilnych][1].

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, iż aplikacji Apache Cordova, który został opracowany przy użyciu programu Visual Studio 2015. To urządzenie należy uruchomić na Emulator systemu Google Android, urządzenia z systemem Android, urządzenia Windows lub urządzenia z systemem iOS.

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Komputer z programem [Visual Studio Community 2015] [ 2] lub nowszej
* [Visual Studio Tools for Apache Cordova][4]
* [Aktywne konto platformy Azure][3]
* Ukończone [Szybki Start Apache Cordova] [ 5] projektu
* (Android) A [konto Google] [ 6] z ze zweryfikowanym adresem e-mail
* (iOS) [Członkostwa w programie dla deweloperów firmy Apple] [ 7] i urządzenia z systemem iOS (iOS Simulator nie obsługuje powiadomienia wypychane)
* (Windows) A [konta dewelopera Microsoft Store] [ 8] i urządzenia z systemem Windows 10

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Obejrzyj film wideo przedstawiający kroki w tej sekcji][9].

## <a name="update-the-server-project"></a>Aktualizacja projektu serwera

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modyfikowanie aplikacji Cordova

Aby upewnić się, że projekt aplikacji Apache Cordova jest gotowy do obsługi powiadomień wypychanych, należy zainstalować wtyczki Cordova wypychania, a także żadnych usług powiadomień wypychanych specyficzne dla platformy.

#### <a name="update-the-cordova-version-in-your-project"></a>Zaktualizuj wersję Cordova w projekcie.

Jeśli projekt używa wersji Apache Cordova, która jest starsza niż wersja 6.1.1, należy zaktualizować projekt klienta. Aby zaktualizować projekt, wykonaj następujące czynności:

* Aby otworzyć projektanta konfiguracji, kliknij prawym przyciskiem myszy `config.xml`.
* Wybierz **platform** kartę.
* W **Cordova CLI** pola tekstowego, wybierz opcję **6.1.1**. 
* Aby zaktualizować projekt, wybierz **kompilacji**, a następnie wybierz pozycję **Kompiluj rozwiązanie**.

#### <a name="install-the-push-plugin"></a>Instalowanie wtyczki wypychania

Aplikacji Apache Cordova nie obsługują natywnie możliwości urządzeń lub sieci.  Te możliwości są udostępniane przez wtyczek, które są publikowane, albo na [npm] [ 10] lub w witrynie GitHub. `phonegap-plugin-push` Wtyczka obsługuje powiadomienia wypychane w sieci.

Wtyczka wypychania można zainstalować w jednym z następujących sposobów:

**W wierszu polecenia:**

Uruchom następujące polecenie:

    cordova plugin add phonegap-plugin-push

**Z poziomu programu Visual Studio:**

1. W Eksploratorze rozwiązań Otwórz `config.xml` pliku. Następnie wybierz pozycję **wtyczek** > **niestandardowe**. Następnie wybierz pozycję **Git** jako źródła instalacji.

2. Wprowadź `https://github.com/phonegap/phonegap-plugin-push` jako źródło.

    ![Otwórz plik config.xml w Eksploratorze rozwiązań][img1]

3. Wybierz strzałkę obok źródła instalacji.

4. W **SENDER_ID**, jeśli masz już identyfikator liczbowych projektu dla projektu konsoli dewelopera Google, możesz dodać ją tutaj. W przeciwnym razie wprowadź wartość symbolu zastępczego, na przykład 777777. Jeśli są przeznaczone dla systemów Android, możesz zaktualizować tę wartość w pliku config.xml później.

    >[!NOTE]
    >Począwszy od wersji 2.0.0 google-services.json musi być zainstalowany w folderze głównym projektu, aby skonfigurować identyfikator nadawcy. Aby uzyskać więcej informacji, zobacz [dokumentacji instalacji.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Wybierz pozycję **Dodaj**.

Wtyczka wypychania jest zainstalowany.

#### <a name="install-the-device-plugin"></a>Instalowanie wtyczki urządzenia

Postępuj zgodnie z tą samą procedurą, używane do zainstalowania wtyczki wypychania. Dodaj wtyczkę urządzenie z listy wtyczek Core. (Aby ją znaleźć, wybierz **wtyczek** > **Core**.) Należy tej wtyczki, aby uzyskać nazwę platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Zarejestruj urządzenie podczas uruchamiania aplikacji 

Początkowo zawiera niektóre minimalne kodu dla systemu Android. Później możesz zmodyfikować aplikację do uruchamiania w systemie iOS lub Windows 10.

1. Dodaj wywołanie do **registerForPushNotifications** podczas wywołania zwrotnego dla procesu logowania. Alternatywnie, można dodać go w dolnej części **onDeviceReady** metody:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Ten przykład przedstawia wywoływanie **registerForPushNotifications** po pomyślnym uwierzytelnieniu. Możesz wywołać `registerForPushNotifications()` tak często, jak jest wymagana.

2. Dodaj nowy **registerForPushNotifications** metody w następujący sposób:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android) W poprzednim kodzie Zastąp `Your_Project_ID` liczbowym identyfikator projektu dla aplikacji na podstawie [konsoli dewelopera Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcjonalnie) Konfigurowanie i uruchamianie aplikacji w systemie Android

Wypełnij tę sekcję, aby włączyć powiadomienia wypychane dla systemu Android.

#### <a name="enable-gcm"></a>Włączanie usługi Firebase Cloud Messaging

Ponieważ początkowo są przeznaczone dla platformy systemu Android firmy Google, należy włączyć usługi Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurowanie zaplecza aplikacji mobilnej na potrzeby wysyłania żądań push przy użyciu usługi FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfiguruj aplikację Cordova dla systemu Android

Otwórz w aplikacji Cordova **pliku config.xml**. Następnie zastąp `Your_Project_ID` liczbowym identyfikator projektu dla aplikacji na podstawie [konsoli dewelopera Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Otwórz **index.js**. Następnie zaktualizuj kod, aby użyć swojego projektu liczbowych identyfikatora.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurowanie urządzenia z systemem Android do debugowania USB

Zanim będzie można wdrożyć aplikację na urządzeniu z systemem Android, musisz włączyć debugowanie USB. Wykonaj następujące czynności na telefonie z systemem Android:

1. Przejdź do **ustawienia** > **informacje o telefonie**. Następnie wybierz przycisk **numer kompilacji** aż tryb dewelopera jest włączona (około siedem razy).
2. Ponownie **ustawienia** > **opcje dewelopera**, Włącz **debugowanie USB**. Następnie nawiązać telefon z systemem Android programowanie komputera za pomocą kabla USB.

Możemy przetestowane to przy użyciu na Google Nexus 5 X urządzenie z systemem Android 6.0 (Marshmallow). Jednak te techniki są wspólne w dowolnej nowoczesnej wydania systemu Android.

#### <a name="install-google-play-services"></a>Zainstaluj usługi Google Play

Wtyczka wypychania zależy od systemu Android usług Google Play dla powiadomień wypychanych.

1. W programie Visual Studio, wybierz **narzędzia** > **Android** > **Menedżer zestawów SDK**. Następnie rozwiń **dodatki** folderu. Zaznacz odpowiednie pola, aby upewnić się, każdy z następujących SDK jest zainstalowany:

   * System android 2.3 lub nowszy
   * Google Repository w wersji 27 lub nowszej
   * Usługi Google Play Services 9.0.2 lub nowszej

2. Wybierz **zainstalować pakiety**. Następnie poczekaj na zakończenie instalacji.

Bieżący wymaganych bibliotek są wyświetlane w [dokumentacji instalacji phonegap wtyczka push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testowych powiadomień push w aplikacji w systemie Android

Możesz teraz testowych powiadomień wypychanych, uruchamiając aplikację, a następnie wstawianie elementów do tabeli TodoItem. Tak długo, jak w przypadku korzystania z tego samego zaplecza można przetestować z tego samego urządzenia lub drugiego urządzenia. Przetestuj aplikację Cordova na platformie systemu Android, w jednym z następujących sposobów:

* *Na urządzeniu fizycznym:* Dołączanie urządzenia z systemem Android na komputerze dewelopera za pomocą kabla USB.  Zamiast **Emulator systemu Google Android**, wybierz opcję **urządzenia**. Visual Studio służy do wdrażania aplikacji na urządzeniu i uruchamia aplikację. Następnie można interakcji z aplikacją na urządzeniu.

  Udostępnianie ekranu aplikacje, takie jak [Mobizen] [ 20] mogą pomóc w rozwoju aplikacji dla systemu Android. Mobizen projektów dla systemu Android ekranu do przeglądarki sieci web na komputerze.

* *W emulatorze systemu Android:* Istnieją dodatkowe czynności konfiguracyjne, które są wymagane, jeśli korzystasz z emulatora.

    Upewnij się, że są wdrażane do urządzenia wirtualnego, który ma ustawioną jako docelową, interfejsy API Google, jak pokazano w programie Android Virtual Device (AVD) manager.

    ![Menedżer urządzeń wirtualnych dla systemu android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Jeśli chcesz używać szybciej x86 emulatora, [zainstalować sterownik aparatu HAXM][11], a następnie skonfigurować emulator, aby go użyć.

    Dodaj konto Google na urządzeniu z systemem Android, wybierając **aplikacje** > **ustawienia** > **Dodaj konto**. Następnie postępuj zgodnie z monitami.

    ![Dodaj konto Google na urządzeniu z systemem Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Uruchom aplikację listy zadań jako przed i Wstaw nowy element todo. Tym razem ikonę powiadomień jest wyświetlany w obszarze powiadomień. Możesz otworzyć menu powiadomień, aby wyświetlić pełny tekst powiadomienia.

    ![Wyświetl powiadomienia](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcjonalnie) Konfigurowanie i uruchamianie w systemie iOS

Ta sekcja dotyczy uruchamiania projektu Cordova na urządzeniach z systemem iOS. Jeśli nie pracujesz z urządzeń z systemem iOS, możesz pominąć tę sekcję.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Zainstaluj i uruchom agenta kompilacji zdalnej dla systemu iOS w usłudze, Mac lub w chmurze

Przed uruchomieniem aplikacji Cordova w systemie iOS przy użyciu programu Visual Studio, wykonaj kroki opisane w [przewodnik instalacyjne systemu iOS] [ 12] do zainstalowania i uruchomienia agenta kompilacji zdalnej.

Upewnij się, że możesz utworzyć aplikację dla systemu iOS. Kroki opisane w przewodniku instalacji są wymagane do tworzenia aplikacji dla systemu iOS w programie Visual Studio. Jeśli nie masz komputera Mac, można tworzyć dla systemu iOS przy użyciu agenta kompilacji zdalnej usługi, takie jak MacInCloud. Aby uzyskać więcej informacji, zobacz [uruchamianie aplikacji systemu iOS w chmurze][21].

> [!NOTE]
> Środowisko Xcode 7 lub nowszy jest wymagany do Użyj wtyczki wypychane w systemie iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Znajdowanie Identyfikatora do użycia jako Identyfikatora aplikacji

Przed zarejestrowaniem aplikacji dla powiadomień wypychanych, otwartego pliku config.xml w swojej aplikacji Cordova, Znajdź `id` wartość w elemencie widget atrybutu, a następnie skopiuj go do późniejszego użycia. Następujący kod XML jest identyfikator `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Tego identyfikatora należy użyć później, podczas tworzenia identyfikator aplikacji w portalu dla deweloperów firmy Apple. Jeżeli tworzysz inny identyfikator aplikacji w portalu dla deweloperów, należy wykonać kilka dodatkowych kroków w dalszej części tego samouczka. Identyfikator w elemencie widget musi odpowiadać identyfikator aplikacji w portalu dla deweloperów.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Zarejestrować aplikację na potrzeby powiadomień wypychanych w portalu dla deweloperów firmy Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure, jak wysyłać powiadomienia wypychane

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Sprawdź, czy Identyfikatora aplikacji odpowiada aplikacji Cordova

Jeśli identyfikator aplikacji, który został utworzony w ramach Twojego konta dewelopera firmy Apple już zgodny z Identyfikatorem elementu widget w pliku config.xml, możesz pominąć ten krok. Jednak jeśli niezgodne identyfikatory, wykonaj następujące czynności:

1. Usuwanie folderu platforms z projektu.
2. Usuń folder dodatków z projektu.
3. Usuwanie folderu node_modules z projektu.
4. Aktualizowanie atrybutu id elementu widget w pliku config.xml, aby użyć Identyfikatora aplikacji, który został utworzony w ramach Twojego konta dewelopera firmy Apple.
5. Ponownie skompiluj projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testowych powiadomień push w aplikacji systemu iOS

1. W programie Visual Studio, upewnij się, że **iOS** jest wybrany jako cel wdrożenia. Następnie wybierz pozycję **urządzenia** do uruchamiania powiadomień wypychanych na urządzenia z systemem iOS połączone.

    Powiadomienia wypychane można uruchomić na urządzeniu z systemem iOS, który jest podłączony do komputera za pomocą programu iTunes. W narzędziu iOS Simulator nie obsługuje powiadomień wypychanych.

2. Wybierz **Uruchom** przycisk lub **F5** w programie Visual Studio, aby skompilować projekt i uruchomić aplikację w urządzeniu z systemem iOS. Następnie wybierz pozycję **OK** do akceptowania powiadomień wypychanych.

   > [!NOTE]
   > Aplikacja zażąda potwierdzenia dla powiadomień wypychanych przy pierwszym uruchomieniu.

3. W aplikacji wpisz zadania, a następnie wybierz znak plus **(+)** ikony.
4. Sprawdź, czy Odebrano powiadomienie. Następnie wybierz pozycję **OK** aby odrzucić powiadomienie.

## <a name="optional-configure-and-run-on-windows"></a>(Opcjonalnie) Konfigurowanie i systemem Windows

W tej sekcji opisano sposób uruchamiania projektu aplikacji Apache Cordova na urządzeniach z systemem Windows 10 (PhoneGap wtyczki push jest obsługiwana w systemie Windows 10). Jeśli nie pracujesz z urządzeniami Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Rejestrowanie aplikacji Windows dla usługi powiadomień wypychanych za pomocą usługi WNS

Aby użyć opcji Store w programie Visual Studio, wybierz Windows target z listy platformy rozwiązania, takie jak **Windows x64** lub **Windows x86**. (Należy unikać **Windows AnyCPU** dla powiadomień wypychanych.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Obejrzyj film przedstawiający podobne kroki][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie Centrum powiadomień w przypadku usługi WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfiguruj aplikację Cordova do obsługi powiadomień wypychanych Windows

Otwórz projektanta konfiguracji, kliknij prawym przyciskiem myszy **pliku config.xml**. Następnie wybierz pozycję **Projektant widoków**. Następnie wybierz pozycję **Windows** , a następnie wybierz pozycję **systemu Windows 10** w obszarze **wersji docelowej Windows**.

Aby zapewnić obsługę powiadomień wypychanych w swoich kompilacjach domyślne (debugowanie), należy otworzyć **build.json** pliku. Następnie skopiuj konfigurację "wersja" konfiguracji debugowania.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Po aktualizacji **build.json** plik powinien zawierać następujący kod:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Tworzenie aplikacji i sprawdź, czy żadne błędy. Aplikację kliencką teraz należy zarejestrować odbieranie powiadomień z zaplecza funkcji Mobile Apps. W tej sekcji należy powtórzyć dla każdego projektu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowych powiadomień push w aplikacji Windows

W programie Visual Studio, upewnij się, że platforma Windows jest wybrany jako cel wdrożenia, takich jak **Windows x64** lub **Windows x86**. Aby uruchomić aplikację na komputerze Windows 10, który jest hostem programu Visual Studio, wybierz opcję **komputera lokalnego**.

1. Wybierz **Uruchom** przycisk, aby skompilować projekt i uruchomić aplikację.

2. W aplikacji wpisz nazwę nowego czynność do wykonania, a następnie wybierz znak plus **(+)** ikonę, aby ją dodać.

Sprawdź, czy otrzyma powiadomienie po dodaniu elementu.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [usługi Notification Hubs] [ 17] Aby dowiedzieć się więcej na temat powiadomień wypychanych.
* Jeśli jeszcze tego nie zrobiono, należy kontynuować samouczek przez [Dodawanie uwierzytelniania] [ 14] do swojej aplikacji Apache Cordova.

Dowiedz się, jak korzystać z następujących zestawów SDK:

* [Zestaw Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Zestaw node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
