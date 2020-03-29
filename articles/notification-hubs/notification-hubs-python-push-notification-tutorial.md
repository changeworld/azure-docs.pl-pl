---
title: Jak korzystać z Centrów powiadomień w języku Python
description: Dowiedz się, jak korzystać z usługi Azure Notification Hubs z aplikacji języka Python.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 1ff8c382813654b1dee38a99bf2cc0ca67afbedd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313831"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Jak korzystać z Centrów powiadomień z języka Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostęp do wszystkich funkcji Centrów powiadomień można uzyskać z zaplecza Java/PHP/Python/Ruby za pomocą interfejsu REST centrum powiadomień, zgodnie z opisem w artykule MsDN [Notification Hubs REST API](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Jest to przykładowa implementacja referencyjna do implementacji powiadomienia wysyła w języku Python i nie jest oficjalnie obsługiwane Notifications Hub Python SDK. Próbka została utworzona przy użyciu języka Python 3.4.

W tym artykule wyjaśniono, jak:

- Tworzenie klienta REST dla funkcji centrum powiadomień w języku Python.
- Wysyłaj powiadomienia przy użyciu interfejsu Języka Python do interfejsów API REST Centrum powiadomień.
- Pobierz zrzut żądania HTTP REST/odpowiedzi dla debugowania/celów edukacyjnych.

Możesz wykonać [samouczek Wprowadzenie](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) dla wybranej platformy mobilnej, implementując część zaplecza w Pythonie.

> [!NOTE]
> Zakres przykładu jest ograniczony tylko do wysyłania powiadomień i nie wykonuje żadnych zarządzania rejestracją.

## <a name="client-interface"></a>Interfejs klienta

Główny interfejs klienta może zapewnić te same metody, które są dostępne w [pliku .NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx). Ten interfejs pozwala bezpośrednio przetłumaczyć wszystkie samouczki i przykłady obecnie dostępne na tej stronie i przekazane przez społeczność w Internecie.

Cały kod dostępny w [przykładzie otoki Python REST]można znaleźć w przykładzie otoki .

Na przykład, aby utworzyć klienta:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Aby wysłać wyskakujące powiadomienie systemu Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Wdrażanie

Jeśli jeszcze tego nie zrobiłeś, wykonaj [samouczek Wprowadzenie] do ostatniej sekcji, w której musisz zaimplementować zaplecze.

Wszystkie szczegóły do wdrożenia pełnej otoki REST można znaleźć na [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano implementację języka Python głównych kroków wymaganych do uzyskiwania dostępu do punktów końcowych REST centrum powiadomień i wysyłania powiadomień

1. Analizowanie parametrów połączenia
2. Generowanie tokenu autoryzacji
3. Wysyłanie powiadomienia przy użyciu interfejsu API HTTP REST

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

Oto główna klasa implementująca klienta, którego konstruktor analizuje parametry połączenia:

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

### <a name="create-security-token"></a>Tworzenie tokenu zabezpieczającego

Szczegóły tworzenia tokenu zabezpieczającego są dostępne [tutaj](https://msdn.microsoft.com/library/dn495627.aspx).
Dodaj następujące metody `NotificationHub` do klasy, aby utworzyć token na podstawie identyfikatora URI bieżącego żądania i poświadczeń wyodrębnionych z ciągu połączenia.

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

### <a name="send-a-notification-using-http-rest-api"></a>Wysyłanie powiadomienia przy użyciu interfejsu API HTTP REST

Najpierw pozwól użyć zdefiniować klasę reprezentującą powiadomienie.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
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

Ta klasa jest kontenerem dla treści powiadomień natywnych lub zestaw właściwości powiadomienia szablonu, zestaw nagłówków, który zawiera format (natywna platforma lub szablon) i właściwości specyficzne dla platformy (takie jak właściwość wygasania firmy Apple i nagłówki WNS).

Wszystkie dostępne opcje można znaleźć w [dokumentacji interfejsów API rest aplikacji notification hubs](https://msdn.microsoft.com/library/dn495827.aspx) i formatach określonych platform powiadomień.

Teraz z tej klasy, napisz metody `NotificationHub` wysyłania powiadomień wewnątrz klasy.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
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
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

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

Te metody wysyłają żądanie HTTP POST do punktu końcowego /messages centrum powiadomień z poprawną treścią i nagłówkami do wysyłania powiadomień.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Używanie właściwości debugowania w celu włączenia szczegółowego rejestrowania

Włączenie właściwości debugowania podczas inicjowania Centrum powiadomień zapisuje szczegółowe informacje rejestrowania dotyczące zrzutu żądania i odpowiedzi HTTP, a także szczegółowy wynik wysyłania wiadomości powiadomień.
[Notification Hubs TestSend Właściwość](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) zwraca szczegółowe informacje o wyniku wysyłania powiadomień.
Aby go użyć - zainicjować przy użyciu następującego kodu:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Adres URL http żądania wysyłania żądania powiadomień zostanie dołączony z ciągiem zapytania "test".

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Ukończ samouczek

Teraz możesz ukończyć samouczek Wprowadzenie, wysyłając powiadomienie z zaplecza Pythona.

Inicjowanie klienta Centrów powiadomień (zastąp parametry połączenia i nazwę koncentratora zgodnie z instrukcjami w [samouczku Wprowadzenie):]

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Następnie dodaj kod wysyłania w zależności od docelowej platformy mobilnej. W tym przykładzie dodano również metody wyższego poziomu, aby umożliwić wysyłanie powiadomień opartych na platformie, na przykład send_windows_notification dla okien; send_apple_notification (dla jabłek) itp.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Sklep Windows i Windows Phone 8.1 (inne niż Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>System Windows Phone 8.0 i 8.1 Silverlight

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

### <a name="kindle-fire"></a>Rozpakuj ogień

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

Uruchomienie kodu języka Python powinno spowodować wyświetlenie powiadomienia na urządzeniu docelowym.

## <a name="examples"></a>Przykłady

### <a name="enabling-the-debug-property"></a>Włączanie `debug` właściwości

Po włączeniu flagi debugowania podczas inicjowania NotificationHub, zobaczysz szczegółowe żądanie HTTP i zrzut odpowiedzi, a także NotificationOutcome, takie jak następujące, gdzie można zrozumieć, jakie nagłówki HTTP są przekazywane w żądaniu i jaka była odpowiedź HTTP otrzymane z Centrum powiadomień:

![][1]

Zobaczysz szczegółowy wynik Centrum powiadomień na przykład.

- po pomyślnym wysłaniu wiadomości do usługi powiadomień wypychanych.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Jeśli nie znaleziono żadnych celów dla jakiegokolwiek powiadomienia push, prawdopodobnie zobaczysz następujące dane wyjściowe jako odpowiedź (co wskazuje, że nie znaleziono żadnych rejestracji w celu dostarczenia powiadomienia prawdopodobnie dlatego, że rejestracje miały pewne niedopasowane tagi)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Emituj wyskakujące powiadomienie do systemu Windows

Zwróć uwagę na nagłówki, które są wysyłane podczas wysyłania powiadomienia wyskakującego do klienta systemu Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Wyślij powiadomienie określające znacznik (lub wyrażenie tagu)

Zwróć uwagę na nagłówek HTTP tagów, który zostanie dodany do żądania HTTP (w poniższym przykładzie powiadomienie jest wysyłane tylko do rejestracji z ładunkiem "sportowym")

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Wysyłanie powiadomień określających wiele tagów

Zwróć uwagę, jak zmienia się nagłówek HTTP znaczników podczas wysyłania wielu tagów.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Powiadomienie z szablonem

Należy zauważyć, że format nagłówek HTTP zmienia się i ładuwek treści jest wysyłany jako część treści żądania HTTP:

**Po stronie klienta - zarejestrowany szablon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Po stronie serwera - wysyłanie ładunku:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć klienta usługi Python REST dla centrów powiadomień. W tym miejscu można wykonać następujące czynności:

- Pobierz pełną [próbkę otoki Python REST], która zawiera cały kod w tym artykule.
- Kontynuuj poznawanie funkcji tagowania centrów powiadomień w [samouczku Breaking News]
- Kontynuuj poznawanie funkcji Szablony centrów powiadomień w [samouczku Lokalizowanie wiadomości]

<!-- URLs -->
[Przykład otoki PYTHON REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Wprowadzenie]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Samouczek Breaking News]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Samouczek Lokalizowanie wiadomości]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
