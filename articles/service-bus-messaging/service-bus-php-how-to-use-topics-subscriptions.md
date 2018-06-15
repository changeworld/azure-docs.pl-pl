---
title: Jak używać tematów usługi Service Bus za pomocą języka PHP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tematów usługi Service Bus za pomocą języka PHP na platformie Azure.
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 4763b172375668213372e6f4f8fc87431c430e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868599"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Jak używać tematów usługi Service Bus i subskrypcji za pomocą języka PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule pokazano, jak używać tematów usługi Service Bus i subskrypcji. Przykłady są napisane w PHP i użyj [zestaw Azure SDK for PHP](../php-download-sdk.md). Omówione scenariusze obejmują **tworzenie tematów i subskrypcji**, **tworzenie filtrów subskrypcji**, **wysyłanie komunikatów do tematu**, **odbieranie komunikatów z subskrypcji**, i **usuwanie tematów i subskrypcji**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji PHP
Jedynym wymaganiem w przypadku tworzenia aplikacji PHP, który uzyskuje dostęp do usługi obiektów Blob platformy Azure jest odwoływać się do klas w [zestaw Azure SDK for PHP](../php-download-sdk.md) od w kodzie. Można użyć narzędzia do programowania do tworzenia aplikacji, lub Notatnik.

> [!NOTE]
> Instalację PHP musi mieć również [rozszerzenie biblioteki OpenSSL](http://php.net/openssl) zainstalowany i włączony.
> 
> 

W tym artykule opisano sposób użycia funkcji usługi, które można wywołać w ramach aplikacji PHP lokalnie lub w kodzie działających w roli sieci web platformy Azure, roli procesu roboczego lub witryny sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz klienta usługi Azure bibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Skonfigurować aplikację do użycia z magistralą usług
Aby użyć interfejsów API usługi Service Bus:

1. Odwołanie przy użyciu pliku automatycznej ładowarki [require_once] [ require-once] instrukcji.
2. Odwoływać się do wszystkich klas, których może używać.

Poniższy przykład pokazuje, jak dołączyć plik automatycznej ładowarki i odwołanie **ServiceBusService** klasy.

> [!NOTE]
> W tym przykładzie (i inne przykłady w tym artykule) przyjęto założenie, że zainstalowano bibliotek klienckich PHP na platformie Azure za pośrednictwem Composer. Jeśli zainstalowano bibliotek ręcznie lub jako pakiet GRUSZKOWA, należy wskazać **WindowsAzure.php** automatycznej ładowarki pliku.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcji jest zawsze widoczne, ale odwołuje się tylko do klas, które są konieczne na przykład do wykonania.

## <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie usługi Service Bus
Można utworzyć klienta usługi Service Bus, musisz najpierw mieć prawidłowe parametry połączenia w następującym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` ma zazwyczaj format `https://[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolnego klienta usługi Azure, należy użyć `ServicesBuilder` klasy. Możesz:

* Parametry połączenia należy przekazać bezpośrednio do niego.
* Użyj **CloudConfigurationManager (CCM)** do sprawdzenia wiele źródeł zewnętrznych ciągu połączenia:
  * Domyślnie pochodzi on z obsługą jednego źródła zewnętrznego — zmiennych środowiskowych.
  * Można dodać nowego źródła rozszerzając `ConnectionStringSource` klasy.

Przykłady przedstawione w tym miejscu ciąg połączenia jest przekazywany bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Tworzenie tematu
Mogą wykonywać operacje zarządzania dla tematów usługi Service Bus za pośrednictwem `ServiceBusRestProxy` klasy. A `ServiceBusRestProxy` obiekt jest tworzony za pomocą `ServicesBuilder::createServiceBusService` metoda fabryki z ciąg odpowiednie połączenie, który hermetyzuje tokenu uprawnieniami do zarządzania nim.

Poniższy przykład przedstawia sposób tworzenia wystąpienia `ServiceBusRestProxy` i Wywołaj `ServiceBusRestProxy->createTopic` utworzyć temat o nazwie `mytopic` w `MySBNamespace` przestrzeni nazw:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Można użyć `listTopics` metoda `ServiceBusRestProxy` obiektów, aby sprawdzić, czy temat o określonej nazwie już istnieje w przestrzeni nazw usługi.
> 
> 

## <a name="create-a-subscription"></a>Tworzenie subskrypcji
Subskrypcje tematu są również tworzone za pomocą `ServiceBusRestProxy->createSubscription` metody. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów przesyłany do wirtualnej kolejki subskrypcji.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Filtr **MatchAll** jest filtrem domyślnym, który jest używany, gdy podczas tworzenia nowej subskrypcji nie został określony żaden filtr. Gdy **MatchAll** filtr jest używany, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie "mysubscription" i używa domyślnej **MatchAll** filtru.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również ustawiać filtry pozwalające określić, które komunikaty wysyłane do tematu powinny znajdować się w subskrypcji określonego tematu. Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter), która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji o SqlFilters, zobacz [właściwości SqlFilter.SqlExpression][sqlfilter].

> [!NOTE]
> Każda reguła w przypadku subskrypcji przetwarza wiadomości przychodzących niezależnie, dodanie ich wiadomości wynik do subskrypcji. Ponadto każda nowa subskrypcja ma wartość domyślną **reguły** obiektu filtr, który dodaje wszystkie komunikaty z tematu do subskrypcji. Aby otrzymywać tylko komunikaty zgodny z filtrem, należy usunąć reguły domyślnej. Należy usunąć domyślną regułę przy użyciu `ServiceBusRestProxy->deleteRule` metody.
> 
> 

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` z **SqlFilter** który wybiera tylko komunikaty, które mają niestandardowy `MessageNumber` właściwości wyższej niż 3. Zobacz [wysyłanie komunikatów do tematu](#send-messages-to-a-topic) informacji o dodawanie właściwości niestandardowych do wiadomości.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Należy pamiętać, że ten kod wymaga użycia dodatkowej przestrzeni nazw: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Podobnie poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z `SqlFilter` który wybiera tylko komunikaty, które mają `MessageNumber` właściwości mniej niż 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Teraz, gdy wiadomość jest wysyłana do `mytopic` tematu, zawsze jest dostarczany do odbiorców `mysubscription` subskrypcji i selektywnie dostarczany do odbiorców mających subskrypcję `HighMessages` i `LowMessages` subskrypcji (w zależności od zawartości komunikatu).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do tematu usługi Service Bus, wywołania aplikacji `ServiceBusRestProxy->sendTopicMessage` metody. Poniższy kod przedstawia sposób wysłania komunikatu do `mytopic` wcześniej utworzony w temacie `MySBNamespace` przestrzeni nazw usługi.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami klasy [BrokeredMessage] [ BrokeredMessage] klasy. [BrokeredMessage] [ BrokeredMessage] obiekty mają zestaw właściwości standardowych i metody, a także właściwości, które mogą służyć do przechowywania niestandardowych właściwości specyficzne dla aplikacji. Poniższy przykład przedstawia sposób wysłania 5 wiadomości testowe `mytopic` wcześniej utworzony temat. `setProperty` Metoda jest używana do dodawania właściwości niestandardowych (`MessageNumber`) do każdej wiadomości. Należy pamiętać, że `MessageNumber` wartość właściwości może być różna dla każdej wiadomości (tej wartości można użyć, aby określić, które subskrypcji odbierania, jak pokazano w [Utwórz subskrypcję](#create-a-subscription) sekcji):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Ta górny limit na rozmiar tematu jest 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Najlepszym sposobem odbierania komunikatów z subskrypcji jest użycie `ServiceBusRestProxy->receiveSubscriptionMessage` metody. Mogą być odbierane wiadomości w dwóch różnych trybach: [ *ReceiveAndDelete* i *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Ustawienie domyślne to **PeekLock**.

W przypadku używania trybu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) odbieranie jest operacją pojedynczego zrzutu. Oznacza to, że kiedy usługa Service Bus odbiera żądanie odczytu komunikatu w subskrypcji, oznacza komunikat jako wykorzystywany i zwraca go do aplikacji. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * tryb jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus zostanie oznaczona komunikat jako wykorzystany, a następnie po uruchomieniu i rozpocznie korzystanie z komunikatów ponownie aplikacji, pominie utracony komunikat, który został wykorzystany przed awarią.

W domyślnej [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb odbieraniu wiadomości staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez przekazanie odebranego komunikatu do `ServiceBusRestProxy->deleteMessage`. Kiedy Usługa Service Bus widzi `deleteMessage` wywołanie, oznacza komunikat jako wykorzystany i usunąć go z kolejki.

Poniższy przykład przedstawia sposób odbierały i przetwarzały komunikat przy użyciu [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mode (tryb domyślny). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Porady: Obsługa awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metody dla odebranego komunikatu (zamiast `deleteMessage` metody). Powoduje to odblokowanie komunikatu w kolejce przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady wygaśnięcia (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i stał się dostępny do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `deleteMessage` żądania, a następnie wiadomość jest dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *co najmniej raz* oznacza to, że przetwarzanie; każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu `getMessageId` — metoda, która pozostaje stała między kolejnymi próbami dostarczenia komunikatu.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Aby usunąć tematu lub subskrypcji, użyj `ServiceBusRestProxy->deleteTopic` lub `ServiceBusRestProxy->deleteSubscripton` metod, odpowiednio. Należy pamiętać, że usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem.

Poniższy przykład przedstawia sposób usuwania tematu o nazwie `mytopic` i jego zarejestrowanych subskrypcji.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Za pomocą `deleteSubscription` metody, można usunąć subskrypcję niezależnie:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz [kolejki, tematy i subskrypcje] [ Queues, topics, and subscriptions] Aby uzyskać więcej informacji.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
