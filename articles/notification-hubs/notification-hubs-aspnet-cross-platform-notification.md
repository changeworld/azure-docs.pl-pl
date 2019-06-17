---
title: Wysyłanie powiadomień dla wielu platform do użytkowników za pomocą centrów powiadomień platformy Azure (ASP.NET)
description: Dowiedz się, jak za pomocą szablonów usługi Notification Hubs wysyłać w pojedynczym żądaniu powiadomienia niezależne od platformy, który jest przeznaczony dla wszystkich platform.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 0f92b49c9d77029a9624782b49eb23f7083c49aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60872259"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Wysyłanie powiadomień dla wielu platform do użytkowników z usługą Notification Hubs

W poprzednim samouczku [powiadamianie użytkowników za pomocą usługi Notification Hubs], wiesz, jak wypychać powiadomienia do wszystkich urządzeń, które są zarejestrowane dla określonego użytkownika uwierzytelnionego. W tym samouczku musieli wysyłania powiadomień do poszczególnych obsługiwana platforma kliencka wielu żądań. Usługa Azure Notification Hubs obsługuje szablony, za pomocą których można określić, jak chce otrzymywać powiadomienia o określonym urządzeniem. Ta metoda ułatwia wysyłanie powiadomień na różne platformy.

W tym artykule pokazano, jak korzystać z szablonów, aby wysłać pojedyncze żądanie powiadomienie niezależne od platformy, który jest przeznaczony dla wszystkich platform. Aby uzyskać szczegółowe informacje dotyczące szablonów, zobacz [Azure Notification Hubs — omówienie][Templates].

> [!IMPORTANT]
> Projekty Windows Phone 8.1 i starsze nie są obsługiwane w programie Visual Studio 2017. Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Usługa Notification Hubs urządzenia można zarejestrować wiele szablonów przy użyciu tego samego tagu. W tym przypadku wiadomości przychodzących, który jest przeznaczony dla wyniki tagów w powiadomieniach wielu dostarczane na urządzeniu, jeden dla każdego szablonu. Ten proces umożliwia wyświetlanie ten sam komunikat w wiele powiadomień wizualne, takie jak zarówno jako wskaźnik i wyskakujące powiadomienie w aplikacji Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Wysyłanie powiadomień dla wielu platform przy użyciu szablonów

Aby wysyłać powiadomienia dla wielu platform przy użyciu szablonów, należy wykonać następujące czynności:

1. W Eksploratorze rozwiązań w programie Visual Studio rozwiń **kontrolerów** folder, a następnie otwórz plik w pliku RegisterController.cs.

2. Znajdź blok kodu w `Put` metodę, która tworzy nową rejestrację, a następnie zastąp `switch` zawartość następującym kodem:

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

    Ten kod wywołuje metodę specyficzne dla platformy do utworzenia rejestracji szablonów, zamiast rejestracji macierzystego. Ponieważ rejestracja szablonu pochodzi z natywnych rejestracji, nie trzeba modyfikować istniejące rejestracje.

3. W `Notifications` kontrolera, Zastąp `sendNotification` metoda następującym kodem:

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

    Ten kod wysyła powiadomienie do wszystkich platform, w tym samym czasie, bez konieczności Określ ładunek natywnych. Powiadomienie koncentratory kompilacje i zapewnia prawidłowe ładunku dla każdego urządzenia z dostępnego *tag* wartości, jak określono w szablonach zarejestrowane.

4. Ponownie opublikować projekt zaplecza interfejsu WebApi.

5. Ponownie uruchom aplikację klienta, a następnie sprawdź, czy rejestracja zakończyła się pomyślnie.

6. (Opcjonalnie) Wdrażanie aplikacji klienta do drugiego urządzenia, a następnie uruchom aplikację.
    Na każdym urządzeniu, zostanie wyświetlone powiadomienie.

## <a name="next-steps"></a>Kolejne kroki

Teraz, że zostały wykonane w tym samouczku, Dowiedz się więcej o usłudze Notification Hubs i szablonów w tych tematach:

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
[Powiadamianie użytkowników za pomocą usługi Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
