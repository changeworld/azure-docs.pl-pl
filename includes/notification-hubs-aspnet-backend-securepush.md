---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b8cf4217ca6c80be998b92e71c3ba29c4f68bce2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111520"
---
## <a name="webapi-project"></a>Projektem WebAPI
1. W programie Visual Studio, otwórz **AppBackend** projektu, który został utworzony w **powiadamianie użytkowników** samouczka.
2. W klasie Notifications.cs Zastąp cały **powiadomienia** klasy z następującym kodem. Koniecznie Zastąp symbole zastępcze parametry połączenia dla Centrum powiadomień i nazwą Centrum (z pełnym dostępem). Możesz uzyskać te wartości z [witryny Azure portal](http://portal.azure.com). Ten moduł odpowiada teraz różnych powiadomień zabezpieczonych, które będą wysyłane. W pełną implementację powiadomienia będą przechowywane w bazie danych; dla uproszczenia w tym przypadku będziemy przechowywać je w pamięci.
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. W pliku NotificationsController.cs, Zastąp kod wewnątrz **NotificationsController** klasy definicji z następującym kodem. Ten składnik implementuje sposób bezpiecznego pobierania powiadomienia urządzenia, a także sposób (na potrzeby tego samouczka) do wyzwalania bezpiecznej operacji wypychania do urządzeń. Należy zwrócić uwagę na to, czy podczas wysyłania powiadomienia z Centrum powiadomień, możemy wysyłać tylko nieprzetworzone powiadomienia o identyfikatorze powiadomienia (i nie komunikat rzeczywiste):
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


Należy pamiętać, że `Post` metoda teraz wysyłał powiadomienie wyskakujące. Wysyła powiadomienie raw, zawierający tylko identyfikator powiadomień, a nie wszystkie poufnej zawartości. Upewnij się również dodać komentarz operacji wysyłania dla platform, dla których nie masz poświadczenia skonfigurowane w Centrum powiadomień, jak będą powodować błędy.

1. Teraz ponownie wdrożymy tę aplikację w witrynie sieci Web platformy Azure, aby go udostępnić wszystkim urządzeniom. Kliknij prawym przyciskiem myszy projekt **AppBackend** i wybierz polecenie **Publikuj**.
2. Wybierz witryny internetowej platformy Azure jako swoje docelową lokalizację publikacji. Zaloguj się przy użyciu konta platformy Azure i wybrać istniejącą lub nową witrynę sieci Web i zanotuj **docelowy adres URL** właściwość **połączenia** kartę. W dalszej części tego samouczka będziemy nazywać ten adres URL *punktem końcowym zaplecza*. Kliknij przycisk **publikowania**.

