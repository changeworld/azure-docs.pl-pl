---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74260828"
---
## <a name="webapi-project"></a>Projekt WebAPI

1. W programie Visual Studio otwórz projekt **AppBackend** utworzony w samouczku **Powiadom użytkowników.**
2. W Notifications.cs zastąp całą klasę **Notifications** następującym kodem. Pamiętaj, aby zastąpić symbole zastępcze ciągiem połączenia (z pełnym dostępem) dla centrum powiadomień i nazwą centrum. Te wartości można uzyskać z [witryny Azure portal](https://portal.azure.com). Ten moduł reprezentuje teraz różne bezpieczne powiadomienia, które zostaną wysłane. W pełnej implementacji powiadomienia będą przechowywane w bazie danych; dla uproszczenia, w tym przypadku przechowujemy je w pamięci.
   
   ```csharp
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
    ```

1. W NotificationsController.cs, zastąpić kod wewnątrz **NotificationsController** definicji klasy z następującym kodem. Ten składnik implementuje sposób dla urządzenia, aby bezpiecznie pobrać powiadomienie, a także zapewnia sposób (na potrzeby tego samouczka) do wyzwalania bezpiecznego wypychania do urządzeń. Należy pamiętać, że podczas wysyłania powiadomienia do centrum powiadomień wysyłamy tylko nieprzetworzone powiadomienie z identyfikatorem powiadomienia (i bez rzeczywistej wiadomości):
   
   ```csharp
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
    ```

Należy zauważyć, że `Post` metoda nie wysyła teraz wyskakujących powiadomień. Wysyła nieprzetworzone powiadomienie, które zawiera tylko identyfikator powiadomienia, a nie żadnych poufnych treści. Ponadto upewnij się, że komentarz operacji wysyłania dla platform, dla których nie masz poświadczenia skonfigurowane w centrum powiadomień, ponieważ będą one powodować błędy.

1. Teraz ponownie wdrożymy tę aplikację w witrynie sieci Web platformy Azure, aby była dostępna ze wszystkich urządzeń. Kliknij prawym przyciskiem myszy projekt **AppBackend** i wybierz polecenie **Publikuj**.
2. Wybierz witrynę sieci Web platformy Azure jako miejsce docelowe publikowania. Zaloguj się za pomocą konta platformy Azure i wybierz istniejącą lub nową witrynę sieci Web i zanotuj **właściwość docelowego adresu URL** na karcie **Połączenie.** Będziemy odnosić się do tego adresu URL jako *punktu końcowego wewnętrznej bazy danych* w dalszej części tego samouczka. Kliknij przycisk **Opublikuj**.
