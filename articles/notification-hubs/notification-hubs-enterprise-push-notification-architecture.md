---
title: Notification Hubs — architektury wypychania przedsiębiorstwa
description: Wskazówki na temat korzystania z usługi Azure Notification Hubs w środowisku przedsiębiorstwa
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 938801148b175456553865b54d59271021811401
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60873396"
---
# <a name="enterprise-push-architectural-guidance"></a>Wskazówki dotyczące architektury powiadomień wypychanych w przedsiębiorstwie

Już dziś przedsiębiorstwa są stopniowo przenoszenie do tworzenia aplikacji mobilnych dla dowolnego z jego usług użytkownicy końcowi (zewnętrzny) lub dla pracowników (wewnętrzny). Mają one istniejących systemów zaplecza w miejscu mainframe lub niektóre aplikacje biznesowe, które muszą zostać włączone do architektury aplikacji mobilnej. Ten przewodnik opowiada, jak najlepszym tej integracji rekomendowanie możliwych rozwiązań typowych scenariuszy.

Jest to często wymagane do wysyłania powiadomień wypychanych użytkownikom za pośrednictwem aplikacji mobilnej, gdy wystąpi zdarzenie zainteresowania w systemach wewnętrznej bazy danych. na przykład chce otrzymywać powiadomienia, gdy debetowa składa się powyżej pewnej ilości z konta lub scenariusza sieci intranet, gdzie pracowników z działu finansowego, mającego aplikacji zatwierdzenia budżetu na Windows Phone, który chce klienta banku, który ma konta w banku bankowości aplikację na telefonie iPhone  Aby otrzymywać powiadomienia, gdy zostanie odebrane żądanie zatwierdzenia.

Konta bankowego lub przetwarzania zatwierdzenia prawdopodobnie odbywać się w niektórych system wewnętrznej bazy danych, który musi inicjować powiadomienie wypychane do użytkownika. Może istnieć wiele takich zaplecza systemów, które należy utworzyć taką samą logikę do wypychania, gdy zdarzenie jest wyzwalane powiadomienie. Złożoność, w tym miejscu znajduje się w integracji kilka systemów zaplecza wraz z systemu pojedynczego wypychania, gdzie użytkownicy końcowi mogą subskrybowany przez różne powiadomienia, a nawet może istnieć wiele aplikacji dla urządzeń przenośnych. Na przykład sieci intranet aplikacje mobilne miejsce jednej aplikacji mobilnych może chcesz otrzymywać powiadomienia z wielu takich systemów zaplecza. Nie wiedzieć, lub trzeba znać wypychania semantyki/technologii, typowym rozwiązaniem tradycyjnie była wprowadzenie składnik, który sonduje systemów zaplecza dla dowolnej interesujących Cię wydarzeń i jest odpowiedzialny za wysyłanie wiadomości wypychane do systemów zaplecza klient.

Lepszym rozwiązaniem jest przy użyciu usługi Azure Service Bus — model tematu/subskrypcji, co zmniejsza złożoność podczas tworzenia skalowalne rozwiązanie.

Poniżej przedstawiono ogólne architekturę rozwiązania (uogólniony za pomocą wielu aplikacji mobilnych jednak również mają zastosowanie, gdy istnieje tylko jedna aplikacja mobilna)

## <a name="architecture"></a>Architektura

![][1]

Kluczowy element na diagramie architektury jest usługi Azure Service Bus, co zapewnia model programowania tematów/subskrypcji (więcej informacji na temat na stronie [Usługa Service Bus Pub/Sub programowania]). Odbiornik, czyli w tym przypadku zaplecza aplikacji mobilnych (zazwyczaj [Azure Mobile Service], która inicjuje powiadomienie wypychane do aplikacji mobilnych) nie otrzyma wiadomości bezpośrednio z systemami zaplecza ale zamiast tego abstrakcję pośrednich udostępniane przez warstwę [Azure Service Bus], co pozwala zaplecza aplikacji mobilnych odbierać komunikaty z jednego lub kilku systemów zaplecza. Tematu magistrali usług musi zostać utworzony dla poszczególnych systemów zaplecza, na przykład konta, HR, finansów, która jest zasadniczo "tematów" publicznej, która inicjuje komunikatów do wysłania jako powiadomienie wypychane. Systemy zaplecza wysyłają komunikaty do tych tematów. Zaplecza mobilnego mogą subskrybować jeden lub więcej takich tematów poprzez utworzenie subskrypcji usługi Service Bus. Uprawnia ono zaplecza aplikacji mobilnych, aby otrzymać powiadomienie z odpowiedniego systemu zaplecza. Zaplecza aplikacji mobilnych w dalszym ciągu nasłuchiwać komunikatów w ramach ich subskrypcji i od razu po przybyciu komunikat, ponownie i wysyła powiadomienie do jego Centrum powiadomień. Usługa Notification hubs następnie po pewnym czasie dostarczenie wiadomości do aplikacji mobilnej. Oto lista najważniejsze składniki:

1. Systemów zaplecza (systemy biznesowe/starsza wersja)
   * Tworzy tematu usługi Service Bus
   * Wysyła komunikat
1. Zaplecze mobilne
   * Tworzy subskrypcję usługi
   * Odbiera komunikat (z system wewnętrznej bazy danych)
   * Wysyła powiadomienie do klientów (za pośrednictwem Centrum powiadomień Azure)
1. Aplikacji mobilnej
   * Odbiera i powiadomienia

### <a name="benefits"></a>Korzyści

1. Oddzielenie od odbiorcy (aplikacji/usługi mobilnej za pośrednictwem Centrum powiadomień) i nadawcy (systemy zaplecza) umożliwia systemów zaplecza dodatkowe integrowany z minimalnym zmiany.
1. Zapewnia także scenariusza, w wielu aplikacjach mobilnych, możliwość odbieranie zdarzeń z jednego lub kilku systemów zaplecza.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Wymagania wstępne

Wykonaj następujące samouczki, aby zapoznać się z pojęcia, jak również typowe kroki tworzenia i konfiguracji:

1. [Usługa Service Bus Pub/Sub programowania] — w tym samouczku wyjaśniono szczegółowe informacje o pracy za pomocą usługi Service Bus tematów/subskrypcji, jak utworzyć przestrzeń nazw zawiera tematów/subskrypcji, jak wysyłać i odbierać komunikaty z nich.
2. [Notification Hubs — samouczek aplikacji uniwersalnych Windows] — w tym samouczku wyjaśniono, jak skonfigurować aplikację Windows Store i zarejestrować, a następnie odbierają powiadomienia przy użyciu usługi Notification Hubs.

### <a name="sample-code"></a>Przykładowy kod

Pełny przykładowy kod znajduje się w temacie [przykłady Centrum powiadomień]. Są one podzielone na trzy składniki:

1. **EnterprisePushBackendSystem**

    a. Ten projekt używa **WindowsAzure.ServiceBus** pakietu NuGet i jest oparta na [Usługa Service Bus Pub/Sub programowania].

    b. Ta aplikacja jest prostą aplikację konsoli C# do symulacji systemem LoB, która inicjuje komunikat, który ma zostać dostarczona do aplikacji mobilnej.

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

    c. `CreateTopic` Służy do tworzenia tematu usługi Service Bus.

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

    d. `SendMessage` Służy do wysyłania wiadomości do tego tematu usługi Service Bus. Ten kod po prostu wysyła zestaw losowe komunikatów do tematu okresowo na potrzeby przykładu. Zazwyczaj jest to system wewnętrznej bazy danych, który wysyła wiadomości, gdy wystąpi zdarzenie.

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
2. **ReceiveAndSendNotification**

    a. Ten projekt używa *WindowsAzure.ServiceBus* i **Microsoft.Web.WebJobs.Publish** NuGet pakietów i opiera się na [Usługa Service Bus Pub/Sub programowania].

    b. Następujące Aplikacja konsoli działa jako [Zadanie WebJob platformy Azure] ponieważ wykonuj ciągle, aby nasłuchiwać komunikatów z systemów biznesowych/wewnętrznej bazy danych. Ta aplikacja jest częścią zapleczu mobilnym.

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

    c. `CreateSubscription` Służy do tworzenia subskrypcji usługi Service Bus, tematu gdzie system wewnętrznej bazy danych, wysyła komunikaty. W zależności od scenariusza biznesowego, ten składnik tworzy co najmniej jedną subskrypcję do odpowiednich tematów (na przykład niektóre może odbierać komunikaty z systemu HR, niektóre z systemu Finanse i tak dalej)

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

    d. `ReceiveMessageAndSendNotification` Służy do odczytu komunikatu z tematu przy użyciu swojej subskrypcji i jeśli odczytu zakończy się pomyślnie do wysłania do aplikacji mobilnej przy użyciu usługi Azure Notification Hubs następnie pracowali powiadomień (w przykładowym scenariuszu Windows natywnych wyskakujące powiadomienie).

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

    e. Do publikowania tej aplikacji jako **zadania WebJob**, kliknij prawym przyciskiem myszy rozwiązanie w programie Visual Studio i wybierz **Publikuj jako zadanie WebJob**

    ![][2]

    f. Wybierz swój profil publikowania i Utwórz nową witrynę sieci Web platformy Azure, jeśli go jeszcze nie istnieje, który hostuje tego zadania WebJob, i po utworzeniu witryny sieci Web jest następnie **Publikuj**.

    ![][3]

    g. Konfigurowanie zadania jako "Uruchamianie stale", aby podczas logowania się do [Azure Portal] powinien zostać wyświetlony, podobną do następującej:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Ta aplikacja jest w przypadku aplikacji Windows Store i odbiera powiadomienia wyskakujące z zadań WebJob, uruchomione jako część usługi zaplecza aplikacji mobilnych i wyświetl ją. Ten kod jest oparty na [Notification Hubs — samouczek aplikacji uniwersalnych Windows].  

    b. Upewnij się, że aplikacja jest włączony do odbierania wyskakujących powiadomień.

    c. Upewnij się, że następujące usługi Notification Hubs kodu rejestracyjnego jest wywoływana w jej uruchamiania (po zastąpieniu `HubName` i `DefaultListenSharedAccessSignature` wartości:

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

1. Upewnij się, że zadania WebJob działa prawidłowo i zaplanowane do uruchomienia w sposób ciągły.
2. Uruchom **EnterprisePushMobileApp**, który uruchamia aplikację Windows Store.
3. Uruchom **EnterprisePushBackendSystem** aplikację konsolową, która symuluje LoB wewnętrznej bazy danych i rozpoczyna wysyłanie wiadomości, a powinien otrzymywać wyskakujące powiadomienia, które pojawiają się podobnie jak na poniższej ilustracji:

    ![][5]

4. Komunikaty zostały pierwotnie wysyłane do tematów usługi Service Bus, w których był monitorowany przez subskrypcje usługi Service Bus w zadaniu sieci Web. Gdy wiadomość została odebrana, powiadomienie utworzono i wysyłane do aplikacji mobilnej. Można sprawdzić za pomocą dzienników zadań WebJob, aby potwierdzić przetwarzania danych, po przejściu do linku dzienniki w [Azure Portal] dla zadania w sieci Web:

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
[Azure Mobile Service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Usługa Service Bus Pub/Sub programowania]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Zadanie WebJob platformy Azure]: ../app-service/webjobs-create.md
[Notification Hubs — samouczek aplikacji uniwersalnych Windows]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal]: https://portal.azure.com/
