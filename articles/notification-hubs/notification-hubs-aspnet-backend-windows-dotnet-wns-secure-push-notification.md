---
title: Usługi Azure Notification Hubs Secure Push dla systemu Windows
description: Dowiedz się, jak wysyłać bezpieczne powiadomienia wypychane na platformie Azure. Przykłady kodu napisane w języku C# z użyciem interfejsu API programu .NET.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530735"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Bezpieczne wypychanie powiadomień z usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

Obsługa powiadomień wypychanych na platformie Microsoft Azure umożliwia dostęp do łatwej w użyciu, wieloplatformowej, skalowej w poziomie infrastruktury push, co znacznie upraszcza wdrażanie powiadomień wypychanych zarówno dla aplikacji konsumenckich, jak i korporacyjnych dla urządzeń przenośnych Platformy.

Ze względu na ograniczenia regulacyjne lub zabezpieczeń czasami aplikacja może chcieć dołączyć coś w powiadomieniu, które nie mogą być przesyłane za pośrednictwem standardowej infrastruktury powiadomień wypychanych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska, wysyłając poufne informacje za pośrednictwem bezpiecznego, uwierzytelnionego połączenia między urządzeniem klienckim a zapleczem aplikacji.

Na wysokim poziomie przepływ jest następujący:

1. Back-end aplikacji:
   * Przechowuje bezpieczne ładunku w bazie danych zaplecza.
   * Wysyła identyfikator tego powiadomienia do urządzenia (nie są wysyłane żadne bezpieczne informacje).
2. Aplikacja na urządzeniu, po otrzymaniu powiadomienia:
   * Urządzenie styka się z zapleczem, żądając bezpiecznego ładunku.
   * Aplikacja może wyświetlić ładunek jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływie (i w tym samouczku) zakładamy, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Gwarantuje to całkowicie bezproblemowe środowisko, ponieważ urządzenie może pobrać bezpieczny ładunek powiadomienia przy użyciu tego tokenu. Jeśli aplikacja nie przechowuje tokenów uwierzytelniania na urządzeniu lub jeśli te tokeny mogą wygasnąć, aplikacja urządzenia po otrzymaniu powiadomienia powinna wyświetlić ogólne powiadomienie z monitem o uruchomienie aplikacji. Następnie aplikacja uwierzytelnia użytkownika i wyświetla ładunek powiadomień.

Ten samouczek Bezpiecznego wypychania pokazuje, jak bezpiecznie wysłać powiadomienie wypychanego. Samouczek opiera się na podziale [Powiadom użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) samouczka, więc należy wykonać kroki w tym samouczku pierwszy.

> [!NOTE]
> W tym samouczku przyjęto założenie, że centrum powiadomień zostało utworzone i skonfigurowane zgodnie z opisem w [artykule Wprowadzenie do centrów powiadomień (Sklep Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Należy również pamiętać, że system Windows Phone 8.1 wymaga poświadczeń systemu Windows (nie windows phone) i że zadania w tle nie działają w systemie Windows Phone 8.0 lub Silverlight 8.1. W przypadku aplikacji ze Sklepu Windows powiadomienia można otrzymywać za pomocą zadania w tle tylko wtedy, gdy aplikacja jest włączona na ekranie blokady (kliknij to pole wyboru w Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modyfikowanie projektu systemu Windows Phone

1. W projekcie **NotifyUserWindowsPhone** dodaj następujący kod, aby App.xaml.cs, aby zarejestrować zadanie w tle wypychania. Dodaj poniższy wiersz kodu na końcu metody `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Nadal w App.xaml.cs, dodać następujący kod natychmiast `OnLaunched()` po metodzie:

    ```csharp
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
3. Dodaj następujące `using` instrukcje u góry pliku App.xaml.cs:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. W menu **Plik** programu Visual Studio kliknij polecenie **Zapisz wszystko**.

## <a name="create-the-push-background-component"></a>Tworzenie komponentu Wypychanie tła

Następnym krokiem jest utworzenie komponentu tło wypychania.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł najwyższego poziomu rozwiązania **(Rozwiązanie SecurePush** w tym przypadku), a następnie kliknij polecenie **Dodaj**, a następnie kliknij polecenie **Nowy projekt**.
2. Rozwiń pozycję **Aplikacje ze Sklepu**, a następnie kliknij pozycję **Aplikacje systemu Windows Phone**, a następnie kliknij pozycję Składnik środowiska wykonawczego systemu Windows **(Windows Phone)**. Nazwij projekt **PushBackgroundComponent**, a następnie kliknij przycisk **OK,** aby utworzyć projekt.

    ![][12]
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **PushBackgroundComponent (Windows Phone 8.1),** a następnie kliknij polecenie **Dodaj**, a następnie kliknij polecenie **Klasa**. Nazwij `PushBackgroundTask.cs`nową klasę . Kliknij **przycisk Dodaj,** aby wygenerować klasę.
4. Zastąp całą `PushBackgroundComponent` zawartość definicji obszaru nazw następującym kodem, zastępując symbol zastępczy `{back-end endpoint}` punktem końcowym zaplecza uzyskanym podczas wdrażania zaplecza:

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
5. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **PushBackgroundComponent (Windows Phone 8.1),** a następnie kliknij polecenie **Zarządzaj pakietami NuGet**.
6. Po lewej stronie kliknij opcję **Online**.
7. W polu **Wyszukaj** wpisz ciąg **Klient HTTP**.
8. Na liście wyników kliknij pozycję **Biblioteki klientów HTTP firmy Microsoft,** a następnie kliknij pozycję **Zainstaluj**. Ukończ instalację.
9. W polu **Wyszukaj** w menedżerze pakietów NuGet wpisz ciąg **Json.net**. Zainstaluj pakiet **Json.NET,** a następnie zamknij okno Menedżera pakietów NuGet.
10. W górnej `using` części pliku dodaj `PushBackgroundTask.cs` następujące instrukcje:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. W Eksploratorze rozwiązań w projekcie **NotifyUserWindowsPhone (Windows Phone 8.1)** kliknij prawym przyciskiem myszy **pozycję Odwołania**, a następnie kliknij polecenie **Dodaj odwołanie...**. W oknie dialogowym Menedżer odwołań zaznacz pole obok pozycji **PushBackgroundComponent**, a następnie kliknij przycisk **OK**.
12. W Eksploratorze rozwiązań kliknij dwukrotnie **pozycję Package.appxmanifest** w projekcie **NotifyUserWindowsPhone (Windows Phone 8.1).** W obszarze Powiadomienia ustaw **wyskakujące wyskakujące** na **Tak**. **Notifications**

    ![][3]
13. Nadal w **Package.appxmanifest**, kliknij menu **Deklaracje** u góry. W menu **rozwijanym Dostępne deklaracje** kliknij pozycję **Zadania w tle**, a następnie kliknij pozycję **Dodaj**.
14. W **Package.appxmanifest**, w obszarze **Właściwości**, zaznacz **powiadomienie push**.
15. W **obszarze Package.appxmanifest**w obszarze **Ustawienia aplikacji**wpisz **PushBackgroundComponent.PushBackgroundTask** w polu **Punkt wejścia.**

    ![][13]
16. W menu **Plik** kliknij polecenie **Zapisz wszystko**.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie Visual Studio uruchom aplikację interfejsu API sieci Web **aplikacji AppBackend.** Zostanie wyświetlona ASP.NET strona internetowa.
2. W programie Visual Studio uruchom aplikację **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone. Emulator systemu Windows Phone uruchamia i ładuje aplikację automatycznie.
3. W interfejsie użytkownika aplikacji **NotifyUserWindowsPhone** wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą być tej samej wartości.
4. W interfejsie użytkownika aplikacji **NotifyUserWindowsPhone** kliknij pozycję **Zaloguj się i zarejestruj**. Następnie kliknij przycisk **Wyślij wypychanie**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
