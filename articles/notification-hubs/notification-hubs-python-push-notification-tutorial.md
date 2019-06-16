---
title: Jak używać usługi Notification Hubs za pomocą języka Python
description: Dowiedz się, jak używać usługi Azure Notification Hubs z zaplecza środowiska Python.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: 43a691ff9025cdb39786f965be6a2fca1b33bd3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458459"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Jak używać usługi Notification Hubs za pomocą języka Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostęp do wszystkich funkcji usługi Notification Hubs z zaplecza Java/PHP/Python/Ruby przy użyciu interfejsu REST Centrum powiadomień, zgodnie z opisem w artykule z witryny MSDN [interfejsów API REST usługi Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> To jest przykładowy implementację referencyjną, implementowania wysyła powiadomienia w języku Python i nie jest oficjalnie obsługiwanych zestawu SDK języka Python Centrum powiadomień. Plik został utworzony przy użyciu języka Python 3.4.

W tym artykule przedstawiono, jak do:

- Tworzenie klienta REST dla funkcji usługi Notification Hubs w języku Python.
- Wysyłanie powiadomień za pomocą interfejsu Python do interfejsów API REST Centrum powiadomień.
- Pobierz zrzut REST protokołu HTTP żądania/odpowiedzi w celu debugowania edukacyjnych.

Możesz wykonać [samouczek z wprowadzeniem](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) na wybraną platformę przenośnych, wdrażanie część zaplecza w języku Python.

> [!NOTE]
> Zakres próbki jest ograniczona tylko do wysyłania powiadomień, a nie wszystkie Zarządzanie rejestracją.

## <a name="client-interface"></a>Interfejs klienta

Interfejs klienta głównego może zapewnić te same metody, które są dostępne w [zestawu SDK .NET Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx). Ten interfejs umożliwia tłumaczenie bezpośrednio, samouczków i przykładów, które są obecnie dostępne w tej witrynie i udostępnione przez społeczność w Internecie.

Dostępne w kod możesz znaleźć [Przykład otoka interfejsu REST języka Python].

Na przykład można utworzyć klienta:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Aby wysłać powiadomienie wyskakujące Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Wdrażanie

Jeśli jeszcze nie zrobiono, wykonaj [samouczek z wprowadzeniem] w górę do ostatniej sekcji, w którym trzeba zaimplementować zaplecza.

Poznaj wszystkie szczegóły implementacji pełnego otoki REST znajduje się na [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano implementacji języka Python główne kroki wymagane do dostępu punkty końcowe REST centrów powiadomień i wysyłania powiadomień

1. Analizowanie parametrów połączenia
2. Wygeneruj token autoryzacji
3. Wyślij powiadomienia za pomocą interfejsu API REST protokołu HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

W tym miejscu jest główna klasa implementacji klienta, której Konstruktor analizuje ciąg połączenia:

```python
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
```

### <a name="create-security-token"></a>Tworzenie tokenu zabezpieczeń

Dostępne są szczegółowe informacje o tworzeniu tokenu zabezpieczeń [tutaj](https://msdn.microsoft.com/library/dn495627.aspx).
Dodaj następujące metody `NotificationHub` klasy do utworzenia tokenu na podstawie identyfikatora URI bieżącego żądania i poświadczenia wyodrębnione z parametrów połączenia.

```python
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
```

### <a name="send-a-notification-using-http-rest-api"></a>Wyślij powiadomienia za pomocą interfejsu API REST protokołu HTTP

Użyj pierwszego, pozwalają zdefiniować Klasa reprezentująca powiadomienie.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm', 'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Ta klasa jest kontenerem dla treści natywnych powiadomień lub zbiór właściwości szablonu powiadomienia, zestaw nagłówków, zawierającą formatu (platformy natywnej lub szablonu) i właściwości specyficzne dla platformy (np. właściwości wygasania firmy Apple i usługą WNS nagłówków).

Zapoznaj się [dokumentacji interfejsów API REST usługi Notification Hubs](https://msdn.microsoft.com/library/dn495827.aspx) na platformach powiadomień określone formaty i dla wszystkich dostępnych opcji.

Teraz za pomocą tej klasy, Wyślij powiadomienie metod zapisu wewnątrz `NotificationHub` klasy.

```python
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

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

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

def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
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
```

Te metody Wyślij żądanie HTTP POST do punktu końcowego /messages Twoje Centrum powiadomień z poprawną treści i nagłówki, aby wysłać powiadomienie.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Aby włączyć szczegółowe rejestrowanie przy użyciu właściwości debugowania

Włączenie właściwości debugowania podczas inicjowania Centrum powiadomień zapisuje się szczegółowe rejestrowanie informacji o żądaniu HTTP, a następnie zrzutu odpowiedzi, a także szczegółowe komunikat z powiadomieniem Wyślij wynik.
[Właściwość Notification Hubs TestSend](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) zwraca szczegółowe informacje na temat wyniku wysyłania powiadomienia.
Można go używać — inicjowanie, używając następującego kodu:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Żądanie Wyślij Centrum powiadomień adres HTTP URL pobiera dołączane za pomocą ciągu zapytania "test" w wyniku.

## <a name="complete-tutorial"></a>Ukończ samouczek

Można teraz ukończyć Samouczek wprowadzający, przez wysyłanie powiadomień z zaplecza środowiska Python.

Inicjowanie klienta usługi Notification Hubs (zastąpić nazwę Centrum i parametry połączenia, zgodnie z instrukcją w [samouczek z wprowadzeniem]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Następnie dodaj Wyślij kod w zależności od docelowej platformy mobilnej. Ten przykład dodaje również wyższego poziomu metody w celu umożliwienia wysyłania powiadomienia, w zależności od platformy, na przykład send_windows_notification dla systemu windows; send_apple_notification (dla firmy apple) itd.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store i Windows Phone 8.1 (bez użycia platformy Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 i 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Uruchamianie kodu w języku Python powinien wywoływać powiadomienia, pojawiają się na urządzeniu docelowym.

## <a name="examples"></a>Przykłady

### <a name="enabling-the-debug-property"></a>Włączanie `debug` właściwości

Po włączeniu flagi debugowania podczas inicjowania Centrum NotificationHub zobaczysz szczegółowe żądania HTTP i odpowiedzi zrzutu, a także NotificationOutcome podobnie do poniższego gdzie może zrozumieć nagłówki HTTP, które są przekazywane w żądaniu i jakie odpowiedź HTTP odebranych z Centrum powiadomień:

![][1]

Zobacz, na przykład szczegółowych wyników Centrum powiadomień.

- Po pomyślnym wysłaniu komunikatu do usługi powiadomień wypychanych.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Jeśli nie było żadnych elementów docelowych, znaleziono dla powiadomień wypychanych, następnie prawdopodobnie ma być widoczne następujące wyniki jako odpowiedzi (co wskazuje na istnienie rejestracji, nie znaleziono prawdopodobnie dostarczyć powiadomienie, ponieważ rejestracje miał niektóre niezgodny Znaczniki)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Emisja powiadomienia wyskakujące Windows

Zwróć uwagę, nagłówki, które Pobierz wysyłane podczas emisji wyskakujące powiadomienie jest wysyłany do klienta Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Wyślij powiadomienie, określając tag (lub wyrażenie etykiety)

Zwróć uwagę, nagłówek HTTP tagi, które pobiera dodane do żądania HTTP (w poniższym przykładzie powiadomienia wysyłane tylko do rejestracji z ładunkiem "Sport")

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Wyślij powiadomienie Określanie wielu tagów

Zwróć uwagę, jak zmienia się nagłówek HTTP tagów podczas wysyłania wielu tagów.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Powiadomienia oparte na szablonach

Zwróć uwagę, że zmiany formatu HTTP nagłówek i treść ładunek jest wysyłany jako część treści żądania HTTP:

**Po stronie klienta — zarejestrowanych szablonu:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Po stronie serwera — wysyłanie ładunek:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak można utworzyć klienta REST języka Python dla usługi Notification Hubs. W tym miejscu możesz wykonywać następujące czynności:

- Pobierz pełną [Przykład otoka interfejsu REST języka Python], który zawiera cały kod w tym artykule.
- Uzyskać więcej informacji o funkcji w tagowania w usłudze Notification Hubs [Przerwanie samouczek wiadomości]
- Uzyskać więcej informacji o funkcji szablonów centra powiadomień w [Lokalizowanie samouczek wiadomości]

<!-- URLs -->
[Przykład otoka interfejsu REST języka Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Samouczek z wprowadzeniem]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Przerwanie samouczek wiadomości]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Lokalizowanie samouczek wiadomości]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
