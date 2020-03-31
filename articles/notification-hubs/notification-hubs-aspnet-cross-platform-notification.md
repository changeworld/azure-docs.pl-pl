---
title: Wysyłanie powiadomień między platformami do użytkowników za pomocą usługi Azure Notification Hubs (ASP.NET)
description: Dowiedz się, jak używać szablonów Centrów powiadomień do wysyłania w jednym żądaniu powiadomienia niezależnego od platformy, które są przeznaczone dla wszystkich platform.
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
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127042"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Wysyłanie powiadomień między platformami za pomocą usługi Azure Notification Hubs

Ten samouczek opiera się na poprzednim samouczku, [Wysyłanie powiadomień do określonych użytkowników przy użyciu usługi Azure Notification Hubs]. W tym samouczku opisano sposób przekazywania powiadomień do wszystkich urządzeń, które są zarejestrowane dla określonego uwierzytelnionego użytkownika. Takie podejście wymagało wielu żądań, aby wysłać powiadomienie do każdej obsługiwanej platformy klienta. Usługa Azure Notification Hubs obsługuje szablony, za pomocą których można określić, jak określone urządzenie chce otrzymywać powiadomienia. Ta metoda upraszcza wysyłanie powiadomień między platformami.

W tym artykule pokazano, jak korzystać z szablonów, aby wysłać powiadomienie, które jest przeznaczone dla wszystkich platform. W tym artykule użyto pojedynczego żądania do wysłania powiadomienia neutralnego dla platformy. Aby uzyskać bardziej szczegółowe informacje o szablonach, zobacz [Centrum powiadomień — omówienie][Templates].

> [!IMPORTANT]
> Projekty systemu Windows Phone 8.1 i starsze nie są obsługiwane w programie Visual Studio 2019. Aby uzyskać więcej informacji, zobacz [Visual Studio 2019 Kierowanie na platformy i zgodność](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Dzięki Centrum powiadomień urządzenie może rejestrować wiele szablonów przy użyciu tego samego tagu. W takim przypadku przychodząca wiadomość, która jest przeznaczona dla tagu, powoduje dostarczenie wielu powiadomień do urządzenia, po jednym dla każdego szablonu. Ten proces umożliwia wyświetlanie tego samego komunikatu w wielu powiadomieniach wizualnych, takich jak zarówno jako plakietka, jak i jako wyskakujące powiadomienie w aplikacji ze Sklepu Windows.

## <a name="send-cross-platform-notifications-using-templates"></a>Wysyłanie powiadomień między platformami przy użyciu szablonów

W tej sekcji użyto przykładowego kodu utworzonego w [wiadomościach Wysyłaj powiadomienia do określonych użytkowników przy użyciu] samouczka Usługi Azure Notification Hubs. Przykład można pobrać z witryny [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Aby wysyłać powiadomienia między platformami przy użyciu szablonów, wykonaj następujące czynności:

1. W programie Visual Studio w **Eksploratorze rozwiązań**rozwiń folder **Kontrolery,** a następnie otwórz plik *RegisterController.cs.*

1. Zlokalizuj blok `Put` kodu w metodzie, która `switch` tworzy nową rejestrację, a następnie zastąp zawartość następującym kodem:

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

    Ten kod wywołuje metodę specyficzne dla platformy, aby utworzyć rejestrację szablonu zamiast rejestracji macierzystej. Ponieważ rejestracje szablonów pochodzą z rejestracji natywnych, nie trzeba modyfikować istniejących rejestracji.

1. W **Eksploratorze rozwiązań**w folderze **Kontrolery** otwórz plik *NotificationsController.cs.* Zastąp metodę `Post` poniższym kodem:

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

    Ten kod wysyła powiadomienie do wszystkich platform w tym samym czasie. Nie określasz ładunku macierzystego. Centra powiadomień tworzy i dostarcza poprawny ładunek do każdego urządzenia z podanej wartości tagu, jak określono w zarejestrowanych szablonów.

1. Ponownie opublikuj swój projekt interfejsu API sieci Web.

1. Uruchom aplikację kliencką ponownie, aby sprawdzić, czy rejestracja powiodła się.

1. Opcjonalnie należy wdrożyć aplikację kliencką na drugim urządzeniu, a następnie uruchomić aplikację. Powiadomienie jest wyświetlane na każdym urządzeniu.

## <a name="next-steps"></a>Następne kroki

Teraz, po wykonaniu tego samouczka, dowiedz się więcej o Centrach powiadomień i szablonach w tych artykułach:

* Aby uzyskać inny scenariusz korzystania z szablonów, zobacz [powiadomienia wypychane do określonych urządzeń z systemem Windows z aplikacjami uniwersalnej platformy systemu Windows][Use Notification Hubs to send breaking news] samouczek.
* Aby uzyskać bardziej szczegółowe informacje na temat szablonów, zobacz [Centrum powiadomień — omówienie][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[wysyłanie powiadomień do konkretnych użytkowników przy użyciu usługi Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
