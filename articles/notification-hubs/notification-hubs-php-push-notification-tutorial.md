---
title: Jak korzystać z usługi Azure Notification Hubs z PHP
description: Dowiedz się, jak korzystać z usługi Azure Notification Hubs z zaplecza PHP.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9a77a9d9c8b2d71197089f66d81e07d56c780e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263850"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Jak korzystać z Centrów powiadomień z PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostęp do wszystkich funkcji Centrów powiadomień można uzyskać z zaplecza Java/PHP/Ruby za pomocą interfejsu REST centrum powiadomień, zgodnie z opisem w temacie [MsDN Notification Hubs REST API](https://msdn.microsoft.com/library/dn223264.aspx).

W tym temacie pokazujemy, jak:

* Tworzenie klienta REST dla funkcji Centrów powiadomień w PHP;
* Postępuj zgodnie [z samouczkiem Wprowadzenie](notification-hubs-ios-apple-push-notification-apns-get-started.md) dla wybranej platformy mobilnej, implementując część zaplecza w PHP.

## <a name="client-interface"></a>Interfejs klienta

Główny interfejs klienta może zapewnić te same metody, które są dostępne w [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx), który pozwala bezpośrednio przetłumaczyć wszystkie samouczki i przykłady obecnie dostępne w tej witrynie i wniesione przez społeczność w Internecie.

Cały kod dostępny w próbce otoki PHP REST można znaleźć w [próbce otoki PHP REST.]

Na przykład, aby utworzyć klienta:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Aby wysłać powiadomienie natywne dla systemu iOS:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Wdrażanie

Jeśli jeszcze tego nie zrobiłeś, wykonaj [samouczek Wprowadzenie] do ostatniej sekcji, w której musisz zaimplementować wewnętrznej bazy danych.
Ponadto, jeśli chcesz, możesz użyć kodu z [próbki otoki PHP REST] i przejść bezpośrednio do sekcji [Wykonaj samouczek.](#complete-tutorial)

Wszystkie szczegóły do wdrożenia pełnej otoki REST można znaleźć na [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano implementację PHP głównych kroków wymaganych do uzyskania dostępu do punktów końcowych REST centrum powiadomień:

1. Analizowanie parametrów połączenia
2. Generowanie tokenu autoryzacji
3. Wykonywanie połączenia HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

Oto główna klasa implementująca klienta, którego konstruktor, który analizuje parametry połączenia:

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

### <a name="create-a-security-token"></a>Tworzenie tokenu zabezpieczającego

Informacje na temat [tworzenia tokenu zabezpieczającego sygnatury dostępu Współdzielonego](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)można znaleźć w dokumentacji platformy Azure.

Dodaj `generateSasToken` metodę do `NotificationHub` klasy, aby utworzyć token na podstawie identyfikatora URI bieżącego żądania i poświadczeń wyodrębnionych z ciągu połączenia.

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

### <a name="send-a-notification"></a>Wysyłanie powiadomienia

Najpierw zdefiniujmy klasę reprezentującą powiadomienie.

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

Ta klasa jest kontenerem dla treści powiadomień natywnych lub zestaw właściwości w przypadku powiadomienia szablonu i zestaw nagłówków, który zawiera format (natywną platformę lub szablon) i właściwości specyficzne dla platformy (takie jak właściwość wygasania firmy Apple i WNS nagłówków).

Wszystkie dostępne opcje można znaleźć w [dokumentacji interfejsów API rest aplikacji notification hubs](https://msdn.microsoft.com/library/dn495827.aspx) i formatach określonych platform powiadomień.

Uzbrojeni w tę klasę, możemy teraz napisać metody `NotificationHub` wysyłania powiadomień wewnątrz klasy:

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

Powyższe metody wysyłają żądanie HTTP `/messages` POST do punktu końcowego centrum powiadomień z poprawną treścią i nagłówkami, aby wysłać powiadomienie.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Ukończ samouczek

Teraz możesz ukończyć samouczek Rozpocznij, wysyłając powiadomienie z zaplecza PHP.

Inicjowanie klienta Centrów powiadomień (zastąp parametry połączenia i nazwę koncentratora zgodnie z instrukcjami w [samouczku Wprowadzenie):]

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Następnie dodaj kod wysyłania w zależności od docelowej platformy mobilnej.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Sklep Windows i Windows Phone 8.1 (inne niż Silverlight)

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

### <a name="windows-phone-80-and-81-silverlight"></a>System Windows Phone 8.0 i 8.1 Silverlight

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

### <a name="kindle-fire"></a>Rozpakuj ogień

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Uruchomienie kodu PHP powinno spowodować wyświetlenie teraz powiadomienia na urządzeniu docelowym.

## <a name="next-steps"></a>Następne kroki

W tym temacie pokazano, jak utworzyć prosty klient Java REST dla centrów powiadomień. W tym miejscu można wykonać następujące czynności:

* Pobierz pełną [próbkę otoki PHP REST,]która zawiera cały kod powyżej.
* Kontynuuj poznawanie funkcji tagowania centrów powiadomień w samouczku [Breaking News tutorial]
* Dowiedz się więcej o wypychaniu powiadomień do poszczególnych użytkowników w [Powiadom użytkowników w samouczku]

Aby uzyskać więcej informacji, zobacz także [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[Php REST próbka otoki]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Wprowadzenie]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
