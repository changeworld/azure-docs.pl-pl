---
title: Architektura wypychania centrum powiadomień
description: Dowiedz się więcej o korzystaniu z usługi Azure Notification Hubs w środowisku przedsiębiorstwa
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264037"
---
# <a name="enterprise-push-architectural-guidance"></a>Wskazówki dotyczące architektury powiadomień wypychanych w przedsiębiorstwie

Przedsiębiorstwa obecnie stopniowo dążą do tworzenia aplikacji mobilnych dla swoich użytkowników końcowych (zewnętrznych) lub dla pracowników (wewnętrznych). Mają istniejące systemy wewnętrznej bazy danych w miejscu, czy to mainframe lub niektórych aplikacji LoB, które muszą być zintegrowane z architekturą aplikacji mobilnych. W tym przewodniku o tym, jak najlepiej wykonać tę integrację zalecając możliwe rozwiązanie typowych scenariuszy.

Częstym wymaganiem jest wysyłanie powiadomień wypychanych do użytkowników za pośrednictwem aplikacji mobilnej, gdy wystąpi zdarzenie zainteresowania w systemach wewnętrznej bazy danych. Na przykład klient banku, który ma aplikację bankową banku na telefonie iPhone, chce otrzymywać powiadomienia o tym, że obciążenie jest dokonywane powyżej określonej kwoty z konta lub scenariusza intranetowego, w którym pracownik działu finansowego, który ma aplikację zatwierdzająca budżet na telefonie Windows Phone, chce zostać powiadomiony po otrzymaniu wniosku o zatwierdzenie.

Konto bankowe lub przetwarzanie zatwierdzenia prawdopodobnie zostanie wykonane w jakimś systemie wewnętrznej bazy danych, który musi zainicjować wypychanie do użytkownika. Może istnieć wiele takich systemów wewnętrznej bazy danych, które muszą wszystkie budować tego samego rodzaju logiki wypychania, gdy zdarzenie wyzwala powiadomienie. Złożoność polega na integracji kilku systemów zaplecza z jednym systemem wypychania, w którym użytkownicy końcowi mogli subskrybować różne powiadomienia, a nawet może istnieć wiele aplikacji mobilnych. Na przykład intranetowe aplikacje mobilne, w których jedna aplikacja mobilna może chcieć otrzymywać powiadomienia z wielu takich systemów zaplecza. Systemy wewnętrznej bazy danych nie wiedzą lub muszą wiedzieć o semantyki push / technologii, więc wspólne rozwiązanie tutaj tradycyjnie było wprowadzenie składnika, który sonduje systemy wewnętrznej bazy danych dla wszelkich zdarzeń zainteresowania i jest odpowiedzialny za wysyłanie wiadomości push do klienta.

Lepszym rozwiązaniem jest użycie usługi Azure Service Bus — model tematu/subskrypcji, co zmniejsza złożoność podczas tworzenia rozwiązania skalowalnego.

Oto ogólna architektura rozwiązania (uogólniona z wieloma aplikacjami mobilnymi, ale równie stosowana, gdy istnieje tylko jedna aplikacja mobilna)

## <a name="architecture"></a>Architektura

![][1]

Kluczowym elementem tego diagramu architektonicznego jest usługa Azure Service Bus, która udostępnia model programowania tematów/subskrypcji (więcej na ten temat w [programie Service Bus Pub/Sub).] Odbiornik, który w tym przypadku jest zaplecza mobilnego (zazwyczaj [usługi Azure Mobile Service], który inicjuje wypychanie do aplikacji mobilnych) nie odbiera wiadomości bezpośrednio z systemów wewnętrznej bazy danych, ale zamiast tego warstwy abstrakcji pośredniej dostarczonych przez usługę Azure Service [Bus], który umożliwia mobilnych zaplecza do odbierania wiadomości z jednego lub więcej systemów zaplecza. Temat usługi Service Bus musi zostać utworzony dla każdego z systemów wewnętrznej bazy danych, na przykład Konto, HR, Finanse, który jest w zasadzie "tematy" zainteresowania, który inicjuje wiadomości, które mają być wysyłane jako powiadomienie wypychania. Systemy wewnętrznej bazy danych wysyłają wiadomości do tych tematów. Zaplecze mobilne może subskrybować jeden lub więcej takich tematów, tworząc subskrypcję usługi Service Bus. Uprawnia on akces do mobilnego zaplecza do otrzymywania powiadomień z odpowiedniego systemu zaplecza. Zaplecze mobilne nadal nasłuchuje wiadomości w swoich subskrypcjach i jak tylko nadejdzie wiadomość, odwraca się i wysyła ją jako powiadomienie do centrum powiadomień. Centra powiadomień ostatecznie dostarczają wiadomość do aplikacji mobilnej. Oto lista kluczowych składników:

1. Systemy zaplecza (systemy LoB/Legacy)
   * Tworzy temat magistrali usług
   * Wysyła wiadomość
1. Zaplecze mobilne
   * Tworzy subskrypcję usługi
   * Odbiera wiadomość (z systemu wewnętrznej bazy danych)
   * Wysyła powiadomienia do klientów (za pośrednictwem usługi Azure Notification Hub)
1. Aplikacja mobilna
   * Odbiera i wyświetla powiadomienia

### <a name="benefits"></a>Korzyści

1. Oddzielenie między odbiornikiem (aplikacja mobilna/usługa za pośrednictwem Centrum powiadomień) a operatorem (systemy wewnętrznej bazy danych) umożliwia zintegrowanie dodatkowych systemów wewnętrznej bazy danych przy minimalnych zmianach.
1. To również sprawia, że scenariusz wielu aplikacji mobilnych jest w stanie odbierać zdarzenia z jednego lub więcej systemów wewnętrznej bazy danych.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Wymagania wstępne

Wykonaj następujące samouczki, aby zapoznać się z pojęciami, a także typowe kroki tworzenia & konfiguracji:

1. [Programowanie pub/sub usługi Service Bus] — w tym samouczku wyjaśniono szczegóły pracy z tematami/subskrypcjami usługi Service Bus, jak utworzyć obszar nazw zawierający tematy/subskrypcje, jak wysyłać & odbierać od nich wiadomości.
2. [Centra powiadomień — samouczek uniwersalny systemu Windows] — w tym samouczku wyjaśniono, jak skonfigurować aplikację ze Sklepu Windows i używać centrów powiadomień do rejestrowania, a następnie odbierania powiadomień.

### <a name="sample-code"></a>Przykładowy kod

Pełny przykładowy kod jest dostępny w [centrum powiadomień Samples]. Jest podzielony na trzy składniki:

1. **EnterprisePushBackendSystem**

    a. Ten projekt używa pakietu **WindowsAzure.ServiceBus** NuGet i jest oparty na [programie Service Bus Pub/Sub].

    b. Ta aplikacja jest prosta aplikacja konsoli Języka C# do symulacji systemu LoB, który inicjuje komunikat, który ma być dostarczany do aplikacji mobilnej.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    d. `CreateTopic`służy do tworzenia tematu usługi Service Bus.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage`służy do wysyłania wiadomości do tego tematu usługi Service Bus. Ten kod po prostu wysyła zestaw losowych wiadomości do tematu okresowo na potrzeby przykładu. Zwykle istnieje system wewnętrznej bazy danych, który wysyła komunikaty, gdy wystąpi zdarzenie.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification (Odbiór i potwierdzanie)**

    a. W tym projekcie używane są pakiety *WindowsAzure.ServiceBus* i **Microsoft.Web.WebJobs.Publish** NuGet i są oparte na [programowaniu Pub/Sub usługi Service Bus].

    b. Następująca aplikacja konsoli działa jako [azure webjob,] ponieważ musi działać w sposób ciągły, aby nasłuchiwać wiadomości z systemów LoB/backend. Ta aplikacja jest częścią zaplecza mobile.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    d. `CreateSubscription`służy do tworzenia subskrypcji usługi Service Bus dla tematu, w którym system wewnętrznej bazy danych wysyła wiadomości. W zależności od scenariusza biznesowego ten składnik tworzy jedną lub więcej subskrypcji do odpowiednich tematów (na przykład niektóre mogą odbierać wiadomości z systemu HR, niektóre z systemu finance i tak dalej)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`służy do odczytywania wiadomości z tematu przy użyciu jego subskrypcji i jeśli odczyt zakończy się pomyślnie, a następnie spreparować powiadomienie (w przykładowym scenariuszu natywnego powiadomienia wyskakującego systemu Windows), które mają zostać wysłane do aplikacji mobilnej przy użyciu usługi Azure Notification Hubs.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Aby opublikować tę aplikację jako **WebJob**, kliknij prawym przyciskiem myszy rozwiązanie w programie Visual Studio i wybierz pozycję **Publikuj jako WebJob**

    ![][2]

    f. Wybierz swój profil publikowania i utwórz nową witrynę sieci Web platformy Azure, jeśli jeszcze nie istnieje, która obsługuje tę usługę WebJob, a gdy masz witrynę WebSite, a następnie **publikuj**.

    ![][3]

    g. Skonfiguruj zadanie jako "Uruchom w sposób ciągły", tak aby po zalogowaniu się do [witryny Azure portal] powinien być widoczny w następujący sposób:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Ta aplikacja jest aplikacją Ze Sklepu Windows, która odbiera wyskakujące powiadomienia z webjob uruchomiony jako część zaplecza mobile i wyświetlić go. Ten kod jest oparty na [Centrach powiadomień - Windows Universal tutorial].  

    b. Upewnij się, że aplikacja jest włączona do odbierania wyskakujących powiadomień.

    d. Upewnij się, że następujący kod rejestracji Centrum powiadomień jest wywoływany podczas uruchamiania aplikacji (po wymianie `HubName` wartości i: `DefaultListenSharedAccessSignature`

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

1. Upewnij się, że twój webjob działa pomyślnie i zaplanowane do uruchomienia w sposób ciągły.
2. Uruchom **aplikację EnterprisePushMobileApp,** która uruchamia aplikację ze Sklepu Windows.
3. Uruchom aplikację konsoli **EnterprisePushBackendSystem,** która symuluje zaplecze LoB i rozpoczyna wysyłanie wiadomości, a powiadomienia wyskakujące powinny być wyświetlane w następujący sposób:

    ![][5]

4. Wiadomości zostały pierwotnie wysłane do tematów usługi Service Bus, który był monitorowany przez subskrypcje usługi Service Bus w zadaniu sieci Web. Po odebraniu wiadomości zostanie utworzone powiadomienie i wysłane do aplikacji mobilnej. Można przeglądać dzienniki WebJob, aby potwierdzić przetwarzanie po przejściu do łącza Dzienniki w [witrynie Azure portal] dla zadania sieci Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Przykłady Centrum powiadomień]: https://github.com/Azure/azure-notificationhubs-samples
[Usługa mobilna platformy Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programowanie usługi Service Bus Pub/Sub]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Centra powiadomień — samouczek systemu Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal Azure]: https://portal.azure.com/
