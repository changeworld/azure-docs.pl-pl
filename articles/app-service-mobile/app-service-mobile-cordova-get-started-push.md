---
title: Dodawanie powiadomień wypychanych do aplikacji Apache Cordova
description: Dowiedz się, jak za pomocą Mobile Apps wysyłać powiadomienia wypychane do aplikacji Apache Cordova.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461610"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Dodawanie powiadomień wypychanych do aplikacji Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dowiesz się, jak dodać powiadomienia wypychane do projektu [Apache Cordova szybkiego startu][5] , aby Powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy rekord zostanie wstawiony.

Jeśli nie korzystasz z pobranego projektu szybkiego startu, będzie potrzebny pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz artykuł [współpraca z zestawem SDK serwera zaplecza .NET dla Mobile Apps][1].

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że masz aplikację Apache Cordova, która została opracowana za pomocą programu Visual Studio 2015. To urządzenie powinno być uruchamiane w usłudze Google Emulator systemu Android, urządzeniu z systemem Android, urządzeniu z systemem Windows lub urządzeniu z systemem iOS.

Do ukończenia tego samouczka niezbędne są następujące elementy:

* KOMPUTER z programem [Visual Studio Community 2015][2] lub nowszym
* [Visual Studio Tools for Apache Cordova][4]
* [Aktywne konto platformy Azure][3]
* Zakończono projekt [szybkiego startu Apache Cordova][5]
* Systemów [Konto Google][6] z zweryfikowanym adresem e-mail
* Wykonane [Członkostwo w programie Apple Developer][7] i urządzenie z systemem iOS (symulator systemu iOS nie obsługuje powiadomień wypychanych)
* Systemy [Konto dewelopera Microsoft Store][8] i urządzenie z systemem Windows 10

## <a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Obejrzyj film wideo pokazujący kroki opisane w tej sekcji][9].

## <a name="update-the-server-project"></a>Aktualizowanie projektu serwera

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modyfikowanie aplikacji platformy Cordova

Aby upewnić się, że projekt aplikacji Apache Cordova jest gotowy do obsługi powiadomień wypychanych, zainstaluj wtyczkę wypychaną oprogramowania Cordova oraz wszystkie usługi wypychania dla konkretnych platform.

#### <a name="update-the-cordova-version-in-your-project"></a>Zaktualizuj wersję oprogramowania Cordova w projekcie.

Jeśli projekt używa wersji Apache Cordova starszej niż wersja 6.1.1, zaktualizuj projekt klienta. Aby zaktualizować projekt, wykonaj następujące czynności:

* Aby otworzyć projektanta konfiguracji, kliknij prawym przyciskiem myszy `config.xml`.
* Wybierz kartę **platformy** .
* W polu tekstowym **Cordova CLI** wybierz pozycję **6.1.1**. 
* Aby zaktualizować projekt, wybierz pozycję **kompilacja**, a następnie wybierz pozycję **Kompiluj rozwiązanie**.

#### <a name="install-the-push-plugin"></a>Instalowanie wtyczki wypychanej

Aplikacje Apache Cordova nie obsługują natywnie możliwości urządzeń ani sieci.  Te funkcje są udostępniane przez wtyczki opublikowane w witrynie [npm][10] lub w witrynie GitHub. Wtyczka `phonegap-plugin-push` obsługuje powiadomienia wypychane w sieci.

Możesz zainstalować wtyczkę wypychaną w jeden z następujących sposobów:

**W wierszu polecenia:**

Uruchom następujące polecenie:

    cordova plugin add phonegap-plugin-push

**Z poziomu programu Visual Studio:**

1. W Eksplorator rozwiązań otwórz plik `config.xml`. Następnie wybierz pozycję **wtyczki** > **niestandardowe**. Następnie wybierz pozycję **git** jako źródło instalacji.

2. Wprowadź `https://github.com/phonegap/phonegap-plugin-push` jako źródło.

    ![Otwórz plik config. XML w Eksplorator rozwiązań][img1]

3. Wybierz strzałkę obok źródła instalacji.

4. W **SENDER_ID**, jeśli masz już liczbowy identyfikator projektu dla projektu konsoli dla deweloperów Google Developer, możesz dodać go tutaj. W przeciwnym razie wprowadź wartość symbolu zastępczego, na przykład 777777. Jeśli chcesz uzyskać system Android, możesz później zaktualizować tę wartość w pliku config. XML.

    >[!NOTE]
    >W przypadku wersji 2.0.0 należy zainstalować plik Google-Services. JSON w folderze głównym projektu w celu skonfigurowania identyfikatora nadawcy. Aby uzyskać więcej informacji, zobacz [dokumentację instalacji.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Wybierz pozycję **Dodaj**.

Wtyczka wypychana jest teraz zainstalowana.

#### <a name="install-the-device-plugin"></a>Instalowanie wtyczki urządzenia

Postępuj zgodnie z tą samą procedurą, która została użyta do zainstalowania wtyczki wypychania. Dodaj wtyczkę urządzenia z listy Core plugins. (Aby go znaleźć, wybierz pozycję **wtyczki** > **rdzeń**). Ta wtyczka jest potrzebna do uzyskania nazwy platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Rejestrowanie urządzenia podczas uruchamiania aplikacji 

Początkowo zawieramy minimalny kod dla systemu Android. Później możesz zmodyfikować aplikację tak, aby była uruchamiana w systemie iOS lub Windows 10.

1. Dodaj wywołanie do **registerForPushNotifications** podczas wywołania zwrotnego dla procesu logowania. Alternatywnie możesz dodać go u dołu metody **onDeviceReady** :

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

    Ten przykład pokazuje wywoływanie **registerForPushNotifications** po pomyślnym uwierzytelnieniu. Możesz wywoływać `registerForPushNotifications()` tak często, jak jest to wymagane.

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
3. Systemów W powyższym kodzie Zastąp `Your_Project_ID` IDENTYFIKATORem projektu numerycznego aplikacji z poziomu [konsoli dewelopera Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Obowiązkowe Konfigurowanie i uruchamianie aplikacji w systemie Android

Wykonaj tę sekcję, aby włączyć powiadomienia wypychane dla systemu Android.

#### <a name="enable-gcm"></a>Włącz obsługę komunikatów w chmurze Firebase

Ponieważ jako pierwszy chcesz korzystać z platformy Google Android, musisz włączyć obsługę komunikatów w chmurze Firebase.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurowanie zaplecza aplikacji mobilnej w celu wysyłania żądań push przy użyciu FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurowanie aplikacji oprogramowania Cordova dla systemu Android

W aplikacji platformy Cordova Otwórz **plik config. XML**. Następnie zastąp `Your_Project_ID` IDENTYFIKATORem projektu numerycznego aplikacji z poziomu [konsoli dewelopera Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Otwórz **index. js**. Następnie zaktualizuj kod, aby użyć identyfikatora projektu numerycznego.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurowanie urządzenia z systemem Android na potrzeby debugowania USB

Aby można było wdrożyć aplikację na urządzeniu z systemem Android, należy włączyć debugowanie USB. Na telefonie z systemem Android wykonaj następujące czynności:

1. Przejdź do pozycji **ustawienia** > **Informacje o telefonie**. Następnie naciśnij **numer kompilacji** , dopóki tryb dewelopera zostanie włączony (około siedem razy).
2. Wróć do **ustawień** > **Opcje dewelopera**, Włącz **debugowanie USB**. Następnie podłącz telefon z systemem Android do komputera deweloperskiego przy użyciu kabla USB.

Przetestowano to przy użyciu urządzenia Google Nexus pięciokrotną z systemem Android 6,0 (Marshmallow). Jednak te techniki są wspólne dla każdej nowoczesnej wersji systemu Android.

#### <a name="install-google-play-services"></a>Zainstaluj Usługi Google Play

Wtyczka wypychana korzysta z Usługi Google Play systemu Android na potrzeby powiadomień wypychanych.

1. W programie Visual Studio wybierz kolejno pozycje **narzędzia** > **Android** > **Android SDK Manager**. Następnie rozwiń folder **Dodatki** . Zaznacz odpowiednie pola, aby upewnić się, że zainstalowano każdy z następujących zestawów SDK:

   * System Android 2,3 lub nowszy
   * Wersja 27 repozytorium Google lub nowsza
   * Usługi Google Play 9.0.2 lub wyższy

2. Wybierz pozycję **Zainstaluj pakiety**. Następnie poczekaj na zakończenie instalacji.

Bieżące wymagane biblioteki są wymienione w [dokumentacji instalacji programu PhoneGap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testowanie powiadomień wypychanych w aplikacji w systemie Android

Teraz można testować powiadomienia wypychane, uruchamiając aplikację i wstawiając elementy w tabeli TodoItem. Możesz testować z tego samego urządzenia lub z drugiego urządzenia, o ile używasz tego samego zaplecza. Przetestuj aplikację Cordova na platformie Android w jeden z następujących sposobów:

* *Na urządzeniu fizycznym:* Dołącz urządzenie z systemem Android do komputera deweloperskiego przy użyciu kabla USB.  Zamiast usługi **Google emulator systemu Android**wybierz pozycję **urządzenie**. Program Visual Studio wdraża aplikację na urządzeniu i uruchamia aplikację. Następnie można korzystać z aplikacji na urządzeniu.

  Aplikacje do udostępniania ekranu, takie jak [Mobizen][20] , mogą pomóc w tworzeniu aplikacji dla systemu Android. Mobizen projekty ekranu systemu Android do przeglądarki sieci Web na komputerze.

* *Na emulatorze systemu Android:* Istnieją dodatkowe czynności konfiguracyjne, które są wymagane w przypadku korzystania z emulatora.

    Upewnij się, że wdrażasz program na urządzeniu wirtualnym z interfejsem API firmy Google ustawionym jako element docelowy, jak pokazano w Menedżerze urządzeń wirtualnych z systemem Android (AVD).

    ![Menedżer urządzeń wirtualne systemu Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Jeśli chcesz użyć szybszego emulatora x86, [Zainstaluj sterownik HAXM][11], a następnie skonfiguruj emulator, aby go używać.

    Dodaj konto Google do urządzenia z systemem Android, wybierając pozycję **aplikacje** > **Ustawienia** > **Dodaj konto**. Następnie postępuj zgodnie z monitami.

    ![Dodawanie konta Google do urządzenia z systemem Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Uruchom aplikację todolist jako wcześniejszą i Wstaw nowy element do wykonania. Tym razem w obszarze powiadomień zostanie wyświetlona ikona powiadomienia. Możesz otworzyć szufladę powiadomień, aby wyświetlić pełny tekst powiadomienia.

    ![Wyświetl powiadomienie](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Obowiązkowe Konfigurowanie i uruchamianie w systemie iOS

Ta sekcja służy do uruchamiania projektu Cordova na urządzeniach z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalowanie i uruchamianie zdalnego agenta kompilacji systemu iOS na komputerze Mac lub w chmurze

Aby można było uruchomić aplikację Cordova w systemie iOS przy użyciu programu Visual Studio, wykonaj kroki opisane w [przewodniku Instalatora systemu iOS][12] w celu zainstalowania i uruchomienia zdalnego agenta kompilacji.

Upewnij się, że możesz skompilować aplikację dla systemu iOS. Kroki opisane w przewodniku Instalatora są wymagane do kompilowania aplikacji dla systemu iOS w programie Visual Studio. Jeśli nie masz komputera Mac, możesz skompilować dla systemu iOS za pomocą zdalnego agenta kompilacji w usłudze, takiej jak MacInCloud. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji systemu iOS w chmurze][21].

> [!NOTE]
> Do korzystania z wtyczki wypychania w systemie iOS wymagany jest Xcode 7 lub nowszy.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Znajdź identyfikator, który ma być używany jako identyfikator aplikacji

Przed zarejestrowaniem aplikacji do powiadomień wypychanych Otwórz plik config. XML w aplikacji oprogramowania Cordova, Znajdź wartość atrybutu `id` w elemencie widget, a następnie skopiuj ją do późniejszego użycia. W poniższym kodzie XML identyfikator jest `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Później Użyj tego identyfikatora podczas tworzenia identyfikatora aplikacji w portalu dla deweloperów firmy Apple. Jeśli utworzysz inny identyfikator aplikacji w portalu dla deweloperów, w dalszej części tego samouczka musisz wykonać kilka dodatkowych kroków. Identyfikator w elemencie widget musi być zgodny z IDENTYFIKATORem aplikacji w portalu dla deweloperów.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Rejestrowanie aplikacji do powiadomień wypychanych w portalu dla deweloperów firmy Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Sprawdź, czy identyfikator aplikacji jest zgodny z aplikacją Cordova

Jeśli identyfikator aplikacji utworzony na koncie dewelopera firmy Apple jest już zgodny z IDENTYFIKATORem elementu widget w pliku config. XML, możesz pominąć ten krok. Jeśli jednak identyfikatory nie są zgodne, wykonaj następujące czynności:

1. Usuń folder platform z projektu.
2. Usuń folder wtyczek z projektu.
3. Usuń folder node_modules z projektu.
4. Zaktualizuj atrybut ID elementu widget w pliku config. XML, aby użyć identyfikatora aplikacji utworzonego na koncie dewelopera firmy Apple.
5. Ponownie skompiluj projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu iOS

1. W programie Visual Studio upewnij się, że dla elementu docelowego wdrożenia wybrano opcję **iOS** . Następnie wybierz pozycję **urządzenie** , aby uruchomić powiadomienia wypychane na podłączonym urządzeniu z systemem iOS.

    Powiadomienia wypychane można uruchomić na urządzeniu z systemem iOS, które jest podłączone do komputera za pomocą programu iTunes. Symulator systemu iOS nie obsługuje powiadomień wypychanych.

2. Wybierz przycisk **Uruchom** lub **F5** w programie Visual Studio, aby skompilować projekt i uruchomić aplikację na urządzeniu z systemem iOS. Następnie wybierz przycisk **OK** , aby zaakceptować powiadomienia wypychane.

   > [!NOTE]
   > Aplikacja żąda potwierdzenia powiadomień wypychanych podczas pierwszego uruchomienia.

3. W aplikacji wpisz zadanie, a następnie wybierz ikonę znaku plus **(+)** .
4. Sprawdź, czy odebrano powiadomienie. Następnie wybierz przycisk **OK** , aby odrzucić powiadomienie.

## <a name="optional-configure-and-run-on-windows"></a>Obowiązkowe Konfigurowanie i uruchamianie w systemie Windows

W tej sekcji opisano sposób uruchamiania projektu aplikacji Apache Cordova na urządzeniach z systemem Windows 10 (wtyczka wypychana PhoneGap jest obsługiwana w systemie Windows 10). Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Rejestrowanie aplikacji systemu Windows na potrzeby powiadomień wypychanych za pomocą usługi WNS

Aby użyć opcji sklepu w programie Visual Studio, wybierz obiekt docelowy systemu Windows z listy platform rozwiązań, np. **Windows-x64** lub **Windows-x86**. (Unikaj używania powiadomień wypychanych w **systemie Windows** ).

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Obejrzyj wideo przedstawiające podobne kroki][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie centrum powiadomień dla WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurowanie aplikacji oprogramowania Cordova do obsługi powiadomień wypychanych systemu Windows

Otwórz projektanta konfiguracji, klikając prawym przyciskiem myszy **plik config. XML**. Następnie wybierz pozycję **Projektant widoków**. Następnie wybierz kartę **Windows** , a następnie wybierz pozycję **Windows 10** w obszarze **wersja docelowa systemu Windows**.

Aby obsługiwać powiadomienia wypychane w ramach domyślnych (debugowania) kompilacji, Otwórz plik **Build. JSON** . Następnie skopiuj konfigurację "Release" do konfiguracji debugowania.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Po aktualizacji plik **Build. JSON** powinien zawierać następujący kod:

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

Skompiluj aplikację i sprawdź, czy nie ma żadnych błędów. Twoja aplikacja kliencka powinna teraz rejestrować się w celu otrzymywania powiadomień z zaplecza Mobile Apps. Powtórz tę sekcję dla każdego projektu systemu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu Windows

W programie Visual Studio upewnij się, że platforma systemu Windows jest wybrana jako miejsce docelowe wdrożenia, na przykład **Windows-x64** lub **Windows-x86**. Aby uruchomić aplikację na komputerze z systemem Windows 10, na którym znajduje się program Visual Studio, wybierz pozycję **komputer lokalny**.

1. Wybierz przycisk **Run (Uruchom** ), aby skompilować projekt i uruchomić aplikację.

2. W aplikacji wpisz nazwę nowego TodoItem, a następnie wybierz ikonę znaku plus **(+)** , aby ją dodać.

Sprawdź, czy odebrano powiadomienie po dodaniu elementu.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj informacje o [Notification Hubs][17] , aby dowiedzieć się więcej na temat powiadomień wypychanych.
* Jeśli jeszcze tego nie zrobiono, Kontynuuj samouczek, [dodając uwierzytelnianie][14] do aplikacji Apache Cordova.

Dowiedz się, jak używać następujących zestawów SDK:

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
