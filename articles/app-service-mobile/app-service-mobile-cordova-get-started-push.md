---
title: Dodawanie powiadomień wypychanych do aplikacji Apache Cordova
description: Dowiedz się, jak używać aplikacji mobilnych do wysyłania powiadomień push do aplikacji Apache Cordova.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461610"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Dodawanie powiadomień wypychanych do aplikacji Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do projektu [szybkiego startu Apache Cordova,][5] aby powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy zostanie wstawiony rekord.

Jeśli nie korzystasz z pobranego projektu serwera szybki start, potrzebujesz pakietu rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [Praca z sdk serwera zaplecza .NET dla aplikacji mobilnych][1].

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że masz aplikację Apache Cordova, która została opracowana za pomocą programu Visual Studio 2015. To urządzenie powinno działać na Google Android Emulator, urządzenie z systemem Android, urządzenie z systemem Windows lub urządzenie z systemem iOS.

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Komputer ze społecznością [Programu Visual Studio 2015][2] lub nowszym
* [Narzędzia programu Visual Studio dla Apache Cordova][4]
* [Aktywne konto platformy Azure][3]
* Ukończony projekt [Szybki start Apache Cordova][5]
* (Android) [Konto Google][6] ze zweryfikowanym adresem e-mail
* (iOS) [Członkostwo w programie deweloperskim Firmy Apple][7] i urządzenie z systemem iOS (symulator systemu iOS nie obsługuje powiadomień wypychanych)
* (Windows) [Konto dewelopera w Sklepie Microsoft][8] i urządzenie z systemem Windows 10

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Obejrzyj film przedstawiający kroki opisane w tej sekcji][9].

## <a name="update-the-server-project"></a>Aktualizowanie projektu serwera

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="modify-your-cordova-app"></a><a name="add-push-to-app"></a>Modyfikowanie aplikacji Cordova

Aby upewnić się, że projekt aplikacji Apache Cordova jest gotowy do obsługi powiadomień wypychanych, zainstaluj wtyczkę Push Cordova oraz wszelkie usługi push specyficzne dla platformy.

#### <a name="update-the-cordova-version-in-your-project"></a>Zaktualizuj wersję Cordova w projekcie.

Jeśli projekt używa wersji Apache Cordova, która jest wcześniejsza niż wersja 6.1.1, zaktualizuj projekt klienta. Aby zaktualizować projekt, należy wykonać następujące kroki:

* Aby otworzyć projektanta konfiguracji, `config.xml`kliknij prawym przyciskiem myszy .
* Wybierz kartę **Platformy.**
* W polu tekstowym **Korfova CLI** wybierz **pozycję 6.1.1**. 
* Aby zaktualizować projekt, wybierz polecenie **Kompilacja**, a następnie wybierz pozycję **Build Solution**.

#### <a name="install-the-push-plugin"></a>Zainstaluj wtyczkę push

Aplikacje Apache Cordova nie obsługują natywnie funkcji urządzeń ani sieci.  Te możliwości są dostarczane przez wtyczki, które są publikowane na [npm][10] lub na GitHub. Wtyczka `phonegap-plugin-push` obsługuje powiadomienia push sieci.

Wtyczkę push można zainstalować w jeden z następujących sposobów:

**Z wiersza polecenia:**

Uruchom następujące polecenie:

    cordova plugin add phonegap-plugin-push

**Z poziomu programu Visual Studio:**

1. W Eksploratorze `config.xml` rozwiązań otwórz plik. Następnie wybierz **opcję Wtyczki** > **niestandardowe**. Następnie wybierz **Git** jako źródło instalacji.

2. Wprowadź `https://github.com/phonegap/phonegap-plugin-push` jako źródło.

    ![Otwieranie pliku config.xml w Eksploratorze rozwiązań][img1]

3. Wybierz strzałkę obok źródła instalacji.

4. W **SENDER_ID**, jeśli masz już numeryczny identyfikator projektu dla projektu Google Developer Console, możesz dodać go tutaj. W przeciwnym razie wprowadź wartość symbolu zastępczego, taką jak 777777. Jeśli kierujesz reklamy na system Android, możesz później zaktualizować tę wartość w pliku config.xml.

    >[!NOTE]
    >W wersji 2.0.0, google-services.json musi być zainstalowany w folderze głównym projektu, aby skonfigurować identyfikator nadawcy. Aby uzyskać więcej informacji, zobacz [dokumentację instalacji.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Wybierz pozycję **Dodaj**.

Wtyczka push jest teraz zainstalowana.

#### <a name="install-the-device-plugin"></a>Zainstaluj wtyczkę urządzenia

Wykonaj tę samą procedurę, która została użyta do zainstalowania wtyczki push. Dodaj wtyczkę Urządzenia z listy wtyczek Core. (Aby go znaleźć, wybierz **Plugins** > **Core.)** Potrzebujesz tej wtyczki, aby uzyskać nazwę platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Zarejestruj urządzenie po uruchomieniu aplikacji 

Początkowo dołączamy minimalny kod dla systemu Android. Później możesz zmodyfikować aplikację, aby działała w systemie iOS lub Windows 10.

1. Dodaj wywołanie, aby **zarejestrowaćForPushNotifications** podczas wywołania zwrotnego dla procesu logowania. Alternatywnie można dodać go w dolnej części **onDeviceReady** metody:

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

    W tym przykładzie pokazano **wywołanie registerForPushNotifications** po pomyślnym uwierzytelnieniu. Można dzwonić `registerForPushNotifications()` tak często, jak jest to wymagane.

2. Dodaj nową metodę **registerForPushNotifications** w następujący sposób:

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
3. (Android) W poprzednim kodzie `Your_Project_ID` zastąp numerycznym identyfikatorem projektu aplikacji w [Konsoli programisty Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcjonalnie) Konfigurowanie i uruchamianie aplikacji w systemie Android

Wypełnij tę sekcję, aby włączyć powiadomienia wypychane dla systemu Android.

#### <a name="enable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>Włączanie usługi Firebase Cloud Messaging

Ponieważ początkowo kierujesz reklamy na platformę Google Android, musisz włączyć firebase cloud messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-the-mobile-app-back-end-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>Konfigurowanie zaplecza aplikacji mobilnej do wysyłania żądań wypychania przy użyciu usługi FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Skonfiguruj aplikację Cordova dla systemu Android

W aplikacji Cordova otwórz **plik config.xml**. Następnie `Your_Project_ID` zastąp numerycznym identyfikatorem projektu aplikacji w [Konsoli programisty Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Otwórz **plik index.js**. Następnie zaktualizuj kod, aby użyć identyfikatora projektu numerycznego.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>Konfigurowanie urządzenia z systemem Android do debugowania USB

Przed wdrożeniem aplikacji na urządzeniu z systemem Android należy włączyć debugowanie USB. Na telefonie z Androidem wykonać następujące czynności:

1. Przejdź do **ustawień** > **dotyczących telefonu**. Następnie dotknij **numeru kompilacji,** dopóki tryb dewelopera nie zostanie włączony (około siedem razy).
2. Powrót w **ustawieniach** > **opcji programisty**, włącz **debugowanie USB**. Następnie podłącz telefon z Systemem Android do komputera dewelopera za pomocą kabla USB.

Przetestowaliśmy to za pomocą urządzenia Google Nexus 5X z systemem Android 6.0 (Marshmallow). Jednak techniki są wspólne w każdej nowoczesnej wersji Androida.

#### <a name="install-google-play-services"></a>Instalowanie usług Google Play

Wtyczka push opiera się na usługach Google Play android do powiadomień push.

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **Android** > **Android SDK Manager**. Następnie rozwiń folder **Dodatki.** Zaznacz odpowiednie pola, aby upewnić się, że każdy z następujących SDK jest zainstalowany:

   * Android 2.3 lub wyższy
   * Google Repozytorium wersja 27 lub nowsza
   * Usługi Google Play 9.0.2 lub nowsze

2. Wybierz **pozycję Zainstaluj pakiety**. Następnie poczekaj na zakończenie instalacji.

Bieżące wymagane biblioteki są wymienione w [dokumentacji instalacji phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testowanie powiadomień push w aplikacji na Androida

Teraz można przetestować powiadomienia wypychane, uruchamiając aplikację i wstawiając elementy w tabeli TodoItem. Możesz przetestować z tego samego urządzenia lub z drugiego urządzenia, o ile używasz tego samego zaplecza. Przetestuj swoją aplikację Cordova na platformie Android w jeden z następujących sposobów:

* *Na urządzeniu fizycznym:* Podłącz urządzenie z Androidem do komputera dewelopera za pomocą kabla USB.  Zamiast **Google Android Emulator**, wybierz **urządzenie**. Visual Studio wdraża aplikację na urządzeniu i uruchamia aplikację. Następnie można wchodzić w interakcje z aplikacją na urządzeniu.

  Aplikacje do udostępniania ekranu, takie jak [Mobizen,][20] mogą pomóc w tworzeniu aplikacji na Androida. Mobizen wyświetla ekran Androida w przeglądarce internetowej na komputerze.

* *Na emulatorze systemu Android:* Istnieją dodatkowe kroki konfiguracji, które są wymagane podczas korzystania z emulatora.

    Upewnij się, że wdrażasz na urządzeniu wirtualnym, na które są ustawione interfejsy API Google jako obiekt docelowy, jak pokazano w menedżerze urządzenia wirtualnego systemu Android (AVD).

    ![Menedżer urządzeń wirtualnych systemu Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Jeśli chcesz użyć szybszego emulatora x86, [zainstaluj sterownik HAXM][11], a następnie skonfiguruj emulator, aby go używał.

    Dodaj konto Google do urządzenia z Androidem, wybierając**pozycję Ustawienia** >  **aplikacji** > **Dodaj konto**. Następnie postępuj zgodnie z instrukcjami.

    ![Dodawanie konta Google do urządzenia z Androidem](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Uruchom aplikację todolist jak poprzednio i wstaw nowy element do wykonania. Tym razem w obszarze powiadomień zostanie wyświetlona ikona powiadomienia. Możesz otworzyć szufladę powiadomień, aby wyświetlić pełną treść powiadomienia.

    ![Wyświetl powiadomienie](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcjonalnie) Konfigurowanie i uruchamianie w uos

Ta sekcja służy do uruchamiania projektu Cordova na urządzeniach z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalowanie i uruchamianie agenta kompilacji zdalnej systemu iOS na komputerze Mac lub usłudze w chmurze

Aby można było uruchomić aplikację Cordova w systemach iOS przy użyciu programu Visual Studio, przejdź przez kroki opisane w [przewodniku konfiguracji systemu iOS,][12] aby zainstalować i uruchomić agenta kompilacji zdalnej.

Upewnij się, że możesz utworzyć aplikację dla systemu iOS. Kroki opisane w przewodniku konfiguracyjnym są wymagane do tworzenia aplikacji dla systemu iOS z programu Visual Studio. Jeśli nie masz komputera Mac, możesz tworzyć dla systemu iOS przy użyciu agenta kompilacji zdalnej w usłudze takiej jak MacInCloud. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji na iOS w chmurze][21].

> [!NOTE]
> Xcode 7 lub wyższy jest wymagany do korzystania z wtyczki push w iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Znajdowanie identyfikatora, który ma być używany jako identyfikator aplikacji

Przed zarejestrowaniem aplikacji dla powiadomień wypychanych otwórz plik config.xml w aplikacji Cordova, znajdź wartość `id` atrybutu w elemencie widżetu, a następnie skopiuj ją do późniejszego użycia. W poniższym pliku XML `io.cordova.myapp7777777`identyfikator jest .

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Później użyj tego identyfikatora podczas tworzenia identyfikatora aplikacji w portalu deweloperskim firmy Apple. Jeśli utworzysz inny identyfikator aplikacji w portalu dla deweloperów, musisz wykonać kilka dodatkowych kroków w dalszej części tego samouczka. Identyfikator w elemencie widżetu musi być zgodny z identyfikatorem aplikacji w portalu dewelopera.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Zarejestruj aplikację do otrzymywani powiadomień push w portalu deweloperskim firmy Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Sprawdź, czy identyfikator aplikacji jest zgodny z twoją aplikacją Cordova

Jeśli identyfikator aplikacji utworzony na koncie dewelopera Apple jest już zgodny z identyfikatorem elementu widżetu w pliku config.xml, możesz pominąć ten krok. Jeśli jednak identyfikatory nie są zgodne, należy wykonać następujące czynności:

1. Usuń folder platformy z projektu.
2. Usuń folder wtyczek z projektu.
3. Usuń folder node_modules z projektu.
4. Zaktualizuj atrybut id elementu widżetu w pliku config.xml, aby użyć identyfikatora aplikacji utworzonego na koncie dewelopera firmy Apple.
5. Odbuduj swój projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testowanie powiadomień wypychanych w aplikacji na iOS

1. W programie Visual Studio upewnij się, że **iOS** jest wybrany jako miejsce docelowe wdrożenia. Następnie wybierz **pozycję Urządzenie,** aby uruchomić powiadomienia wypychane na podłączonym urządzeniu z systemem iOS.

    Powiadomienia wypychane można uruchamiać na urządzeniu z systemem iOS podłączonym do komputera za pomocą programu iTunes. Symulator systemu iOS nie obsługuje powiadomień wypychanych.

2. Wybierz przycisk **Uruchom** lub **F5** w programie Visual Studio, aby utworzyć projekt i uruchomić aplikację na urządzeniu z systemem iOS. Następnie wybierz **przycisk OK,** aby akceptować powiadomienia wypychane.

   > [!NOTE]
   > Aplikacja żąda potwierdzenia powiadomień wypychanych podczas pierwszego uruchomienia.

3. W aplikacji wpisz zadanie, a następnie wybierz ikonę plus **(+).**
4. Sprawdź, czy odebrano powiadomienie. Następnie wybierz **przycisk OK,** aby odrzucić powiadomienie.

## <a name="optional-configure-and-run-on-windows"></a>(Opcjonalnie) Konfigurowanie i uruchamianie w systemie Windows

W tej sekcji opisano sposób uruchamiania projektu aplikacji Apache Cordova na urządzeniach z systemem Windows 10 (wtyczka wypychania PhoneGap jest obsługiwana w systemie Windows 10). Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Rejestrowanie aplikacji systemu Windows w celu otrzymywanania powiadomień wypychanych w usłudze WNS

Aby użyć opcji Sklepu w programie Visual Studio, wybierz obiekt docelowy systemu Windows z listy Platformy rozwiązań, na przykład **Windows-x64** lub **Windows-x86**. (Unikaj **windows-anycpu** dla powiadomień wypychanych.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Obejrzyj wideo przedstawiające podobne kroki][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie centrum powiadomień dla usługi WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurowanie aplikacji Cordova do obsługi powiadomień wypychanych systemu Windows

Otwórz projektanta konfiguracji, klikając prawym przyciskiem myszy **plik config.xml**. Następnie wybierz polecenie **Widok projektanta**. Następnie wybierz kartę **Windows,** a następnie wybierz pozycję **Windows 10** w obszarze **Wersja docelowa systemu Windows**.

Aby obsługiwać powiadomienia wypychań w kompilacjach domyślnych (debugowania), otwórz plik **build.json.** Następnie skopiuj konfigurację "release" do konfiguracji debugowania.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Po aktualizacji plik **build.json** powinien zawierać następujący kod:

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

Skompiluj aplikację i sprawdź, czy nie ma żadnych błędów. Aplikacja kliencka powinna teraz zarejestrować się w celu otrzymywannia powiadomień z zaplecza aplikacji mobilnych. Powtórz tę sekcję dla każdego projektu systemu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu Windows

W programie Visual Studio upewnij się, że jako miejsce docelowe wdrożenia wybrano platformę systemu Windows, taką jak **Windows-x64** lub **Windows-x86**. Aby uruchomić aplikację na komputerze z systemem Windows 10, na który hostuje program Visual Studio, wybierz pozycję **Komputer lokalny**.

1. Wybierz przycisk **Uruchom,** aby utworzyć projekt i uruchomić aplikację.

2. W aplikacji wpisz nazwę nowego todoitem, a następnie wybierz ikonę plus **(+),** aby ją dodać.

Sprawdź, czy powiadomienie jest odbierane po dodaniu elementu.

## <a name="next-steps"></a><a name="next-steps"></a>Następne kroki

* Przeczytaj o [Centrach powiadomień,][17] aby dowiedzieć się więcej o powiadomieniach wypychanych.
* Jeśli jeszcze tego nie zrobiłeś, kontynuuj samouczek, [dodając uwierzytelnianie][14] do aplikacji Apache Cordova.

Dowiedz się, jak używać następujących sdków:

* [Zestaw Apache Cordova SDK][15]
* [Zestaw ASP.NET Server SDK][1]
* [Zestaw Node.js Server SDK][16]

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
