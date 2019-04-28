---
title: Wysyłanie powiadomień push do aplikacji programu Chrome przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji dla programu Chrome przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
keywords: powiadomienia wypychane na urządzeniach przenośnych, powiadomienia wypychane, powiadomienie wypychane, powiadomienia wypychane w przeglądarce chrome
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 03374f63345bd6c9e4f2b603443a1448493e1cdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875867"
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień push do aplikacji dla programu Chrome przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

W tym samouczku przedstawiono sposób tworzenia centrum powiadomień oraz wysyłania powiadomień push do przykładowej aplikacji dla programu Google Chrome przy użyciu usługi [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). Aplikacja dla programu Chrome działa w kontekście przeglądarki Google Chrome i rejestruje się w centrum powiadomień.

> [!NOTE]
> Powiadomienia wypychane aplikacji dla programu Chrome nie są standardowymi powiadomieniami w przeglądarce — są to specjalne powiadomienia modelu rozszerzeń przeglądarki. Szczegółowe informacje znajdują się w temacie [Chrome Apps Overview] (Omówienie aplikacji dla programu Chrome). Aplikacje dla programu Chrome mogą działać w przeglądarce dla komputerów oraz na urządzeniach przenośnych (Android i iOS) przy użyciu platformy Apache Cordova. Aby uzyskać więcej informacji, zobacz [Chrome Apps on Mobile] (Aplikacja dla programu Chrome na urządzeniach przenośnych).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * [Włączanie usługi Google Cloud Messaging](#register)
> * [Konfigurowanie centrum powiadomień](#configure-hub)
> * [Łączenie aplikacji dla programu Chrome z centrum powiadomień](#connect-app)
> * [Wysyłanie powiadomienia wypychanego do aplikacji dla programu Chrome](#send)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a id="register"></a>Włączanie usługi Google Cloud Messaging

1. Przejdź do witryny [Google Cloud Console](https://console.cloud.google.com/cloud-resource-manager) i zaloguj się przy użyciu poświadczeń konta Google
2. Wybierz opcję **Create Project** (Utwórz projekt) na pasku narzędzi.

    ![Przycisk Create Project (Utwórz projekt)](media/notification-hubs-chrome-get-started/create-project-button.png)
3. Wprowadź odpowiednią nazwę w polu **Project name** (Nazwa projektu), a następnie kliknij przycisk **Create** (Utwórz).
4. Wybierz ikonę powiadomień (dzwonek) na pasku narzędzi, a następnie wybierz komunikat **Create Project** (Utwórz projekt).

    ![Powiadomienie o utworzeniu projektu](media/notification-hubs-chrome-get-started/project-creation-notification.png)
5. Zanotuj wartość w polu **Project Number** (Numer projektu) na stronie **Projects** (Projekty) dla utworzonego projektu. Numer projektu jest używany jako **GCM Sender ID** (Identyfikator nadawcy usługi GCM) w aplikacji dla programu Chrome w celu rejestracji w usłudze GCM.

    ![Google Cloud Console — numer projektu](media/notification-hubs-chrome-get-started/gcm-project-number.png)
6. Na pulpicie nawigacyjnym wybierz pozycję **Go to APIs overview** (Przejdź do omówienia interfejsów API).

    ![Przycisk Go to APIs overview (Przejdź do omówienia interfejsu API)](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
7. Na stronie API & Services (Interfejsy API i usługi) wybierz pozycję **Enable APIs and Services** (Włącz interfejsy API i usługi).

    ![Włączanie interfejsów API i usług](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
8. Przeszukaj listę przy użyciu słowa kluczowego **cloud messaging**. Wybierz opcję **Google Cloud Messaging** z odfiltrowanej listy.

    ![Interfejs API usługi Google Cloud Messaging](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
9. Na stronie **Google Cloud Messaging** wybierz pozycję **Enable** (Włącz).

    ![Włączanie usługi GCM](media/notification-hubs-chrome-get-started/enable-gcm.png)
10. Na stronie **API & Services** (Interfejsy API i usługi) przejdź do karty **Credentials** (Poświadczenia). Wybierz pozycję **Create credentials** (Utwórz poświadczenia), a następnie wybierz pozycję **API key** (Klucz interfejsu API).

    ![Przycisk tworzenia klucza interfejsu API](media/notification-hubs-chrome-get-started/create-api-key-button.png)
11. W oknie dialogowym **API key created** (Utworzono klucz interfejsu API) wybierz przycisk kopiowania, aby skopiować klucz do schowka. Zapisz go gdzieś. Wartość ta jest używana podczas konfigurowania centrum powiadomień w następnej sekcji, aby umożliwić wysyłanie powiadomień push do usługi GCM.

    ![Strona interfejsu API](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Wybierz klucz interfejsu API z listy **API keys** (Klucze interfejsu API). Na stronie klucza interfejsu API wybierz pozycję **IP addresses (web servers, cron jobs, etc.)** (Adresy IP — serwery internetowe, zadania cron itp.), a następnie wprowadź wartość **0.0.0.0/0** dla adresu IP i kliknij pozycję Save (Zapisz).

    ![Włączanie adresów IP](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Tworzenie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Wybierz pozycję **Google (GCM)** w kategorii **USTAWIENIA POWIADOMIEŃ**, wprowadź **klucz interfejsu API** dla projektu GCM i kliknij pozycję **Zapisz**.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs — Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>Łączenie aplikacji dla programu Chrome z centrum powiadomień

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą GCM i uzyskano parametry połączenia do rejestrowania aplikacji w celu odbierania i wysyłania powiadomień wypychanych.

### <a name="create-a-new-chrome-app"></a>Tworzenie nowej aplikacji dla programu Chrome

Poniższy przykład jest oparty na [przykładzie usługi GCM dla aplikacji dla programu Chrome] i przedstawiono w nim zalecany sposób tworzenia aplikacji dla programu Chrome. W tej sekcji opisano kroki, które są specyficzne dla usługi Azure Notification Hubs.

> [!NOTE]
> Zalecane jest pobranie źródła tej aplikacji dla programu Chrome z [przykładu centrum powiadomień aplikacji dla programu Chrome].

Aplikacja dla programu Chrome jest tworzona przy użyciu języka JavaScript i można ją utworzyć za pomocą dowolnego edytora tekstu. Na poniższej ilustracji przedstawiono, jak wygląda aplikacja dla programu Chrome:

![Aplikacja dla programu Google Chrome][15]

1. Utwórz folder i nadaj mu nazwę `ChromePushApp`. Możesz użyć dowolnej nazwy. Jeśli użyjesz innej nazwy, musisz zmienić ścieżkę w wymaganych segmentach kodu.
2. Pobierz [bibliotekę crypto-js] do folderu utworzonego w drugim kroku. Ten folder biblioteki zawiera dwa podfoldery: `components` i `rollups`.
3. Utwórz plik `manifest.json`. Dla wszystkich aplikacji dla programu Chrome jest tworzony plik manifestu zawierający metadane aplikacji oraz, co najistotniejsze, wszystkie uprawnienia przyznane aplikacji podczas instalacji przez użytkownika.

    ```json
    {
        "name": "NH-GCM Notifications",
        "description": "Chrome platform app.",
        "manifest_version": 2,
        "version": "0.1",
        "app": {
        "background": {
            "scripts": ["background.js"]
        }
        },
        "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
        "icons": { "128": "gcm_128.png" }
    }
    ```

    Zwróć uwagę na element `permissions`, który określa, że ta aplikacja dla programu Chrome może odbierać powiadomienia wypychane z usługi GCM. Przykładowa aplikacja używa również pliku ikony, `gcm_128.png`, który można znaleźć w źródle pochodzącym z oryginalnego przykładu usługi GCM. Można zastąpić go dowolnym obrazem spełniającym [kryteria ikony](https://developer.chrome.com/apps/manifest/icons).
4. Utwórz plik o nazwie `background.js` zawierający następujący kod:

    ```javascript
    // Returns a new notification ID used in the notification.
    function getNotificationId() {
        var id = Math.floor(Math.random() * 9007199254740992) + 1;
        return id.toString();
    }

    function messageReceived(message) {
        // A message is an object with a data property that
        // consists of key-value pairs.

        // Concatenate all key-value pairs to form a display string.
        var messageString = "";
        for (var key in message.data) {
        if (messageString != "")
            messageString += ", "
        messageString += key + ":" + message.data[key];
        }
        console.log("Message received: " + messageString);

        // Pop up a notification to show the GCM message.
        chrome.notifications.create(getNotificationId(), {
        title: 'GCM Message',
        iconUrl: 'gcm_128.png',
        type: 'basic',
        message: messageString
        }, function() {});
    }

    var registerWindowCreated = false;

    function firstTimeRegistration() {
        chrome.storage.local.get("registered", function(result) {

        registerWindowCreated = true;
        chrome.app.window.create(
            "register.html",
            {  width: 520,
                height: 500,
                frame: 'chrome'
            },
            function(appWin) {}
        );
        });
    }

    // Set up a listener for GCM message event.
    chrome.gcm.onMessage.addListener(messageReceived);

    // Set up listeners to trigger the first-time registration.
    chrome.runtime.onInstalled.addListener(firstTimeRegistration);
    chrome.runtime.onStartup.addListener(firstTimeRegistration);
    ```

    To plik, który powoduje wyświetlenie strony HTML (`register.html`) w oknie aplikacji dla programu Chrome, a także określa procedurę obsługi `messageReceived` służącą do obsługi przychodzących powiadomień push.
5. Utwórz plik o nazwie `register.html`, który określa interfejs użytkownika aplikacji dla programu Chrome.

   > [!NOTE]
   > W tym przykładzie użyto biblioteki **CryptoJS w wersji 3.1.2**. Jeśli pobrano inną wersję biblioteki, upewnij się, że prawidłowo zastąpiono wersję w ścieżce `src`.

    ```html
    <html>
        <head>
            <title>GCM Registration</title>
            <script src="register.js"></script>
            <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
            <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
        <body>
            Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
            <button id="registerWithGCM">Register with GCM</button>
            <br/>
            <br/>
            <br/>

            Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
            Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

            <br/>
            <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
            <br/>
            <br/>

            <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
        </body>
    </html>
    ```
6. Utwórz plik o nazwie `register.js` zawierający kod zamieszczony w tym kroku. Ten plik zawiera skrypt użyty w pliku `register.html`. Wykonywanie śródwierszowe w aplikacjach dla programu Chrome jest niedozwolone, dlatego należy utworzyć oddzielny skrypt pomocniczy dla interfejsu użytkownika.

    ```javascript
    var registrationId = "";
    var hubName        = "", connectionString = "";
    var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

    window.onload = function() {
        document.getElementById("registerWithGCM").onclick = registerWithGCM;  
        document.getElementById("registerWithNH").onclick = registerWithNH;
        updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
    }

    function updateLog(status) {
        currentStatus = document.getElementById("console").innerHTML;
        if (currentStatus != "") {
        currentStatus = currentStatus + "\n\n";
        }

        document.getElementById("console").innerHTML = currentStatus  + status;
    }

    function registerWithGCM() {
        var senderId = document.getElementById("senderId").value.trim();
        chrome.gcm.register([senderId], registerCallback);

        // Prevent register button from being clicked again before the registration finishes.
        document.getElementById("registerWithGCM").disabled = true;
    }

    function registerCallback(regId) {
        registrationId = regId;
        document.getElementById("registerWithGCM").disabled = false;

        if (chrome.runtime.lastError) {
        // When the registration fails, handle the error and retry the
        // registration later.
        updateLog("Registration failed: " + chrome.runtime.lastError.message);
        return;
        }

        updateLog("Registration with GCM succeeded.");
        document.getElementById("registerWithNH").disabled = false;

        // Mark that the first-time registration is done.
        chrome.storage.local.set({registered: true});
    }

    function registerWithNH() {
        hubName = document.getElementById("hubName").value.trim();
        connectionString = document.getElementById("connectionString").value.trim();
        splitConnectionString();
        generateSaSToken();
        sendNHRegistrationRequest();
    }

    // From https://msdn.microsoft.com/library/dn495627.aspx
    function splitConnectionString()
    {
        var parts = connectionString.split(';');
        if (parts.length != 3)
        throw "Error parsing connection string";

        parts.forEach(function(part) {
        if (part.indexOf('Endpoint') == 0) {
        endpoint = 'https' + part.substring(11);
        } else if (part.indexOf('SharedAccessKeyName') == 0) {
        sasKeyName = part.substring(20);
        } else if (part.indexOf('SharedAccessKey') == 0) {
        sasKeyValue = part.substring(16);
        }
        });

        originalUri = endpoint + hubName;
    }

    function generateSaSToken()
    {
        targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
        var expiresInMins = 10; // 10 minute expiration

        // Set expiration in seconds.
        var expireOnDate = new Date();
        expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
        var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
        .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
        .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
        .getUTCSeconds()) / 1000;
        var tosign = targetUri + '\n' + expires;

        // Using CryptoJS.
        var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
        var base64signature = signature.toString(CryptoJS.enc.Base64);
        var base64UriEncoded = encodeURIComponent(base64signature);

        // Construct authorization string.
        sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
    }

    function sendNHRegistrationRequest()
    {
        var registrationPayload =
        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
        "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
            "<content type=\"application/xml\">" +
                "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                    "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                "</GcmRegistrationDescription>" +
            "</content>" +
        "</entry>";

        // Update the payload with the registration ID obtained earlier.
        registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

        var url = originalUri + "/registrations/?api-version=2014-09";
        var client = new XMLHttpRequest();

        client.onload = function () {
        if (client.readyState == 4) {
            if (client.status == 200) {
            updateLog("Notification Hub Registration successful!");
            updateLog(client.responseText);
            } else {
            updateLog("Notification Hub Registration did not succeed!");
            updateLog("HTTP Status: " + client.status + " : " + client.statusText);
            updateLog("HTTP Response: " + "\n" + client.responseText);
            }
        }
        };

        client.onerror = function () {
            updateLog("ERROR - Notification Hub Registration did not succeed!");
        }

        client.open("POST", url, true);
        client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
        client.setRequestHeader("Authorization", sasToken);
        client.setRequestHeader("x-ms-version", "2014-09");

        try {
            client.send(registrationPayload);
        }
        catch(err) {
            updateLog(err.message);
        }
    }
    ```

    Skrypt zawiera następujące najważniejsze parametry:

   * `window.onload` określa zdarzenia dotyczące kliknięcia przycisków dla dwóch przycisków w interfejsie użytkownika. Pierwsza procedura obsługi zdarzenia dotyczącego kliknięcia przycisku służy do rejestracji w usłudze GCM, a druga używa identyfikatora rejestracji zwróconego po zarejestrowaniu w usłudze GCM do rejestracji w usłudze Azure Notification Hubs.
   * `updateLog` jest funkcją, która umożliwia rejestrowanie informacji przez kod.
   * `registerWithGCM` jest procedurą obsługi kliknięcia pierwszego przycisku, który wykonuje wywołanie `chrome.gcm.register` do usługi GCM w celu zarejestrowania bieżącego wystąpienia aplikacji dla programu Chrome.
   * `registerCallback` jest funkcją wywołania zwrotnego, która jest wywoływana po zwróceniu danych przez wywołanie rejestracji w usłudze GCM.
   * `registerWithNH` jest programem obsługi kliknięcia drugiego przycisku, który dokonuje rejestracji w usłudze Notification Hubs. Pobiera on właściwości `hubName` oraz `connectionString` (określone przez użytkownika) i tworzy wywołanie rejestracji w usłudze Notification Hubs za pomocą interfejsu API REST.
   * `splitConnectionString` i `generateSaSToken` są pomocnikami reprezentującymi implementację procesu tworzenia tokenu SaS w języku JavaScript, która musi być używana we wszystkich wywołaniach interfejsu API REST. Aby uzyskać więcej informacji, zobacz temat [Common Concepts](https://msdn.microsoft.com/library/dn495627.aspx) (Wspólne pojęcia).
   * `sendNHRegistrationRequest` jest funkcją wykonującą wywołanie interfejsu REST protokołu HTTP do usługi Azure Notification Hubs.
   * `registrationPayload` definiuje ładunek XML rejestracji. Aby uzyskać więcej informacji, zobacz temat [Create Registration NH REST API] (Interfejs API REST rejestracji w usłudze Notification Hubs). Zaktualizuj w nim identyfikator rejestracji, używając wartości odebranej z usługi GCM.
   * `client` jest wystąpieniem obiektu `XMLHttpRequest` używanym przez aplikację do tworzenia żądań POST protokołu HTTP. Zaktualizuj nagłówek `Authorization` przy użyciu elementu `sasToken`. Pomyślne wykonanie tego wywołania spowoduje zarejestrowanie tego wystąpienia aplikacji dla programu Chrome w usłudze Azure Notification Hubs.

     Ogólna struktura folderów dla tego projektu powinna przypominać następującą: ![Aplikacja dla programu Google Chrome — struktura folderów][21]

### <a name="set-up-and-test-your-chrome-app"></a>Instalowanie i testowanie aplikacji dla programu Chrome

1. Otwórz przeglądarkę Chrome. Otwórz **stronę Rozszerzenia programu Chrome** i włącz **Tryb programisty**.

    ![Google Chrome — włączanie trybu programisty][16]
2. Kliknij przycisk **Wczytaj rozszerzenie bez pakietu** i przejdź do folderu, w którym utworzono pliki. Możesz również użyć narzędzia **Chrome Apps & Extensions Developer Tool**. To narzędzie samo w sobie jest aplikacją dla programu Chrome (instalowaną ze sklepu Chrome Web Store) i zapewnia zaawansowane możliwości debugowania podczas tworzenia aplikacji dla programu Chrome.

    ![Google Chrome — Wczytaj rozszerzenie bez pakietu][17]
3. Jeśli aplikacja dla programu Chrome została utworzona bez żadnych błędów, zostanie wyświetlona.

    ![Google Chrome — wyświetlona aplikacja dla programu Chrome][18]
4. Wprowadź wartość w polu **Project Number** (Numer projektu), którą uzyskano wcześniej w witrynie **Google Cloud Console**, jako identyfikator nadawcy i kliknij przycisk **Register with GCM** (Zarejestruj w usłudze GCM). Musi zostać wyświetlony komunikat **Registration with GCM succeeded** (Pomyślnie zarejestrowano w usłudze GCM).

    ![Google Chrome — dostosowywanie aplikacji dla programu Chrome][19]
5. Wprowadź wartość w polu **Notification Hub Name** (Nazwa centrum powiadomień) oraz wartość **DefaultListenSharedAccessSignature** uzyskaną wcześniej w portalu i kliknij przycisk pozycję **Register with Azure Notification Hub** (Zarejestruj w centrum powiadomień Azure). Musi zostać wyświetlony komunikat **Notification Hub Registration successful!** (Pomyślnie zarejestrowano w centrum powiadomień!) oraz szczegóły odpowiedzi dotyczącej rejestracji, które zawierają identyfikator rejestracji w usłudze Azure Notification Hubs.

    ![Google Chrome — określanie szczegółów centrum powiadomień][20]  

## <a name="send"></a>Wysyłanie powiadomienia do aplikacji dla programu Chrome

W celach testowych wyślij powiadomienia wypychane programu Chrome przy użyciu aplikacji konsolowej programu .NET.

> [!NOTE]
> Powiadomienia wypychane można wysyłać przy użyciu usługi Notification Hubs z poziomu dowolnego zaplecza za pośrednictwem publicznego [interfejsu REST](https://msdn.microsoft.com/library/windowsazure/dn223264.aspx). Odwiedź [portal dokumentacji](https://azure.microsoft.com/documentation/services/notification-hubs/), aby uzyskać więcej przykładów dla różnych platform.

1. W programie Visual Studio z menu **Plik** wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. W obszarze **Visual C#** kliknij pozycję **Windows**, kliknij pozycję **Aplikacja konsolowa**, a następnie kliknij przycisk **OK**.  Ten krok powoduje utworzenie nowego projektu aplikacji konsolowej.
2. W menu **Narzędzia** kliknij pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**. W dolnym oknie zostanie wyświetlona Konsola menedżera pakietów.
3. W oknie konsoli wykonaj następujące polecenie:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

   Do projektu zostanie automatycznie dodane odwołanie do zestawu Azure Service Bus SDK za pomocą [pakietu NuGet WindowsAzure.ServiceBus](https://nuget.org/packages/WindowsAzure.ServiceBus/).
4. Otwórz plik `Program.cs` i dodaj następującą instrukcję `using`:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```
5. W klasie `Program` dodaj następującą metodę:

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
        await hub.SendGcmNativeNotificationAsync(message);
    }
    ```

    Zastąp symbol zastępczy `<hub name>` nazwą centrum powiadomień wyświetlaną w [portalu](https://portal.azure.com) na stronie centrum powiadomień. Ponadto zastąp symbol zastępczy parametrów połączenia parametrami połączenia o nazwie `DefaultFullSharedAccessSignature` uzyskanymi w sekcji konfiguracji centrum powiadomień.

    > [!NOTE]
    > Upewnij się, że użyto parametrów połączenia z uprawnieniami dostępu **Pełne**, a nie **Nasłuchiwanie**. Parametry połączenia z uprawnieniami dostępu **Nasłuchiwanie** nie przyznają uprawnień do wysyłania powiadomień wypychanych.

6. Dodaj następujące wywołania do metody `Main`:

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

7. Upewnij się, że program Chrome jest uruchomiony, i uruchom aplikację konsolową.
8. Na pulpicie powinno zostać wyświetlone następujące powiadomienie.

    ![Google Chrome — powiadomienie][13]
9. Wszystkie powiadomienia można również wyświetlić przy użyciu okna powiadomień programu Chrome na pasku zadań (w systemie Windows), gdy program Chrome jest uruchomiony.

    ![Google Chrome — lista powiadomień](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Aplikacja dla programu Chrome nie musi być uruchomiona lub otwarta w przeglądarce (chociaż przeglądarka Chrome musi być uruchomiona). Okno powiadomień programu Chrome umożliwia również wyświetlenie skonsolidowanego widoku wszystkich powiadomień.

## <a name="next-steps"> </a>Następne kroki

W tym samouczku wysłano powiadomienia rozgłaszane do wszystkich klientów zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[przykładu centrum powiadomień aplikacji dla programu Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Chrome Apps Overview]: https://developer.chrome.com/apps/about_apps (Omówienie aplikacji dla programu Chrome)
[przykładzie usługi GCM dla aplikacji dla programu Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome Apps on Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile (Aplikacja dla programu Chrome na urządzeniach przenośnych)
[Create Registration NH REST API]: https://msdn.microsoft.com/library/azure/dn223265.aspx (Interfejs API REST tworzenia rejestracji w usłudze Notification Hubs)
[bibliotekę crypto-js]: https://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
