---
title: Azure Notification Hubs bezpiecznego Push
description: Dowiedz się, jak wysyłać powiadomienia wypychane bezpiecznej platformie Azure. Przykłady kodu napisane w języku C# z użyciem interfejsu API programu .NET.
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 8d051107a5e114ed8aa5f4b5a629a439519157b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777648"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs bezpiecznego Push
> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Przegląd
Obsługa powiadomień wypychanych w Microsoft Azure pozwala uzyskiwać dostęp do infrastruktury wypychania łatwy w użyciu, wieloplatformową skalowalnych w poziomie, co znacznie upraszcza implementacji powiadomienia wypychane dla aplikacji zarówno konsumenckie i korporacyjne dla platform urządzeń przenośnych.

Z powodu przepisami ograniczeń dotyczących zabezpieczeń, czasami aplikacji może mają zostać uwzględnione coś w powiadomienie, które nie są przesyłane za pośrednictwem infrastruktury powiadomień wypychanych standardowa. Ten przewodnik opisuje sposób do osiągnięcia w tym samym środowisku, wysyłając informacje poufne za pośrednictwem bezpiecznego uwierzytelnionego połączenia od urządzeń klienckich i zaplecza aplikacji.

Na wysokim poziomie przepływ wygląda następująco:

1. Zaplecza aplikacji:
   * Magazyny bezpiecznego ładunku w wewnętrznej bazie danych.
   * Wysyła identyfikator tego powiadomienia do urządzenia (nie informacji o są wysyłane).
2. Aplikacją na urządzeniu, podczas odbierania powiadomienia:
   * Urządzenie kontaktuje się z zaplecza żąda bezpiecznego ładunku.
   * Aplikację można wyświetlić ładunku jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływu (i w tym samouczku) przyjęto założenie, że urządzenia są przechowywane token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Gwarantuje to całkowicie nie zakłóca pracy, jak urządzenia mogą pobierać ładunku bezpiecznego powiadomienia za pomocą tego tokenu. Jeśli aplikacja nie przechowuje tokeny uwierzytelniania na urządzeniu lub tokeny te mogą wygasnąć, aplikacji urządzenia, po otrzymaniu powiadomienia powinien być wyświetlany ogólny powiadomienie monitowania użytkownika do uruchomienia aplikacji. Następnie aplikacja uwierzytelnia użytkownika i zawiera ładunek powiadomienia.

W tym samouczku Secure wypychania pokazano, jak bezpiecznie wysyłać powiadomienia wypychane. Samouczek opiera się na [Powiadom użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) samouczku, dlatego należy wykonać kroki tego samouczka najpierw.

> [!NOTE]
> Ten samouczek zakłada, że utworzony i skonfigurowany Centrum powiadomień, zgodnie z opisem w [wprowadzenie do korzystania z usługi Notification Hubs (w Sklepie Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Należy również zauważyć, że program Windows Phone 8.1 wymaga poświadczeń systemu Windows (nie Windows Phone) i czy zadania w tle nie działają na Windows Phone 8.0 lub Silverlight 8.1. Dla aplikacji ze Sklepu Windows można otrzymywać powiadomienia za pośrednictwem zadania w tle, tylko wtedy, gdy aplikacja jest włączona na ekranie blokady (kliknij pole wyboru w Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modyfikowanie projektu Windows Phone
1. W **NotifyUserWindowsPhone** projekt, Dodaj następujący kod do pliku App.xaml.cs zarejestrować zadania w tle wypychania. Dodaj poniższy wiersz kodu na końcu metody `OnLaunched()`:
   
        RegisterBackgroundTask();
2. W pliku App.xaml.cs Dodaj następujący kod bezpośrednio po `OnLaunched()` metody:
   
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
3. Dodaj następujące `using` instrukcje w górnej części pliku App.xaml.cs:
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. W menu **Plik** programu Visual Studio kliknij polecenie **Zapisz wszystko**.

## <a name="create-the-push-background-component"></a>Tworzenie składnika tła wypychania
Następnym krokiem jest utworzyć składnika tła wypychania.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł najwyższego poziomu rozwiązania (**SecurePush rozwiązania** w tym przypadku), następnie kliknij przycisk **Dodaj**, następnie kliknij przycisk **nowy projekt**.
2. Rozwiń węzeł **aplikacji ze sklepu**, następnie kliknij przycisk **aplikacji Windows Phone**, następnie kliknij przycisk **składnika środowiska wykonawczego systemu Windows (Windows Phone)**. Nazwij projekt **PushBackgroundComponent**, a następnie kliknij przycisk **OK** Aby utworzyć projekt.
   
    ![][12]
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **PushBackgroundComponent (Windows Phone 8.1)** projektu, a następnie kliknij przycisk **Dodaj**, następnie kliknij przycisk **klasy**. Nazwa nowej klasy **PushBackgroundTask.cs**. Kliknij przycisk **Dodaj** do generowania klasy.
4. Zastąp całą zawartość **PushBackgroundComponent** definicję przestrzeni nazw następującym kodem, zastępując symbol zastępczy `{back-end endpoint}` z punktem końcowym zaplecza uzyskane podczas wdrażania sieci wewnętrznej:
   
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
5. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **PushBackgroundComponent (Windows Phone 8.1)** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
6. Po lewej stronie kliknij **Online**.
7. W **wyszukiwania** wpisz **klienta Http**.
8. Na liście wyników kliknij **bibliotek klienta HTTP Microsoft**, a następnie kliknij przycisk **zainstalować**. Ukończ instalację.
9. W polu **wyszukiwania** wpisz **Json.net**. Zainstaluj **Json.NET** pakietu, a następnie zamknij okno Menedżera pakietów NuGet.
10. Dodaj następujące `using` instrukcje w górnej części **PushBackgroundTask.cs** pliku:
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. W Eksploratorze rozwiązań w **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu, kliknij prawym przyciskiem myszy **odwołania**, następnie kliknij przycisk **Dodawanie odwołania...** . W oknie dialogowym Menedżer odwołania, zaznacz pole wyboru obok pozycji **PushBackgroundComponent**, a następnie kliknij przycisk **OK**.
12. W Eksploratorze rozwiązań kliknij dwukrotnie **Package.appxmanifest** w **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu. W obszarze **powiadomienia**ustaw **wyskakujące funkcją** do **tak**.
    
    ![][3]
13. Nadal **Package.appxmanifest**, kliknij przycisk **deklaracje** menu u góry. W **dostępne deklaracje** listy rozwijanej, kliknij przycisk **zadania w tle**, a następnie kliknij przycisk **Dodaj**.
14. W **Package.appxmanifest**w obszarze **właściwości**, sprawdź **powiadomienie wypychane**.
15. W **Package.appxmanifest**w obszarze **ustawień aplikacji**, typ **PushBackgroundComponent.PushBackgroundTask** w **punktu wejścia** pola.
    
    ![][13]
16. W menu **Plik** kliknij polecenie **Zapisz wszystko**.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie Visual Studio, uruchom **AppBackend** aplikacji interfejsu API sieci Web. Zostanie wyświetlona strona sieci web ASP.NET.
2. W programie Visual Studio, uruchom **NotifyUserWindowsPhone (Windows Phone 8.1)** aplikacji Windows Phone. Windows Phone emulator działa i automatycznie załadowanie aplikacji.
3. W **NotifyUserWindowsPhone** aplikacji interfejsu użytkownika, wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą one mieć taką samą wartość.
4. W **NotifyUserWindowsPhone** aplikacji interfejsu użytkownika, kliknij przycisk **zalogować się i Zarejestruj**. Następnie kliknij przycisk **wysyłania wypychania**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
