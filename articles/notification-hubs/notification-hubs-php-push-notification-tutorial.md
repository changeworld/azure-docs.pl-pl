---
title: Jak używać usługi Notification Hubs za pomocą języka PHP
description: Dowiedz się, jak używać usługi Azure Notification Hubs z zapleczem PHP.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872191"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Jak używać usługi Notification Hubs za pomocą języka PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostęp do wszystkich funkcji usługi Notification Hubs z zaplecza Java/PHP/języka Ruby przy użyciu interfejsu REST Centrum powiadomień, zgodnie z opisem w temacie w witrynie MSDN [interfejsów API REST usługi Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx).

W tym temacie pokazano, jak:

* Tworzenie klienta REST dla funkcji usługi Notification Hubs w języku PHP;
* Postępuj zgodnie z [samouczek z wprowadzeniem](notification-hubs-ios-apple-push-notification-apns-get-started.md) na wybraną platformę przenośnych, wdrażanie część zaplecza w języku PHP.

## <a name="client-interface"></a>Interfejs klienta

Interfejs klienta głównego może zapewnić te same metody, które są dostępne w [zestawu SDK .NET Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx), który pozwala na bezpośrednie przetłumaczenie samouczków i przykładów, które są obecnie dostępne w tej witrynie i pochodzącego z danego Społeczność w Internecie.

Dostępne w kod możesz znaleźć [przykład otoka interfejsu REST PHP].

Na przykład można utworzyć klienta:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Aby wysłać dla systemu iOS natywnych powiadomień:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Wdrażanie

Jeśli jeszcze nie zrobiono, wykonaj [samouczek z wprowadzeniem] w górę do ostatniej sekcji, w którym trzeba zaimplementować wewnętrznej bazy danych.
Ponadto, jeśli chcesz, możesz użyć kodu z [przykład otoka interfejsu REST PHP] i przejdź bezpośrednio do [ukończenia tego samouczka](#complete-tutorial) sekcji.

Poznaj wszystkie szczegóły implementacji pełnego otoki REST znajduje się na [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano implementację PHP główne kroki wymagane do uzyskania dostępu punkty końcowe REST centrów powiadomień:

1. Analizowanie parametrów połączenia
2. Wygeneruj token autoryzacji
3. Wykonywać wywołania HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

W tym miejscu jest główna klasa implementacji klienta, której Konstruktor, który analizuje ciąg połączenia:

    ```php
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
    ```

### <a name="create-a-security-token"></a>Utworzyć token zabezpieczający

Dokumentacji platformy Azure, aby uzyskać informacje o sposobie [Tworzenie tokenu sygnatury dostępu Współdzielonego zabezpieczeń](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Dodaj `generateSasToken` metody `NotificationHub` klasy do utworzenia tokenu na podstawie identyfikatora URI bieżącego żądania i poświadczenia wyodrębnione z parametrów połączenia.

    ```php
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
    ```

### <a name="send-a-notification"></a>Wyślij powiadomienie

Najpierw należy zdefiniować Daj nam Klasa reprezentująca powiadomienie.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Ta klasa jest kontenerem dla treści natywnych powiadomień, lub zbiór właściwości, w przypadku powiadomienia szablonu i zestaw nagłówków, zawierającą formatu (platformy natywnej lub szablonu) i właściwości specyficzne dla platformy (na przykład właściwości wygasania firmy Apple lub usługi WNS nagłówki).

Zapoznaj się [dokumentacji interfejsów API REST usługi Notification Hubs](https://msdn.microsoft.com/library/dn495827.aspx) na platformach powiadomień określone formaty i dla wszystkich dostępnych opcji.

Korzystając z tej klasy, możemy teraz zapisać wysyłania powiadomień metody wewnątrz `NotificationHub` klasy:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
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
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Powyższych metod wysyłania żądania HTTP POST do `/messages` punktu końcowego Centrum powiadomień, z treści poprawne i nagłówki, aby wysłać powiadomienie.

## <a name="complete-tutorial"></a>Ukończ samouczek

Teraz możesz ukończyć Samouczek wprowadzający, wysyłając powiadomienia z poziomu zaplecza PHP.

Inicjowanie klienta usługi Notification Hubs (zastąpić nazwę Centrum i parametry połączenia, zgodnie z instrukcją w [samouczek z wprowadzeniem]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Następnie dodaj Wyślij kod w zależności od docelowej platformy mobilnej.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store i Windows Phone 8.1 (bez użycia platformy Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 i 8.1 Silverlight

    ```php
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
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Uruchamianie kodu w języku PHP powinny być teraz powiadomienie, które pojawiają się na urządzeniu docelowym.

## <a name="next-steps"></a>Następne kroki

W tym temacie pokazaliśmy, jak utworzyć proste klienta Java REST dla usługi Notification Hubs. W tym miejscu możesz wykonywać następujące czynności:

* Pobierz pełną [przykład otoka interfejsu REST PHP], która zawiera kod powyżej.
* Kontynuuj szkoleniowe dotyczące usługi Notification Hubs funkcji tagowania w [samouczek istotne wiadomości]
* Dowiedz się więcej o wypychanie powiadomień do poszczególnych użytkowników w [powiadamianie użytkowników samouczek]

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[Przykład otoka interfejsu REST PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Samouczek z wprowadzeniem]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
