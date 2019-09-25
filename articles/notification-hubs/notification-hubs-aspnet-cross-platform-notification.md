---
title: Wysyłanie powiadomień między platformami do użytkowników przy użyciu usługi Azure Notification Hubs (ASP.NET)
description: Dowiedz się, jak za pomocą szablonów Notification Hubs wysyłać powiadomienia w ramach pojedynczego żądania, czyli niezależny od na platformę, która jest przeznaczona dla wszystkich platform.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cea0d63c20af781fcfc6ba5d7c06061b12992702
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212026"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Wysyłanie powiadomień między platformami do użytkowników za pomocą Notification Hubs

W poprzednim samouczku [Powiadamianie użytkowników za pomocą Notification Hubs], wiesz, jak wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych dla określonego uwierzytelnionego użytkownika. W tym samouczku do wysłania powiadomienia do każdej obsługiwanej platformy klienckiej są wymagane wiele żądań. Usługa Azure Notification Hubs obsługuje szablony, za pomocą których można określić, w jaki sposób konkretne urządzenie ma otrzymywać powiadomienia. Ta metoda upraszcza wysyłanie powiadomień między platformami.

W tym artykule pokazano, jak korzystać z szablonów do wysyłania w pojedynczym żądaniu — powiadomienia platformy niezależny od, które są przeznaczone dla wszystkich platform. Aby uzyskać bardziej szczegółowe informacje na temat szablonów, zobacz [Omówienie usługi Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone projekty 8,1 i wcześniejsze nie są obsługiwane w programie Visual Studio 2017. Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> W przypadku Notification Hubs urządzenie może zarejestrować wiele szablonów z tym samym tagiem. W takim przypadku komunikat przychodzący, który jest przeznaczony dla tagu, skutkuje wieloma powiadomieniami dostarczonymi do urządzenia, po jednym dla każdego szablonu. Ten proces umożliwia wyświetlenie tego samego komunikatu w wielu powiadomieniach wizualnych, takich jak identyfikator i wyskakujące powiadomienie w aplikacji ze sklepu Windows.

## <a name="send-cross-platform-notifications-using-templates"></a>Wysyłanie powiadomień na wielu platformach przy użyciu szablonów

Aby wysyłać powiadomienia na wielu platformach za pomocą szablonów, wykonaj następujące czynności:

1. W Eksplorator rozwiązań w programie Visual Studio rozwiń folder **controllers** , a następnie otwórz plik RegisterController.cs.

2. Znajdź blok kodu w `Put` metodzie, która tworzy nową rejestrację, a następnie `switch` Zastąp zawartość następującym kodem:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Ten kod wywołuje metodę specyficzną dla platformy w celu utworzenia rejestracji szablonu zamiast natywnej rejestracji. Ponieważ rejestracje szablonów pochodzą z natywnych rejestracji, nie trzeba modyfikować istniejących rejestracji.

3. W kontrolerze Zastąp `sendNotification`metodęnastępującymkodem: `Notifications`

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Ten kod wysyła powiadomienie do wszystkich platform w tym samym czasie, bez konieczności określania natywnego ładunku. Notification Hubs kompiluje i dostarcza prawidłowy ładunek do każdego urządzenia z podaną wartością *tagu* , jak określono w zarejestrowanych szablonach.

4. Opublikuj ponownie projekt zaplecza WebApi.

5. Ponownie uruchom aplikację kliencką, a następnie sprawdź, czy rejestracja zakończyła się pomyślnie.

6. Obowiązkowe Wdróż aplikację kliencką na drugim urządzeniu, a następnie uruchom aplikację.
    Na każdym urządzeniu zostanie wyświetlone powiadomienie.

## <a name="next-steps"></a>Następne kroki

Po ukończeniu tego samouczka więcej informacji na temat Notification Hubs i szablonów można znaleźć w następujących tematach:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Omówienie usługi Azure Notification Hubs][Templates]: Zawiera bardziej szczegółowe informacje na temat szablonów.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Powiadamianie użytkowników za pomocą Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
