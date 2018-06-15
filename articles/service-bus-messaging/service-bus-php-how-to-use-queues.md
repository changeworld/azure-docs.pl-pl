---
title: Jak używać kolejek usługi Service Bus za pomocą języka PHP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure. Przykłady kodu napisane w języku PHP.
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 3514812f7f087582035dad5d9a4d620652aa4da9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868326"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Jak używać kolejek usługi Service Bus za pomocą języka PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ten przewodnik przedstawia, jak używać kolejek usługi Service Bus. Przykłady są napisane w PHP i użyj [zestaw Azure SDK for PHP](../php-download-sdk.md). Omówione scenariusze obejmują **tworzenie kolejek**, **wysyłania i odbierania wiadomości**, i **usuwanie kolejek**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji PHP
Jedynym wymaganiem dla tworzenia aplikacji PHP, który uzyskuje dostęp do usługi obiektów Blob platformy Azure jest odwołanie do klasy w [zestaw Azure SDK for PHP](../php-download-sdk.md) od w kodzie. Można użyć narzędzia do programowania do tworzenia aplikacji, lub Notatnik.

> [!NOTE]
> Instalację PHP musi mieć również [rozszerzenie biblioteki OpenSSL](http://php.net/openssl) zainstalowany i włączony.
> 
> 

W tym przewodniku użyje funkcji usługi, które mogą być wywoływane w ramach aplikacji PHP lokalnie lub w kodzie działających w roli sieci web platformy Azure, roli procesu roboczego lub witryny sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz klienta usługi Azure bibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Skonfigurować aplikację do użycia z magistralą usług
Aby korzystać z kolejki usługi Service Bus interfejsów API, wykonaj następujące czynności:

1. Odwołanie przy użyciu pliku automatycznej ładowarki [require_once] [ require_once] instrukcji.
2. Odwoływać się do wszystkich klas, których może używać.

Poniższy przykład pokazuje, jak dołączyć plik automatycznej ładowarki i odwołanie `ServicesBuilder` klasy.

> [!NOTE]
> W tym przykładzie (i inne przykłady w tym artykule) przyjęto założenie, że zainstalowano bibliotek klienckich PHP na platformie Azure za pośrednictwem Composer. Jeśli zainstalowano bibliotek ręcznie lub jako pakiet GRUSZKOWA, należy wskazać **WindowsAzure.php** automatycznej ładowarki pliku.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcji są zawsze wyświetlane, ale odwołuje się tylko do klas, które są konieczne na przykład do wykonania.

## <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie usługi Service Bus
Można utworzyć klienta usługi Service Bus, najpierw musi mieć prawidłowe parametry połączenia w następującym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` ma zazwyczaj format `[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolnego klienta usługi Azure, należy użyć `ServicesBuilder` klasy. Możesz:

* Parametry połączenia należy przekazać bezpośrednio do niego.
* Użyj **CloudConfigurationManager (CCM)** do sprawdzenia wiele źródeł zewnętrznych ciągu połączenia:
  * Domyślnie pochodzi on z obsługą jednego źródła zewnętrznego — zmienne środowiskowe
  * Można dodać nowego źródła rozszerzając `ConnectionStringSource` — klasa

Przykłady przedstawione w tym miejscu ciąg połączenia jest przekazywany bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki
Mogą wykonywać operacje zarządzania kolejkami usługi Service Bus za pośrednictwem `ServiceBusRestProxy` klasy. A `ServiceBusRestProxy` obiekt jest tworzony za pomocą `ServicesBuilder::createServiceBusService` metoda fabryki z ciąg odpowiednie połączenie, który hermetyzuje tokenu uprawnieniami do zarządzania nim.

Poniższy przykład przedstawia sposób tworzenia wystąpienia `ServiceBusRestProxy` i Wywołaj `ServiceBusRestProxy->createQueue` utworzyć kolejkę o nazwie `myqueue` w `MySBNamespace` przestrzeni nazw usługi:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Można użyć `listQueues` metoda `ServiceBusRestProxy` obiektów, aby sprawdzić, czy kolejka o określonej nazwie już istnieje w przestrzeni nazw.
> 
> 

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać wiadomość do kolejki usługi Service Bus, wywołania aplikacji `ServiceBusRestProxy->sendQueueMessage` metody. Poniższy kod przedstawia sposób wysłania komunikatu do `myqueue` kolejki utworzonej wcześniej w `MySBNamespace` przestrzeni nazw usługi.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Komunikaty wysłane do (i odebrane z) usługi Service Bus kolejki są wystąpieniami klasy [BrokeredMessage] [ BrokeredMessage] klasy. [BrokeredMessage] [ BrokeredMessage] obiekty mają zestaw standardowych metod i właściwości, które są używane do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ta górny limit na rozmiar kolejki wynosi 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Najlepszym sposobem na odbieranie komunikatów z kolejki jest użycie `ServiceBusRestProxy->receiveQueueMessage` metody. Mogą być odbierane wiadomości w dwóch różnych trybach: [ *ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) i [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock). Ustawienie domyślne to **PeekLock**.

Korzystając z [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) trybie odbieranie jest operacją pojedynczego zrzutu; oznacza to, kiedy usługa Service Bus odbiera żądanie odczytu komunikatu w kolejce, oznacza komunikat jako wykorzystany i zwraca go do aplikacji. Tryb [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus zostanie oznaczona komunikat jako wykorzystany, a następnie po uruchomieniu i rozpocznie korzystanie z komunikatów ponownie aplikacji, pominie utracony komunikat, który został wykorzystany przed awarią.

W domyślnej [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) tryb odbieraniu wiadomości staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Kiedy Usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go, aby uniemożliwić innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez przekazanie odebranego komunikatu do `ServiceBusRestProxy->deleteMessage`. Kiedy Usługa Service Bus widzi `deleteMessage` wywołania spowoduje oznaczenie komunikat jako wykorzystany i usunąć go z kolejki.

Poniższy przykład przedstawia sposób odbierały i przetwarzały komunikat przy użyciu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) mode (tryb domyślny).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metody dla odebranego komunikatu (zamiast `deleteMessage` metody). Spowoduje to odblokowanie komunikatu w kolejce i udostępnienie go do ponownego odbioru, przez tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady wygaśnięcia (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus zostanie automatycznie odblokowanie komunikatu i stał się dostępny do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `deleteMessage` żądania, a następnie komunikat zostanie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *co najmniej raz* oznacza to, że przetwarzanie; każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje dwukrotnego przetwarzania, zaleca się następnie dodać dodatkową logikę do aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu `getMessageId` — metoda, która pozostaje stała między kolejnymi próbami dostarczenia komunikatu.

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz [kolejki, tematy i subskrypcje] [ Queues, topics, and subscriptions] Aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, odwiedź również [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


