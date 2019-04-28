---
title: Azure Notification Hubs bezpieczne wypychanie
description: Dowiedz się, jak wysyłać powiadomienia wypychane bezpiecznego na platformie Azure. Przykłady kodu napisane w języku C# z użyciem interfejsu API programu .NET.
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: cf23ef5df3bdcaad23841da111fa06cc36b4cd57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459244"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Bezpiecznie powiadomienia wypychane z usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

Obsługę powiadomień wypychanych w systemie Microsoft Azure umożliwia dostęp do infrastruktury wypychania łatwy w użyciu, wieloplatformową i skalowanych w poziomie, który znacznie upraszcza implementację powiadomienia wypychane dla aplikacji firmowych i komercyjnych dla urządzeń przenośnych platform.

Ze względu na przepisami ograniczenia zabezpieczeń, czasami aplikacja może mają zostać uwzględnione coś powiadomienia, które nie mogą być przekazywane za pośrednictwem infrastruktury powiadomień wypychanych standardowych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska poprzez wysłanie informacji poufnych przez bezpieczne, uwierzytelnione połączenie między urządzeniem klienckim i zapleczem aplikacji.

Na wysokim poziomie przepływ jest następująca:

1. Zaplecze aplikacji:
   * Ładunek bezpiecznego magazynów w wewnętrznej bazie danych.
   * Wysyła identyfikator to powiadomienie do urządzenia (wysyłane nie informacje zabezpieczeń).
2. Aplikacja na urządzeniu, podczas odbierania powiadomienia:
   * Urządzenie kontaktuje się z zaplecza bezpiecznego ładunku żądania.
   * Aplikację można wyświetlić ładunku powiadomienie na urządzenie.

Należy zauważyć, że w poprzedniej przepływu (i w ramach tego samouczka) przyjęto założenie, że urządzenia są przechowywane token uwierzytelniania w magazynie lokalnym, po użytkownik loguje. Gwarantuje to całkowicie bezproblemowe, jako urządzenia mogą pobierać ładunku bezpieczne powiadomień, używające tego tokenu. Jeśli aplikacja nie przechowuje tokeny uwierzytelniania na urządzeniu lub tokeny te mogą wygasnąć, aplikacji urządzenia, po otrzymaniu zawiadomienia powinien być wyświetlany ogólny powiadomienie monitowania użytkownika do uruchomienia aplikacji. Aplikacja następnie uwierzytelnia użytkownika i zawiera ładunek powiadomienia.

W tym samouczku Secure wypychania pokazuje, jak bezpiecznie wysyłać powiadomienia wypychane. Samouczek opiera się na [powiadamianie użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) samouczek, dlatego należy wykonać kroki opisane w tym samouczku najpierw.

> [!NOTE]
> W tym samouczku przyjęto założenie, że utworzone i skonfigurowane Centrum powiadomień, zgodnie z opisem w [wprowadzenie do usługi Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Należy również zauważyć, że Windows Phone 8.1 wymaga poświadczeń Windows (nie Windows Phone) i że zadania w tle nie działają w systemie Windows Phone 8.0 lub Silverlight 8.1. Dla aplikacji Windows Store, można otrzymywać powiadomienia za pośrednictwem zadania w tle, tylko wtedy, gdy aplikacja jest włączona na ekranie blokady (kliknij pole wyboru w Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modyfikowanie projektu Windows Phone

1. W **NotifyUserWindowsPhone** projektu, Dodaj następujący kod do pliku App.xaml.cs zarejestrować wypychania zadanie w tle. Dodaj poniższy wiersz kodu na końcu metody `OnLaunched()`:

    ```c#
    RegisterBackgroundTask();
    ```
2. W pliku App.xaml.cs Dodaj następujący kod bezpośrednio po `OnLaunched()` metody:

    ```c#
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. Dodaj następujący kod `using` instrukcji w górnej części pliku App.xaml.cs:

    ```c#
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. W menu **Plik** programu Visual Studio kliknij polecenie **Zapisz wszystko**.

## <a name="create-the-push-background-component"></a>Tworzenie składnika tła wypychania

Następnym krokiem jest utworzyć składnika tła wypychania.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł najwyższego poziomu rozwiązania (**SecurePush rozwiązania** w tym przypadku), następnie kliknij przycisk **Dodaj**, następnie kliknij przycisk **nowy projekt**.
2. Rozwiń **Store Apps**, następnie kliknij przycisk **aplikacji Windows Phone**, następnie kliknij przycisk **składnika środowiska wykonawczego Windows (Windows Phone)**. Nadaj projektowi nazwę **PushBackgroundComponent**, a następnie kliknij przycisk **OK** do tworzenia projektu.

    ![][12]
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **PushBackgroundComponent (Windows Phone 8.1)** projektu, a następnie kliknij przycisk **Dodaj**, następnie kliknij przycisk **klasy**. Nadaj nowej klasie `PushBackgroundTask.cs`. Kliknij przycisk **Dodaj** aby wygenerować klasę.
4. Zastąp całą zawartość `PushBackgroundComponent` definicję przestrzeni nazw następującym kodem, zastępując symbol zastępczy `{back-end endpoint}` z punktem końcowym zaplecza uzyskanego podczas wdrażania usługi zaplecza:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **PushBackgroundComponent (Windows Phone 8.1)** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
6. Po lewej stronie kliknij opcję **Online**.
7. W polu **Wyszukaj** wpisz ciąg **Klient HTTP**.
8. Na liście wyników kliknij **biblioteki klienta HTTP Microsoft**, a następnie kliknij przycisk **zainstalować**. Ukończ instalację.
9. W polu **Wyszukaj** w menedżerze pakietów NuGet wpisz ciąg **Json.net**. Zainstaluj **Json.NET** pakietu, a następnie zamknij okno Menedżera pakietów NuGet.
10. Dodaj następujący kod `using` instrukcji na górze `PushBackgroundTask.cs` pliku:

    ```c#
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. W Eksploratorze rozwiązań w **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu, kliknij prawym przyciskiem myszy **odwołania**, następnie kliknij przycisk **Dodaj odwołanie...** . W oknie dialogowym Reference Manager, zaznacz pole wyboru obok pozycji **PushBackgroundComponent**, a następnie kliknij przycisk **OK**.
12. W Eksploratorze rozwiązań kliknij dwukrotnie **Package.appxmanifest** w **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu. W obszarze **powiadomienia**ustaw **wyskakujące możliwością** do **tak**.

    ![][3]
13. Nadal w **Package.appxmanifest**, kliknij przycisk **deklaracje** menu u góry. W **dostępne deklaracje** listy rozwijanej kliknij **zadania w tle**, a następnie kliknij przycisk **Dodaj**.
14. W **Package.appxmanifest**w obszarze **właściwości**, sprawdź **powiadomienie wypychane**.
15. W **Package.appxmanifest**w obszarze **ustawienia aplikacji**, typ **PushBackgroundComponent.PushBackgroundTask** w **punktu wejścia** pole.

    ![][13]
16. W menu **Plik** kliknij polecenie **Zapisz wszystko**.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie Visual Studio, uruchom **AppBackend** aplikacji internetowego interfejsu API. Zostanie wyświetlona strona sieci web platformy ASP.NET.
2. W programie Visual Studio, uruchom **NotifyUserWindowsPhone (Windows Phone 8.1)** aplikacji Windows Phone. Emulator Windows Phone działa, a aplikacja jest ładowana automatycznie.
3. W **NotifyUserWindowsPhone** aplikacji interfejsu użytkownika, wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą one mieć taką samą wartość.
4. W **NotifyUserWindowsPhone** aplikacji interfejsu użytkownika, kliknij przycisk **Zaloguj się i rejestrowanie**. Następnie kliknij przycisk **wysyłania wypychanych**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
