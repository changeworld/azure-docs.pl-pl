---
title: Wyślij powiadomienia i platform do użytkowników z centra powiadomień Azure (ASP.NET)
description: Dowiedz się, jak używać usługi Notification Hubs szablonów wysłać pojedyncze żądanie powiadomienia niezależny od platformy, przeznaczonego dla wszystkich platform.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 95793aac3c25563e3af39f3c47cebdd06e25e35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777957"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Wyślij powiadomienia i platform do użytkowników z usługą Notification Hubs
W poprzednich samouczek [powiadomić użytkowników z usługą Notification Hubs], przedstawiono sposób powiadomienia wypychane do wszystkich urządzeń, które są zarejestrowane dla określonego użytkownika uwierzytelnionego. W tym samouczku wiele żądań były wymagane do wysyłania powiadomień do każdego obsługiwana platforma kliencka. Centra powiadomień Azure obsługuje szablony, z których można określić sposób chce otrzymywać powiadomienia o określonym urządzeniem. Ta metoda ułatwia wysyłanie powiadomień i platform. 

W tym artykule pokazano, jak korzystać z szablonów, aby wysłać pojedyncze żądanie powiadomienia niezależny od platformy, przeznaczonego dla wszystkich platform. Aby uzyskać szczegółowe informacje o szablonach, zobacz [Przegląd centra powiadomień Azure][Templates].

> [!IMPORTANT]
> Projektów Windows Phone 8.1 i starsze nie są obsługiwane w programie Visual Studio 2017 r. Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Z usługą Notification Hubs urządzenia można zarejestrować wiele szablonów z tym samym tagiem. W takim przypadku przychodzącego komunikatu przeznaczonego dla tagu skutkuje wiele powiadomień są dostarczane na urządzeniu, jeden dla każdego szablonu. Ten proces umożliwia wyświetlanie ten sam komunikat w wielu visual powiadomień, takie jak zarówno jako wskaźnika, a wyskakujące powiadomienie w aplikacji ze Sklepu Windows.
> 
> 

Aby wysyłać powiadomienia wielu platform przy użyciu szablonów, należy wykonać następujące czynności:

1. W Eksploratorze rozwiązań w programie Visual Studio, rozwiń węzeł **kontrolerów** folder, a następnie otwórz plik RegisterController.cs.

2. Znajdź blok kodu w **Put** metodę, która powoduje utworzenie nowej rejestracji, a następnie zastąp `switch` zawartość następującym kodem:
   
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
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Ten kod wywołuje metodę specyficzne dla platformy do utworzenia rejestracji szablonu zamiast rejestracji macierzystego. Ponieważ rejestracje szablonu pochodzi od rejestracji macierzystego, nie musisz zmodyfikować istniejące rejestracje.

3. W **powiadomienia** kontrolera, Zastąp **sendNotification** metodę z następującym kodem:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Ten kod wysyła powiadomienie do wszystkich platform, w tym samym czasie, bez konieczności Określ natywnego ładunku. Powiadomienie koncentratory kompilacje i dostarcza poprawne ładunku do wszystkich urządzeń z dostarczonych *tag* wartość określoną w szablonie w zarejestrowany.

4. Ponownie opublikować WebApi projektu zaplecza.

5. Ponownie uruchom aplikację klienta, a następnie sprawdź, czy rejestracja zakończyła się pomyślnie.

6. (Opcjonalnie) Wdrażanie aplikacji klienta do drugiego urządzenia, a następnie uruchom aplikację.
    Na każdym urządzeniu zostanie wyświetlone powiadomienie.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy została ukończona w tym samouczku, Dowiedz się więcej o usłudze Notification Hubs i szablonów w tych tematach:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Omówienie usługi Azure Notification Hubs][Templates]: zawiera bardziej szczegółowe informacje na temat szablonów.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[powiadomić użytkowników z usługą Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
