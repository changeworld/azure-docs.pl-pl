---
title: Architektura Push Notification Hubs — Enterprise
description: Wskazówki dotyczące używania usługi Azure Notification Hubs w środowisku przedsiębiorstwa
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d7066b58330d35e5dba66cfe6ed5cfaddff4b68a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778066"
---
# <a name="enterprise-push-architectural-guidance"></a>Wskazówki dotyczące architektury powiadomień wypychanych w przedsiębiorstwie
Dzisiaj przedsiębiorstwa są stopniowo przenoszenie do tworzenia aplikacji dla urządzeń przenośnych dla dowolnego użytkownikom końcowym (zewnętrzne) lub dla pracowników (wewnętrzny). Mają one istniejących systemów zaplecza w miejscu Komputery mainframe firmy i niektóre aplikacje LoB, które muszą zostać włączone do architektury aplikacji mobilnej. Ten przewodnik zawiera o jak najlepszym rozwiązaniem tej integracji rekomendowanie możliwe rozwiązanie typowych scenariuszy.

Częste wymaganie dotyczy wysyłać powiadomienia wypychane do użytkowników za pośrednictwem ich aplikacji mobilnej, gdy wystąpi zdarzenie zainteresowania w systemach wewnętrznej bazy danych. na przykład chce otrzymać powiadomienie, gdy debetowa staje się powyżej pewnego z konta lub scenariusz sieci intranet, gdzie pracowników z działu finansowego mającego aplikacji zatwierdzenia budżetu na Windows Phone, który chce klienta mającego banku bankowości aplikacji na telefonie iPhone  Aby otrzymać powiadomienie, gdy zostanie odebrane żądanie zatwierdzenia.

Konta bankowego lub przetwarzania zatwierdzenia prawdopodobnie odbywać się w niektórych systemu wewnętrznej bazy danych, konieczne jest zainicjowanie wypychania do użytkownika. Może istnieć wiele takich wewnętrznej bazy danych systemów, które należy utworzyć ten sam rodzaj logiki do dystrybuowania, gdy zdarzenie jest wyzwalane powiadomienie. Złożoność tutaj znajduje się w integrowania kilka systemów wewnętrznej bazy danych wraz z systemu pojedynczego wypychania, gdzie użytkownicy końcowi mogą subskrybuje różnych powiadomienia, a może nawet istnieć wiele aplikacji dla urządzeń przenośnych. Na przykład intranet aplikacje mobilne miejsce jednego aplikacji mobilnej może chcesz otrzymywać powiadomień według wiele takich systemów zaplecza. Systemy zaplecza nie znasz lub nie trzeba znać wypychania semantyki/technologii, w tym miejscu typowych rozwiązań tradycyjnie była wprowadzenie składnika, który sonduje systemów zaplecza, wszystkie zdarzenia zainteresowań i jest odpowiedzialny za wysyłanie wiadomości wypychanych do klient.

Lepszym rozwiązaniem jest do użycia usługi Azure Service Bus — model tematu/subskrypcji, co zmniejsza złożoność jednocześnie skalowalne rozwiązanie.

Poniżej przedstawiono ogólne architektury rozwiązania (uogólniony z wielu aplikacji mobilnych ale również mają zastosowanie, gdy istnieje tylko jeden aplikacji mobilnej)

## <a name="architecture"></a>Architektura
![][1]

Element klucza na tym diagramie architektury jest usługi Azure Service Bus, która zapewnia model programowania tematy/subskrypcji (więcej informacji na temat go w [programowania magistrali usługi Pub/Sub]). Odbiornik, czyli w tym przypadku zaplecza aplikacji mobilnych (zazwyczaj [usługi mobilnej Azure], która inicjuje wypychane do aplikacji mobilnych) nie odbiera wiadomości bezpośrednio z systemów zaplecza ale zamiast tego abstrakcję pośredni Warstwa podał [Azure Service Bus, dzięki czemu zaplecze aplikacji mobilnej do odbierania wiadomości z jednego lub kilku systemów zaplecza. Temat magistrali usługi musi zostać utworzony dla poszczególnych systemów zaplecza, na przykład konto, HR, not, która jest zasadniczo "tematów", która inicjuje komunikatów do wysłania jako powiadomienie wypychane. Systemy zaplecza wysyłanie komunikatów do tych tematów. Zaplecza Mobile mogą subskrybować jeden lub więcej takich tematów podczas tworzenia subskrypcji usługi Service Bus. Uprawnia ono zaplecza mobilnego, aby otrzymać powiadomienie z odpowiedniego systemu wewnętrznej bazy danych. Zaplecze aplikacji mobilnej w dalszym ciągu nasłuchiwać komunikatów na ich subskrypcji i zaraz po odebraniu komunikatu Przechodzi wstecz i wysyła je jako powiadomienie jego Centrum powiadomień. Centra powiadomień następnie ostatecznie dostarczenie wiadomości do aplikacji mobilnej. Oto lista najważniejsze składniki:

1. Systemy zaplecza (LoB/starsze systemy)
   * Tworzy temat magistrali usług
   * Wysyła komunikat
2. Zaplecze mobilne
   * Tworzy subskrypcji usługi
   * Odbiera komunikat (z wewnętrznej bazy danych systemu)
   * Wysyła powiadomienie do klientów (za pośrednictwem Centrum powiadomień Azure)
3. Aplikacji mobilnej
   * Odbiera i wyświetlania powiadomień

### <a name="benefits"></a>Korzyści:
1. Rozdzielenie między odbiornika (aplikacji/usługi mobilnej za pośrednictwem Centrum powiadomień) i nadawcy (systemy zaplecza) umożliwia systemów dodatkowe wewnętrznej bazy danych jest zintegrowany z minimalnym zmiany.
2. Zapewnia także scenariusza wiele aplikacji mobilnych możliwość odbieranie zdarzeń z jednego lub kilku systemów zaplecza.  

## <a name="sample"></a>Przykład:
### <a name="prerequisites"></a>Wymagania wstępne
Wykonaj następujące samouczki, aby zapoznać się z założenia, a także typowe kroki tworzenia i konfiguracji:

1. [programowania magistrali usługi Pub/Sub] — w tym samouczku opisano szczegółowe informacje o pracy z tematów magistrali usługi/subskrypcji, jak utworzyć przestrzeń nazw zawiera tematy/subskrypcji, jak wysyłać i odbierać komunikaty z nich.
2. [Notification Hubs — samouczek aplikacji uniwersalnych systemu Windows] — w tym samouczku wyjaśniono, jak skonfigurować aplikację ze Sklepu Windows i zarejestrować, a następnie odbierają powiadomienia przy użyciu usługi Notification Hubs.

### <a name="sample-code"></a>Przykładowy kod
Kod pełny przykład jest dostępny w [przykłady Centrum powiadomień]. Są one podzielone na trzy składniki:

1. **EnterprisePushBackendSystem**
   
    a. Ten projekt używa *WindowsAzure.ServiceBus* pakietu NuGet i jest oparty na [programowania magistrali usługi Pub/Sub].
   
    b. Ta aplikacja jest prostej C# console aplikacji symulowanie systemu LoB, która inicjuje komunikat, który ma być dostarczony do aplikacji mobilnej.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` Służy do tworzenia tematu usługi Service Bus.
   
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
   
    d. `SendMessage` Służy do wysyłania komunikatów do tego tematu magistrali usługi. Ten kod po prostu wysyła zestaw losowe komunikatów do tematu okresowo na potrzeby przykładu. Zazwyczaj jest system wewnętrznej bazy danych, który wysyła wiadomości, gdy wystąpi zdarzenie.
   
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
2. **ReceiveAndSendNotification**
   
    a. Ten projekt używa *WindowsAzure.ServiceBus* i *Microsoft.Web.WebJobs.Publish* NuGet pakietów i jest oparty na [programowania magistrali usługi Pub/Sub].
   
    b. Uruchamia następujące aplikacji konsoli jako [zadań WebJob Azure] ponieważ uruchamiaj stale nasłuchiwać komunikatów z systemów LoB/wewnętrznej bazy danych. Ta aplikacja jest częścią Twojego zaplecze aplikacji mobilnej.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription that receives messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` Służy do tworzenia subskrypcji usługi Service Bus dla tematu gdzie system zaplecza wysyła wiadomości. W zależności od scenariusza biznesowego ten składnik tworzy jedną lub więcej subskrypcji do odpowiednich tematów (na przykład niektóre może odbierać komunikaty z systemu HR, niektóre z systemu finansowego i tak dalej)
   
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
   
    d. ReceiveMessageAndSendNotification jest używany do odczytywania wiadomości z tematu przy użyciu jego subskrypcji i jeśli odczytu zakończy się pomyślnie przesyłany do aplikacji mobilnej przy użyciu usługi Azure Notification Hubs następnie spreparować powiadomienia (w przykładowym scenariuszu natywnego wyskakujących powiadomień systemu Windows).
   
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
   
    e. Do publikowania tej aplikacji jako **zadania WebJob**, kliknij prawym przyciskiem myszy rozwiązanie w programie Visual Studio i wybierz **Publikuj jako zadanie WebJob**
   
    ![][2]
   
    f. Wybierz profil publikowania i utworzyć nową witrynę sieci Web platformy Azure, jeśli go jeszcze nie istnieje, który obsługuje ten zadania WebJob i po utworzeniu witryny sieci Web jest następnie **publikowania**.
   
    ![][3]
   
    g. Skonfiguruj zadania jako "Uruchom stale", aby po zalogowaniu się do [portalu Azure] powinny zostać wyświetlone informacje takie jak następujące:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Ta aplikacja jest aplikacją ze Sklepu Windows, który odbiera wyskakujące powiadomienia z zadania WebJob uruchomienia w ramach Twojej zaplecza aplikacji mobilnych i wyświetl ją. Ten kod jest oparta na [Notification Hubs — samouczek aplikacji uniwersalnych systemu Windows].  
   
    b. Upewnij się, że aplikacja jest włączona do odbierania wyskakujących powiadomień.
   
    c. Upewnij się, że następujące usługi Notification Hubs kod rejestracji jest wywoływana w aplikacji uruchomienia (po zastąpieniu *HubName* i *DefaultListenSharedAccessSignature*:
   
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

### <a name="running-sample"></a>Uruchamianie przykładowych:
1. Upewnij się, że WebJob działa prawidłowo i zaplanowane do uruchomienia w sposób ciągły.
2. Uruchom ** EnterprisePushMobileApp, która uruchamia aplikację ze Sklepu Windows.
3. Uruchom **EnterprisePushBackendSystem** aplikację konsolową, która symuluje LoB wewnętrznej bazy danych i rozpoczyna wysyłanie wiadomości i powinna zostać wyświetlona wyskakujące powiadomienia pojawiające się, jak na poniższej ilustracji:
   
    ![][5]
4. Wiadomości zostały pierwotnie wysyłane do tematów usługi Service Bus, które monitorowanym przez subskrypcje usługi Service Bus w zadanie sieci Web. Po Odebrano komunikat powiadomienia została tworzonych i wysyłanych do aplikacji mobilnej. Można przeglądać dzienniki zadania WebJob, aby potwierdzić przetwarzania po przejściu do konsolidacji dzienników w [portalu Azure] dla zadania sieci Web:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[przykłady Centrum powiadomień]: https://github.com/Azure/azure-notificationhubs-samples
[usługi mobilnej Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[programowania magistrali usługi Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[zadań WebJob Azure]: ../app-service/web-sites-create-web-jobs.md
[Notification Hubs — samouczek aplikacji uniwersalnych systemu Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[portalu Azure]: https://portal.azure.com/
