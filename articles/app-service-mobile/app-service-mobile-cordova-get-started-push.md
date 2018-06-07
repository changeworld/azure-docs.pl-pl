---
title: Dodawanie powiadomień wypychanych do aplikacji oprogramowania Apache Cordova za pomocą funkcji Mobile Apps w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać do wysyłania powiadomień wypychanych do aplikacji platformy Apache Cordova Mobile Apps.
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
ms.openlocfilehash: 13c1a53cfa3f998c9e3fa3ee1ee2dcec37357095
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597998"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Przegląd
W tym samouczku, dodawanie powiadomień wypychanych do [szybkiego startu Apache Cordova] [ 5] projektu, aby powiadomienia wypychane są wysyłane do urządzenia, za każdym razem, gdy wstawieniu rekordu.

Pobrany projekt szybkiego startu serwera nie jest używany, należy najpierw pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracy z serwera zaplecza .NET SDK dla aplikacji mobilnych][1].

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek zakłada, że masz aplikację oprogramowania Apache Cordova, który został utworzony przy użyciu programu Visual Studio 2015. To urządzenie należy uruchomić na Emulator systemu Google Android, urządzenia z systemem Android, urządzenia z systemem Windows lub urządzenia z systemem iOS.

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Komputer z programem [Visual Studio Community 2015] [ 2] lub nowszy
* [Visual Studio Tools for Apache Cordova][4]
* [Aktywne konto platformy Azure][3]
* Ukończono [szybkiego startu Apache Cordova] [ 5] projektu
* (Android) A [konto Google] [ 6] z ze zweryfikowanym adresem e-mail
* (iOS) [Członkostwo w programie dla deweloperów firmy Apple] [ 7] i urządzenia z systemem iOS (iOS Simulator nie obsługuje powiadomienia wypychane)
* (System Windows) A [konto dewelopera w sklepie Microsoft] [ 8] i urządzeń z systemem Windows 10

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Obejrzyj film wideo przedstawiający kroki opisane w tej sekcji][9].

## <a name="update-the-server-project"></a>Aktualizowanie projektu serwera
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modyfikowanie aplikacji platformy Cordova
Aby upewnić się, że Twój projekt aplikacji oprogramowania Apache Cordova jest gotowy do obsługi powiadomień wypychanych, zainstaluj wtyczki Cordova wypychania plus żadnych usług powiadomień wypychanych specyficzne dla platformy.

#### <a name="update-the-cordova-version-in-your-project"></a>Zaktualizuj wersję oprogramowania Cordova w projekcie.
Jeśli projekt używa wersji oprogramowania Apache Cordova, która jest starsza niż wersja 6.1.1, należy zaktualizować projekt klienta. Aby zaktualizować projektu, należy wykonać następujące czynności: 

* Aby otworzyć projektanta konfiguracji, kliknij prawym przyciskiem myszy `config.xml`.
* Wybierz **platform** kartę.
* W **interfejsu Cordova CLI** pole tekstowe, wybierz opcję **6.1.1**. 
* Aby zaktualizować projektu, zaznacz **kompilacji**, a następnie wybierz **Kompiluj rozwiązanie**.

#### <a name="install-the-push-plugin"></a>Instalowanie wtyczki wypychania
Aplikacji oprogramowania Apache Cordova nie obsługują natywnie możliwości urządzenia lub sieci.  Te możliwości są udostępniane przez wtyczek, które są publikowane, albo na [npm] [ 10] lub w witrynie GitHub. `phonegap-plugin-push` Wtyczki obsługuje powiadomienia wypychane w sieci.

Można zainstalować dodatek wypychania w jednym z następujących sposobów:

**W wierszu polecenia:**

Uruchom następujące polecenie:

    cordova plugin add phonegap-plugin-push

**Z poziomu programu Visual Studio:**

1. W Eksploratorze rozwiązań Otwórz `config.xml` pliku. Następnie wybierz pozycję **wtyczek** > **niestandardowy**. Następnie wybierz **Git** jako źródła instalacji. 
    
2. Wprowadź `https://github.com/phonegap/phonegap-plugin-push` jako źródło.

    ![Otwórz plik config.xml w Eksploratorze rozwiązań][img1]

3. Wybierz strzałkę obok źródła instalacji.

4. W **SENDER_ID**, jeśli masz już identyfikator liczbowych projektu dla projektu konsoli dla deweloperów Google, możesz dodać ją tutaj. W przeciwnym razie Podaj wartość symbolu zastępczego, na przykład 777777. Jeśli ma być przeznaczona dla systemu Android, należy zaktualizować tę wartość w pliku config.xml później.

    >[!NOTE]
    >Począwszy od wersji 2.0.0 google services.json musi być zainstalowany w folderze głównym projektu Aby skonfigurować identyfikator nadawcy. Aby uzyskać więcej informacji, zobacz [dokumentacji instalacji.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)
5. Wybierz pozycję **Dodaj**.

Dodatek wypychania jest zainstalowany.

#### <a name="install-the-device-plugin"></a>Instalowanie wtyczki urządzenia
Postępuj zgodnie z tą samą procedurą, którego użyto Instalowanie wtyczki wypychania. Dodaj wtyczkę urządzenie z listy wtyczek Core. (Można go znaleźć, wybierz **wtyczek** > **Core**.) Należy to dodatek plug-in, aby uzyskać nazwę platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Zarejestruj urządzenie podczas uruchamiania aplikacji 
Początkowo przeprowadzamy minimalnego kodu dla systemu Android. Później można zmodyfikować aplikację do uruchamiania w systemie iOS lub Windows 10.

1. Dodaj wywołanie do **registerForPushNotifications** podczas wywołania zwrotnego dla procesu logowania. Alternatywnie, można go dodać w dolnej części **onDeviceReady** metody:

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

    Ten przykład przedstawia wywoływanie **registerForPushNotifications** po pomyślnym uwierzytelnieniu. Możesz wywołać `registerForPushNotifications()` tyle razy, ile jest wymagana.

2. Dodaj nowe **registerForPushNotifications** metody w następujący sposób:

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
3. (Android) W powyższym kodzie Zamień `Your_Project_ID` liczbowym identyfikator projektu dla aplikacji z [konsoli dla deweloperów Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcjonalnie) Konfigurowanie i uruchamianie aplikacji w systemie Android
Wykonaj tę sekcję, aby włączyć powiadomień wypychanych dla systemu Android.

#### <a name="enable-gcm"></a>Włącz Firebase Cloud Messaging
Ponieważ są początkowo korzystających z platformy systemu Google Android, należy włączyć Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurowanie zaplecza aplikacji mobilnej do wysyłania żądań wypychanych przy użyciu FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurowanie aplikacji platformy Cordova dla systemu Android
Otwórz plik config.xml w aplikacji platformy Cordova. Następnie zastąp `Your_Project_ID` liczbowym identyfikator projektu dla aplikacji z [konsoli dla deweloperów Google][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Otwórz index.js. Następnie zaktualizuj kod, aby używać swojego identyfikatora liczbowego projektu.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Konfigurowanie urządzenia z systemem Android do debugowania USB
Zanim będzie można wdrożyć aplikację na urządzeniu z systemem Android, musisz włączyć debugowanie USB. Wykonaj następujące czynności na telefonie z systemem Android:

1. Przejdź do **ustawienia** > **informacje o telefonie**. Naciśnij przycisk **numer kompilacji** dopóki (około siedem razy) jest włączony tryb dewelopera.
2. W **ustawienia** > **opcje dewelopera**, Włącz **debugowanie USB**. Następnie podłącz telefon z systemem Android do programowania komputera za pomocą kabla USB.

Firma Microsoft przetestowane to przy użyciu węzła Google 5 X urządzeniu z systemem Android 6.0 (Marshmallow). Jednak te techniki są często używane przez wszystkie nowoczesne wersji dla systemu Android.

#### <a name="install-google-play-services"></a>Zainstaluj usługi Google Play
Dodatek wypychania zależy od systemu Android usług Google Play dla powiadomień wypychanych.

1. W programie Visual Studio, wybierz **narzędzia** > **Android** > **Android SDK Manager**. Następnie rozwiń węzeł **dodatki** folderu. Zaznacz odpowiednie pola, aby upewnić się, każdy z następujących zestawów SDK jest zainstalowany:

   * Android 2.3 lub nowszej
   * Poprawki repozytorium Google 27 lub nowszej
   * Usług Google Play 9.0.2 lub nowszej

2. Wybierz **zainstalować pakiety**. Poczekaj na zakończenie instalacji.

Bieżący wymaganych bibliotek są wyświetlane w [phonegap wtyczka wypychana instalacja dokumentacji][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testowych powiadomień wypychanych w aplikacji w systemie Android
Możesz teraz testowych powiadomień wypychanych przez aplikację i wstawianie elementów w tabeli TodoItem. Można przetestować z tego samego urządzenia lub drugiego urządzenia, tak długo, jak w przypadku korzystania z tego samego zaplecza. Testowanie aplikacji Cordova na platformie Android w jednym z następujących sposobów:

* *Na urządzeniu fizycznym:* Dołączanie urządzenia z systemem Android do komputera programowanie za pomocą kabla USB.  Zamiast **Emulator systemu Google Android**, wybierz pozycję **urządzenia**. Visual Studio wdraża aplikację na urządzeniu i uruchamia aplikację. Następnie zakłócają aplikacji na urządzeniu.

  Udostępnianie ekranu aplikacje, takie jak [Mobizen] [ 20] mogą pomóc w tworzeniu aplikacji systemu Android. Mobizen projektów dla systemu Android ekranu w przeglądarce sieci web na komputerze.

* *Na emulatorze systemu Android:* są dodatkowe czynności konfiguracyjne, które są wymagane, gdy używasz emulatora.

    Upewnij się, że jest wdrażany z urządzenia wirtualnego, który ma ustawioną jako docelową, interfejsy API Google, jak pokazano w programie Android Virtual Device (AVD) manager.

    ![Menedżer urządzeń wirtualnych systemu android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Jeśli chcesz użyć szybsze x86 emulatora, [zainstalować sterownik HAXM][11], a następnie skonfiguruj emulator, aby go użyć.

    Dodaj konto Google na urządzeniu z systemem Android, wybierając **aplikacje** > **ustawienia** > **Dodaj konto**. Następnie postępuj zgodnie z monitami.

    ![Dodaj konto Google na urządzeniu z systemem Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Uruchamianie aplikacji todolist jako przed i Wstaw nowe zadanie do wykonania. Teraz, w obszarze powiadomień jest wyświetlana ikona powiadomienia. Można także otworzyć menu powiadomień, aby wyświetlić pełny tekst powiadomienia.

    ![Wyświetl powiadomienie](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcjonalnie) Konfigurowanie i uruchamianie w systemie iOS
Ta sekcja dotyczy uruchamiania projektu Cordova na urządzeniach z systemem iOS. Jeśli nie są urządzeniami z systemem iOS, możesz pominąć tę sekcję.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Zainstaluj i uruchom agenta kompilacji zdalnej systemu iOS w usłudze Mac lub w chmurze
Przed uruchomieniem aplikacji Cordova w systemie iOS przy użyciu programu Visual Studio, przejdź do kroków [przewodnik ustawień systemu iOS] [ 12] do zainstalowania i uruchomienia agenta kompilacji zdalnej.

Upewnij się, że można tworzyć aplikacji dla systemu iOS. Kroki opisane w przewodniku po instalacji są wymagane do tworzenia aplikacji dla systemu iOS w programie Visual Studio. Jeśli nie masz Mac, można tworzyć dla systemu iOS przy użyciu zdalnego agenta kompilacji na usług, takich jak MacInCloud. Aby uzyskać więcej informacji, zobacz [uruchamianie aplikacji systemu iOS w chmurze][21].

> [!NOTE]
> XCode 7 lub nowszy jest wymagany do użycia wtyczki wypychania w systemie iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Znajdź identyfikator, który ma być używana jako Identyfikatora aplikacji
Przed rejestrowania aplikacji w taki sposób, aby powiadomienia wypychane, otwórz plik config.xml w aplikacji platformy Cordova `id` wartość w elemencie widget atrybutu, a następnie skopiować go do późniejszego użycia. W poniższych XML identyfikator jest `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Później za pomocą tego identyfikatora po utworzeniu Identyfikatora aplikacji w portalu dla deweloperów firmy Apple. Jeśli tworzysz inny identyfikator aplikacji w portalu dla deweloperów, należy wykonać kilka dodatkowych czynności w dalszej części tego samouczka. Identyfikator w elemencie widget muszą być zgodne, identyfikator aplikacji w portalu dla deweloperów.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Rejestrowanie aplikacji dla powiadomień wypychanych w portalu dla deweloperów firmy Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie usługi Azure do wysyłania powiadomień wypychanych
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Sprawdź, czy identyfikator aplikacji zgodna aplikacji platformy Cordova
Jeśli już utworzony na Twoim koncie deweloperów firmy Apple identyfikator aplikacji jest zgodny z Identyfikatorem elementu widget w pliku config.xml, możesz pominąć ten krok. Jednak jeśli identyfikatory nie są zgodne, należy wykonać następujące czynności:

1. Usuń folder platform z projektu.
2. Usuń folder wtyczki z projektu.
3. Usuń node_modules folder z projektu.
4. Aktualizacja atrybutu id elementu widget w pliku config.xml, aby użyć utworzonego w ramach Twojego konta dewelopera Apple Identyfikatora aplikacji.
5. Ponownie skompiluj projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testowych powiadomień wypychanych w aplikacji systemu iOS
1. W programie Visual Studio, upewnij się, że **iOS** został wybrany jako cel wdrożenia. Następnie wybierz **urządzenia** do uruchomienia na urządzeniu z systemem iOS połączonych powiadomień wypychanych.

    Powiadomienia wypychane można uruchamiać na urządzeniu z systemem iOS podłączonego do komputera za pomocą programu iTunes. Symulatora systemu iOS nie obsługuje powiadomień wypychanych.

2. Wybierz **Uruchom** przycisk lub **F5** w Visual Studio, aby skompilować projekt i uruchomić aplikację w urządzeniu z systemem iOS. Następnie wybierz **OK** do akceptowania powiadomień wypychanych.

   > [!NOTE]
   > Aplikacja prosi o potwierdzenie dla powiadomień wypychanych przy pierwszym uruchomieniu.

3. W aplikacji wpisz zadania, a następnie wybierz plus **(+)** ikony.
4. Sprawdź, czy Odebrano powiadomienie. Następnie wybierz **OK** na odrzucenie powiadomienia.

## <a name="optional-configure-and-run-on-windows"></a>(Opcjonalnie) Konfigurowanie i uruchamianie w systemie Windows
W tej sekcji opisano, jak uruchomić projekt aplikacji oprogramowania Apache Cordova na urządzeniach z systemem Windows 10 (PhoneGap wtyczki wypychania jest obsługiwany w systemie Windows 10). Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Rejestrowanie aplikacji systemu Windows dla powiadomień wypychanych z usługą WNS
Aby użyć opcji magazynu w programie Visual Studio, wybierz docelowy z systemem Windows z listy platformy rozwiązania, takie jak **Windows x64** lub **Windows x86**. (Uniknąć **Windows AnyCPU** dla powiadomień wypychanych.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Obejrzyj film wideo przedstawiający podobne kroki][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie Centrum powiadomień w przypadku usługi WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurowanie aplikacji platformy Cordova do obsługi powiadomień wypychanych systemu Windows
Otwórz projektanta konfiguracji, klikając prawym przyciskiem myszy **pliku config.xml**. Następnie wybierz **Widok projektanta**. Następnie wybierz pozycję **Windows** , a następnie wybierz **systemu Windows 10** w obszarze **Windows docelową wersję**.

Do obsługi powiadomień wypychanych w domyślnej (debugowanie) kompilacji, otwórz plik build.json. Następnie skopiuj konfiguracji "wersja" konfiguracji debugowania.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Po aktualizacji pliku build.json powinien zawierać następujący kod:

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

Tworzenie aplikacji i sprawdź, czy użytkownik nie ma błędów. Twoja aplikacja kliencka teraz należy zarejestrować odbieranie powiadomień z zaplecza aplikacji mobilnej. W tej sekcji należy powtórzyć dla każdego projektu systemu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowych powiadomień wypychanych w aplikacji systemu Windows
W programie Visual Studio, upewnij się, że platformy systemu Windows jest wybrany jako cel wdrożenia, takich jak **Windows x64** lub **Windows x86**. Aby uruchomić aplikację na komputerach z systemem Windows 10, który jest hostem programu Visual Studio, wybierz **komputera lokalnego**.

1. Wybierz **Uruchom** przycisk, aby skompilować projekt i uruchomić aplikację.

2. W aplikacji wpisz nazwę nowego zadania do wykonania, a następnie wybierz plus **(+)** ikonę, aby dodać go.

Sprawdź, czy otrzyma powiadomienie po dodaniu elementu.

## <a name="next-steps"></a>Następne kroki
* Przeczytaj informacje o [usługi Notification Hubs] [ 17] Aby dowiedzieć się więcej na temat powiadomień wypychanych.
* Jeśli jeszcze nie Kontynuuj samouczek przez [Dodawanie uwierzytelniania] [ 14] do swojej aplikacji Apache Cordova.

Dowiedz się, jak korzystać z następujących zestawów SDK:

* [Zestaw Apache Cordova SDK][15]
* [Zestaw ASP.NET Server SDK][1]
* [Zestaw node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
