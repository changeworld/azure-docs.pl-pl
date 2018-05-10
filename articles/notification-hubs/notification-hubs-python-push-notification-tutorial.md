---
title: Jak używać usługi Notification Hubs z języka Python
description: Dowiedz się, jak używać usługi Azure Notification Hubs z zaplecza Python.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 32953bacb8fdb135d5f3e0e9324218d2a71b0818
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-notification-hubs-from-python"></a>Jak używać usługi Notification Hubs w języku Python
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostęp do wszystkich funkcji centra powiadomień z zaplecza Java/PHP/Python/Ruby przy użyciu interfejsu REST Centrum powiadomień, zgodnie z opisem w temacie MSDN [interfejsów API REST centra powiadomień](http://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> To jest przykładowy implementacji wykonywania wysyła powiadomienia w języku Python i nie jest oficjalnie obsługiwana SDK Python Centrum powiadomień.
> 
> Ten przykład jest zapisywany przy użyciu języka Python 3.4.
> 
> 

W tym artykule przedstawiono sposób do:

* Tworzenie klienta REST dla funkcji usługi Notification Hubs w języku Python.
* Wysyłanie powiadomień za pomocą interfejsu Python do interfejsów API REST Centrum powiadomień. 
* Pobierz zrzutu REST protokołu HTTP żądania/odpowiedzi w celu debugowania edukacyjnych. 

Możesz wykonać [samouczku Get](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) dla platformy przenośne wyboru wdrażanie części zaplecza w języku Python.

> [!NOTE]
> Zakres próbki jest ograniczona tylko do wysyłania powiadomień i nie wykonuje żadnych zarządzania rejestracji.
> 
> 

## <a name="client-interface"></a>Interfejs klienta
Interfejs klienta głównego zapewniają te same metody, które są dostępne w [.NET SDK centra powiadomień](http://msdn.microsoft.com/library/jj933431.aspx). Ten interfejs umożliwia tłumaczenie bezpośrednio samouczki i przykłady, które są obecnie dostępne w tej witrynie, a zamieszczone przez społeczność w Internecie.

Możesz znaleźć kod dostępne w [próbki otoki Python REST].

Na przykład można utworzyć klienta:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Aby wysłać powiadomienie wyskakujące systemu Windows:

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

## <a name="implementation"></a>Wdrażanie
Jeśli tak, nie jest jeszcze nie, wykonaj [samouczku Get] w górę do ostatniej sekcji, w którym należy implementować zaplecza.

Wszystkie szczegóły, aby zaimplementować pełne otoki REST można znaleźć w [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano implementacji Python głównych kroków wymaganych dostęp punkty końcowe REST centra powiadomień do wysyłania powiadomień

1. Analizowanie parametrów połączenia
2. Wygeneruj token autoryzacji
3. Wyślij powiadomienia za pomocą interfejsu API REST protokołu HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia
W tym miejscu jest główna klasa implementacji klienta, którego konstruktor analizuje parametry połączenia:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"

        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug

            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")

            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Utwórz token zabezpieczeń
Szczegóły dotyczące tworzenia tokenu zabezpieczeń są dostępne [tutaj](http://msdn.microsoft.com/library/dn495627.aspx).
Dodaj następujące metody **NotificationHub** do utworzenia tokenu na podstawie identyfikatora URI bieżącego żądania i poświadczenia wyodrębniony z ciągu połączenia.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Wyślij powiadomienia za pomocą interfejsu API REST protokołu HTTP
Użyj pierwszego, umożliwiają definiowanie Klasa reprezentująca powiadomienie.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

            self.format = notification_format
            self.payload = payload

            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Ta klasa jest kontenerem dla treści natywnych powiadomień lub zbiór właściwości szablonu powiadomienia, zestaw nagłówków, zawiera formacie (native platformy lub szablonu) i właściwości specyficzne dla platformy (na przykład właściwość wygaśnięcia firmy Apple i nagłówki WNS).

Zapoznaj się [dokumentacja interfejsów API REST centra powiadomień](http://msdn.microsoft.com/library/dn495827.aspx) i formaty na platformach powiadomienia określonych dla wszystkich dostępnych opcji.

Teraz z tą klasą wysyłania powiadomień metod zapisu wewnątrz **NotificationHub** klasy.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Te metody Wyślij żądanie HTTP POST do punktu końcowego /messages Centrum powiadomień, z poprawną treści i nagłówków, aby wysłać powiadomienie.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Aby włączyć szczegółowe rejestrowanie przy użyciu właściwości debugowania
Włączanie właściwości debugowania podczas inicjowania Centrum powiadomień zapisuje szczegółowe rejestrowanie informacji o informacje dotyczące żądania HTTP i zrzutu odpowiedzi, a także szczegółowe komunikatu powiadomienia wysyłania wyników. [TestSend centra powiadomień właściwość](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) zwraca szczegółowe informacje na temat wyniku wysyłania powiadomienia. Można go użyć — inicjowanie, używając następującego kodu:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Wyślij Centrum powiadomień żądanie adresu URL HTTP pobiera w związku z tym dołączane querystring "test". 

## <a name="complete-tutorial"></a>Ukończ samouczek
Teraz możesz ukończyć Samouczek wprowadzający przez wysyłanie powiadomień z zaplecza Python.

Inicjowanie klienta usługi Notification Hubs (zastąpić nazwę Centrum i parametry połączenia, zgodnie z instrukcją w [samouczku Get]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Następnie dodaj kod wysyłania, w zależności od platform przenośnych docelowych. W tym przykładzie dodano również wyższego poziomu metody w celu umożliwienia wysyłania powiadomień z platformą, na przykład send_windows_notification dla systemu windows; send_apple_notification (dla apple) itd. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Sklep Windows i Windows Phone 8.1 (z systemem innym niż platformy Silverlight)
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 i 8.1 Silverlight
    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS
    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Uruchamianie kodu języka Python powinny być powiadomienie znajdujących się na urządzeniu docelowym.

## <a name="examples"></a>Przykłady:
### <a name="enabling-debug-property"></a>Włączenie właściwości debugowania
Po włączeniu flagi debugowania podczas inicjowania NotificationHub, zobaczysz szczegółowe żądania HTTP i zrzutu odpowiedzi, a także NotificationOutcome podobnie do następującej gdzie można zrozumieć, jakie nagłówki HTTP są przekazywane w żądaniu i jakie odpowiedź HTTP Odebrano z Centrum powiadomień: ![][1]

Zobacz, na przykład szczegółowe wyniki Centrum powiadomień. 

* gdy komunikat jest wysyłany pomyślnie Push Notification Service. 
  
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
* Jeśli nie było żadnych elementów docelowych znaleziono dla wszystkich powiadomień wypychanych, następnie prawdopodobnie będą wyświetlać następujące dane wyjściowe odpowiedzi (która wskazuje, czy nie było żadnej rejestracji znaleziono prawdopodobnie dostarczyć powiadomienia, ponieważ rejestracje miała niektóre niezgodne Znaczniki)
  
        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Emisji wyskakujące powiadomienie do systemu Windows
Zwróć uwagę, nagłówki, które get wysyłane podczas przesyłania emisji wyskakujących powiadomień na klientach z systemem Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Wyślij powiadomienie, określając tag (lub wyrażenie etykiety)
Zwróć uwagę, nagłówek HTTP znaczników, który pobiera dodane do żądania HTTP (w poniższym przykładzie parametr powiadomienie jest wysyłane tylko do rejestracji z ładunku "Sport")

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Wyślij powiadomienie określania wielu tagów
Zwróć uwagę, jak nagłówka HTTP tagi zostanie zmieniona, gdy wiele tagów są wysyłane. 

    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Szablonu powiadomień
Zwróć uwagę, że zmiany nagłówka formatu HTTP i treści ładunku jest wysyłany jako część treści żądania HTTP:

**Po stronie klienta — zarejestrowanych szablonu**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";

**Po stronie serwera — wysyłanie ładunku**

        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]

## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak utworzyć klienta REST języka Python dla usługi Notification Hubs. W tym miejscu można wykonywać następujące czynności:

* Pobierz pełny [próbki otoki Python REST], który zawiera wszystkie kodu w tym artykule.
* Kontynuować szkoleniowe dotyczące usługi Notification Hubs znakowanie funkcja [samouczek fundamentalne wiadomości]
* Kontynuować zapoznawanie funkcji szablonów centra powiadomień w [samouczek lokalizowanie wiadomości]

<!-- URLs -->
[próbki otoki Python REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[samouczku Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[samouczek fundamentalne wiadomości]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[samouczek lokalizowanie wiadomości]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png

