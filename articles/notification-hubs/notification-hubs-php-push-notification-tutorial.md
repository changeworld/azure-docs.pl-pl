---
title: Jak używać usługi Notification Hubs za pomocą języka PHP
description: Dowiedz się, jak używać usługi Azure Notification Hubs z zaplecza PHP.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 930da7cca312ac6233b337dd7ddac478c3bbee7b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776043"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Jak używać usługi Notification Hubs za pomocą języka PHP
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostęp do wszystkich funkcji centra powiadomień z zaplecza Ruby-Java/PHP przy użyciu interfejsu REST Centrum powiadomień, zgodnie z opisem w temacie MSDN [interfejsów API REST centra powiadomień](http://msdn.microsoft.com/library/dn223264.aspx).

W tym temacie zostanie przedstawiony sposób:

* Tworzenie klienta REST dla funkcji usługi Notification Hubs w języku PHP;
* Postępuj zgodnie z [samouczku Get](notification-hubs-ios-apple-push-notification-apns-get-started.md) dla platformy przenośne wyboru wdrażanie części zaplecza w kodzie PHP.

## <a name="client-interface"></a>Interfejs klienta
Interfejs klienta głównego zapewniają te same metody, które są dostępne w [.NET SDK centra powiadomień](http://msdn.microsoft.com/library/jj933431.aspx), co pozwala na bezpośrednie przetłumaczenie samouczki i przykłady, które są obecnie dostępne w tej witrynie i przekazanych przez społeczności w Internecie.

Możesz znaleźć kod dostępne w [próbki otoki PHP REST].

Na przykład można utworzyć klienta:

    $hub = new NotificationHub("connection string", "hubname");    

Aby wysłać iOS natywnych powiadomień:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Wdrażanie
Jeśli tak, nie jest jeszcze nie, wykonaj [samouczku Get] w górę do ostatniej sekcji, w którym należy implementować zaplecza.
Ponadto jeśli chcesz służy kod z [próbki otoki PHP REST] i przejść bezpośrednio do [Ukończ samouczek](#complete-tutorial) sekcji.

Wszystkie szczegóły, aby zaimplementować pełne otoki REST można znaleźć w [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano wykonania PHP główne kroki wymagane do uzyskania dostępu punkty końcowe REST centra powiadomień:

1. Analizowanie parametrów połączenia
2. Wygeneruj token autoryzacji
3. Wykonaj wywołanie HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia
W tym miejscu jest główna klasa implementacji klienta, którego konstruktora, która analizuje parametry połączenia:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Utwórz token zabezpieczeń
Szczegóły dotyczące tworzenia tokenu zabezpieczeń są dostępne [tutaj](http://msdn.microsoft.com/library/dn495627.aspx).
Następująca metoda musi być dodany do **NotificationHub** do utworzenia tokenu na podstawie identyfikatora URI bieżącego żądania i poświadczenia wyodrębniony z ciągu połączenia.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Wyślij powiadomienie
Najpierw należy zdefiniować Daj nam Klasa reprezentująca powiadomienie.

    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }

Ta klasa jest kontenerem dla treści natywnych powiadomień, lub zbiór właściwości, w przypadku szablonu powiadomienia i zestaw nagłówków, zawiera formacie (native platformy lub szablonu) i właściwości specyficzne dla platformy (na przykład właściwość wygaśnięcia firmy Apple i WNS nagłówki).

Zapoznaj się [dokumentacja interfejsów API REST centra powiadomień](http://msdn.microsoft.com/library/dn495827.aspx) i formaty na platformach powiadomienia określonych dla wszystkich dostępnych opcji.

Dzięki tej klasy, możemy teraz zapisać wysyłania powiadomień metody wewnątrz **NotificationHub** klasy.

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

Powyżej metod Wyślij żądanie HTTP POST do punktu końcowego /messages Centrum powiadomień, z poprawną treści i nagłówków, aby wysłać powiadomienie.

## <a name="complete-tutorial"></a>Ukończ samouczek
Teraz możesz ukończyć Samouczek wprowadzający przez wysyłanie powiadomień z zaplecza PHP.

Inicjowanie klienta usługi Notification Hubs (zastąpić nazwę Centrum i parametry połączenia, zgodnie z instrukcją w [samouczku Get]):

    $hub = new NotificationHub("connection string", "hubname");    

Następnie dodaj kod wysyłania, w zależności od platform przenośnych docelowych.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Sklep Windows i Windows Phone 8.1 (z systemem innym niż platformy Silverlight)
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 i 8.1 Silverlight
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Uruchamianie kodu PHP powinny być teraz powiadomienie znajdujących się na urządzeniu docelowym.

## <a name="next-steps"></a>Następne kroki
W tym temacie firma Microsoft pokazano, jak utworzyć prosty Java REST klienta usługi Notification Hubs. W tym miejscu można wykonywać następujące czynności:

* Pobierz pełny [próbki otoki PHP REST], który zawiera kod powyżej.
* Kontynuować szkoleniowe dotyczące usługi Notification Hubs znakowanie funkcji [samouczka wiadomości krytyczne]
* Dowiedz się więcej o wypychanie powiadomień do poszczególnych użytkowników [Powiadom użytkowników samouczka]

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka PHP](/develop/php/).

[próbki otoki PHP REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[samouczku Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/

