---
title: Jak używać kolejek usługi Service Bus za pomocą języka PHP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure. Przykłady kodu napisane w języku PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 55eee839e24db2ad96eb635adc488e9a119c5907
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501199"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Jak używać kolejek usługi Service Bus za pomocą języka PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym samouczku dowiesz się, jak tworzyć aplikacje PHP do wysyłania komunikatów i odbiera komunikaty z kolejki usługi Service Bus. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Jeśli nie masz kolejki chcesz pracować, wykonaj czynności opisane w [użycia usługi Azure portal można utworzyć kolejki usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.
    1. Przeczytaj szybkiego **Przegląd** usługi Service Bus **kolejek**. 
    2. Tworzenie usługi Service Bus **przestrzeni nazw**. 
    3. Pobierz **parametry połączenia**. 

        > [!NOTE]
        > Utworzysz **kolejki** w przestrzeni nazw usługi Service Bus przy użyciu języka PHP w ramach tego samouczka. 
3. [Zestaw Azure SDK dla środowiska PHP](../php-download-sdk.md)

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem do tworzenia aplikacji w języku PHP, który uzyskuje dostęp do usługi obiektów Blob platformy Azure jest odwoływanie się do klas w [zestawu Azure SDK dla języka PHP](../php-download-sdk.md) z w obrębie kodu. Można użyć dowolnego narzędzia programistyczne do tworzenia aplikacji lub Notatnik.

> [!NOTE]
> Instalacja PHP musi mieć również [rozszerzenia OpenSSL](https://php.net/openssl) zainstalowane i włączone.

W tym przewodniku użyje funkcji usługi, które mogą być wywoływane w ramach aplikacji PHP lokalnie lub w kodzie, w ramach roli sieci web platformy Azure, rola procesu roboczego lub witryny sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klienta usługi Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Aby korzystać z kolejki usługi Service Bus interfejsów API, wykonaj następujące czynności:

1. Odwołanie do automatycznej ładowarki pliku przy użyciu [require_once] [ require_once] instrukcji.
2. Odwoływać się do dowolnej klasy, których można użyć.

Poniższy przykład pokazuje, jak to plik automatycznej ładowarki i odwołania `ServicesBuilder` klasy.

> [!NOTE]
> W tym przykładzie (i inne przykłady w tym artykule) przyjęto założenie, że zainstalowano biblioteki klienckie PHP na platformie Azure za pośrednictwem Composer (kompozytor). Po zainstalowaniu bibliotek ręcznie lub jako pakiet GRUSZKI, użytkownik musi odwoływać się **WindowsAzure.php** automatycznej ładowarki pliku.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcji są zawsze wyświetlane, ale są wywoływane tylko klasy konieczne na przykład do wykonania.

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Do utworzenia wystąpienia klienta usługi Service Bus, musisz mieć prawidłowe parametry połączenia w następującym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` ma zazwyczaj format `[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolny klient usługi platformy Azure, należy użyć `ServicesBuilder` klasy. Możesz:

* Przekaż parametry połączenia do niego bezpośrednio.
* Użyj **CloudConfigurationManager (CCM)** do sprawdzenia wiele źródeł zewnętrznych dla parametrów połączenia:
  * Domyślnie pochodzi z obsługą jednego źródła zewnętrznego — zmienne środowiskowe
  * Można dodać nowe źródła, rozszerzając `ConnectionStringSource` klasy

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki
Można wykonywać operacje zarządzania kolejkami usługi Service Bus za pośrednictwem `ServiceBusRestProxy` klasy. A `ServiceBusRestProxy` obiekt jest konstruowany przy użyciu `ServicesBuilder::createServiceBusService` metoda fabryki parametrami odpowiednie połączenie, który hermetyzuje tokenu uprawnieniami do zarządzania nim.

Poniższy przykład pokazuje, jak utworzyć wystąpienie `ServiceBusRestProxy` i wywołać `ServiceBusRestProxy->createQueue` utworzyć kolejkę o nazwie `myqueue` w ramach `MySBNamespace` przestrzeni nazw usługi:

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
> Możesz użyć `listQueues` metody `ServiceBusRestProxy` obiekty do sprawdzenia, czy kolejka o określonej nazwie już istnieje w przestrzeni nazw.
> 
> 

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki usługi Service Bus, wywołania aplikacji `ServiceBusRestProxy->sendQueueMessage` metody. Poniższy kod pokazuje, jak można wysłać wiadomości do `myqueue` kolejki utworzone wcześniej w ramach `MySBNamespace` przestrzeni nazw usługi.

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

Komunikaty wysłane do (i otrzymane z) usługi Service Bus kolejki są wystąpieniami [BrokeredMessage] [ BrokeredMessage] klasy. [BrokeredMessage] [ BrokeredMessage] obiekty mają zestaw standardowych metod i właściwości, które są używane do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ta górny limit na rozmiar kolejki jest 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Najlepszym sposobem na odbieranie komunikatów z kolejki jest użycie `ServiceBusRestProxy->receiveQueueMessage` metody. Komunikaty mogą być odbierane w dwóch różnych trybach: [*ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode) i [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). Ustawienie domyślne to **PeekLock**.

Korzystając z [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) trybie odbieranie jest operacją pojedynczego zrzutu; oznacza to, gdy Usługa Service Bus odbiera żądanie odczytu komunikatu w kolejce, jego oznacza komunikat jako wykorzystany i zwraca go do aplikacji. Tryb [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ usługi Service Bus będą oznaczyła komunikat jako wykorzystany, a następnie, gdy aplikacja ponownie uruchamia i ponownie rozpocznie korzystanie z komunikatów, pominie utracony komunikat, który został wykorzystany przed awarią.

W domyślnym [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) trybie odebranie komunikatu staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy Usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go, aby uniemożliwić innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez przekazanie odebranego komunikatu do `ServiceBusRestProxy->deleteMessage`. Gdy Usługa Service Bus widzi `deleteMessage` wywołanie, wówczas Oznacz komunikat jako wykorzystany i usuń go z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać komunikat przy użyciu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) mode (tryb domyślny).

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

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metody dla odebranego komunikatu (zamiast `deleteMessage` metody). Spowoduje to odblokowanie komunikatu w kolejce i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus zostanie automatycznie odblokować wiadomości i ułatwiają dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed `deleteMessage` wystawić żądania, a następnie komunikat zostanie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *co najmniej raz* oznacza, że przetwarzanie; każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje dwukrotnego przetwarzania, zalecana jest następnie dodając dodatkową logikę do aplikacji do obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu `getMessageId` metoda wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz [kolejki, tematy i subskrypcje] [ Queues, topics, and subscriptions] Aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, odwiedź również [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


