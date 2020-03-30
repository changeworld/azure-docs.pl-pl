---
title: Jak korzystać z kolejek usługi Azure Service Bus z PHP
description: W tym samouczku dowiesz się, jak tworzyć aplikacje PHP do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Service Bus.
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
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760695"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Szybki start: jak korzystać z kolejek usługi Service Bus z PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym samouczku dowiesz się, jak tworzyć aplikacje PHP do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Service Bus. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Jeśli nie masz kolejki do pracy, wykonaj kroki w [witrynie Azure portal, aby utworzyć kolejkę usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.
    1. Przeczytaj krótki **przegląd** **kolejek**usługi Service Bus . 
    2. Tworzenie obszaru **nazw**usługi Service Bus . 
    3. Pobierz **ciąg połączenia**. 

        > [!NOTE]
        > **Kolejkę** utworzysz w obszarze nazw usługi Service Bus przy użyciu php w tym samouczku. 
3. [Zestaw Azure SDK dla środowiska PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem dla tworzenia aplikacji PHP, która uzyskuje dostęp do usługi Azure Blob jest odwoływanie się do klas w [usłudze Azure SDK dla PHP](https://github.com/Azure/azure-sdk-for-php) z poziomu kodu. Do utworzenia aplikacji można użyć dowolnych narzędzi programistycznych lub Notatnika.

> [!NOTE]
> Instalacja PHP musi być również zainstalowana i włączona [rozszerzeń OpenSSL.](https://php.net/openssl)

W tym przewodniku użyjesz funkcji usługi, które można wywołać z poziomu aplikacji PHP lokalnie lub w kodzie uruchomionym w roli sieci Web platformy Azure, roli procesu roboczego lub witrynie sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klientów platformy Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Aby użyć interfejsów API kolejki usługi Service Bus, wykonaj następujące czynności:

1. Odwołanie się do pliku automatycznego ładowania przy użyciu [instrukcji require_once.][require_once]
2. Odwoływać się do wszystkich klas, które można użyć.

W poniższym przykładzie pokazano, jak dołączyć `ServicesBuilder` plik automatycznego ładowania i odwołać się do klasy.

> [!NOTE]
> W tym przykładzie (i innych przykładach w tym artykule) przyjęto założenie, że zainstalowano biblioteki klienta PHP dla platformy Azure za pośrednictwem programu Composer. Jeśli biblioteki zostały zainstalowane ręcznie lub jako pakiet PEAR, należy odwołać się do pliku autoloadera **WindowsAzure.php.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcja będzie zawsze wyświetlana, ale odwołuje się tylko do klas niezbędnych do wykonania.

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Aby utworzyć wystąpienie klienta usługi Service Bus, musisz najpierw mieć prawidłowy ciąg połączenia w tym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` jest zazwyczaj format `[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolnego klienta usługi platformy `ServicesBuilder` Azure, należy użyć klasy. Możesz:

* Przekaż ciąg połączenia bezpośrednio do niego.
* Użyj **cloudconfigurationManager (CCM),** aby sprawdzić wiele źródeł zewnętrznych dla ciągu połączenia:
  * Domyślnie jest wyposażony w obsługę jednego źródła zewnętrznego - zmiennych środowiskowych
  * Można dodać nowe źródła, rozszerzając `ConnectionStringSource` klasę

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Tworzenie kolejki
Operacje zarządzania dla kolejek usługi `ServiceBusRestProxy` Service Bus można wykonywać za pośrednictwem klasy. Obiekt `ServiceBusRestProxy` jest konstruowany `ServicesBuilder::createServiceBusService` za pomocą metody fabrycznej z odpowiednim ciągiem połączenia, który hermetyzuje uprawnienia tokenu do zarządzania nim.

W poniższym przykładzie pokazano, `ServiceBusRestProxy` jak `ServiceBusRestProxy->createQueue` utworzyć wystąpienie `myqueue` i `MySBNamespace` wywołanie kolejki o nazwie w obszarze nazw usługi:

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
> Za pomocą `listQueues` metody `ServiceBusRestProxy` na obiektach można sprawdzić, czy kolejka o określonej nazwie już istnieje w obszarze nazw.
> 
> 

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać wiadomość do kolejki usługi Service `ServiceBusRestProxy->sendQueueMessage` Bus, aplikacja wywołuje tę metodę. Poniższy kod pokazuje, jak wysłać wiadomość do `myqueue` `MySBNamespace` kolejki wcześniej utworzonej w obszarze nazw usługi.

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

Wiadomości wysyłane do (i odebrane z) kolejek usługi Service Bus są wystąpieniami [brokeredMessage][BrokeredMessage] klasy. [BrokeredMessage][BrokeredMessage] obiekty mają zestaw standardowych metod i właściwości, które są używane do przechowywania właściwości specyficzne dla aplikacji niestandardowych i treść dowolnych danych aplikacji.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten górny limit rozmiaru kolejki wynosi 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki

Najlepszym sposobem odbierania wiadomości z kolejki `ServiceBusRestProxy->receiveQueueMessage` jest użycie metody. Wiadomości mogą być odbierane w dwóch różnych trybach: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) i [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). Ustawienie domyślne to **PeekLock**.

Podczas korzystania [z trybu ReceiveAndDelete,](/dotnet/api/microsoft.servicebus.messaging.receivemode) receive jest operacją pojedynczego strzału; oznacza to, że gdy usługa Service Bus odbiera żądanie odczytu wiadomości w kolejce, oznacza komunikat jako używane i zwraca go do aplikacji. Tryb [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ usługa Service Bus oznaczy komunikat jako zużyty, po ponownym uruchomieniu aplikacji i ponownym rozpoczęciu korzystania z wiadomości, zostanie wyświetlony komunikat, który został zużyty przed awarią.

W domyślnym trybie [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) odbieranie wiadomości staje się operacją dwuetapową, która umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajdzie następną wiadomość do zużywania, blokuje go, aby uniemożliwić innym odbiorcom odbieranie go, a następnie zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości przez aplikację (lub niezawodnym jej magazynowaniu) kończy drugi etap procesu odbierania, przekazując odebraną wiadomość do `ServiceBusRestProxy->deleteMessage`. Gdy usługa Service `deleteMessage` Bus zobaczy wywołanie, oznaczy wiadomość jako zużytą i usunie ją z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać wiadomość przy użyciu trybu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (tryb domyślny).

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

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie jest w stanie przetworzyć `unlockMessage` wiadomości z jakiegoś powodu, a `deleteMessage` następnie można wywołać metodę na odebranej wiadomości (zamiast metody). Spowoduje to, że usługa Service Bus, aby odblokować wiadomość w kolejce i udostępnić go do odebrania ponownie, za pomocą tej samej aplikacji zużywającej lub przez inną aplikację zużywającą.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce, a jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokuje wiadomość i ją ustawi. można otrzymać ponownie.

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed wystawieniem `deleteMessage` żądania, komunikat zostanie ponownie dostarczona do aplikacji po ponownym uruchomieniu. Jest to często nazywane *co najmniej raz* przetwarzania; oznacza to, że każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowanego przetwarzania, zaleca się dodanie dodatkowej logiki do aplikacji do obsługi dostarczania zduplikowanych wiadomości. Jest to często osiągane przy użyciu `getMessageId` metody wiadomości, która pozostaje stała w próbach dostarczenia.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy kolejek usługi Service Bus, zobacz [Kolejki, tematy i subskrypcje,][Queues, topics, and subscriptions] aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, odwiedź również [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


