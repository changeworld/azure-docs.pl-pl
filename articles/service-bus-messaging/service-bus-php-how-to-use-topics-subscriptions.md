---
title: Jak korzystać z tematów usługi Azure Service Bus z PHP
description: W tym samouczku dowiesz się, jak używać tematów i subskrypcji usługi Azure Service Bus z aplikacji PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760678"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Szybki start: jak korzystać z tematów i subskrypcji usługi Service Bus z PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule pokazano, jak używać tematów i subskrypcji usługi Service Bus. Próbki są zapisywane w PHP i używają [narzędzia Azure SDK dla PHP](https://github.com/Azure/azure-sdk-for-php). Scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie wiadomości do tematu 
- Odbieranie wiadomości z subskrypcji
- Usuwanie tematów i subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [korzyści dla subskrybenta programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub założyć [bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Wykonaj kroki opisane w [przewodniku Szybki start: Użyj portalu Azure, aby utworzyć temat usługi Service Bus i subskrypcje tematu, aby](service-bus-quickstart-topics-subscriptions-portal.md) utworzyć **obszar nazw** usługi Service Bus i uzyskać **ciąg połączenia**.

    > [!NOTE]
    > W tym przewodniku szybki start utworzysz **temat** i **subskrypcję** tematu. **PHP** 

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem dla tworzenia aplikacji PHP, która uzyskuje dostęp do usługi Azure Blob jest odwołanie klas w [usłudze Azure SDK dla PHP](https://github.com/Azure/azure-sdk-for-php) z poziomu kodu. Do utworzenia aplikacji można użyć dowolnych narzędzi programistycznych lub Notatnika.

> [!NOTE]
> Instalacja PHP musi być również zainstalowana i włączona [rozszerzeń OpenSSL.](https://php.net/openssl)
> 
> 

W tym artykule opisano sposób używania funkcji usługi, które mogą być wywoływane w aplikacji PHP lokalnie lub w kodzie uruchomionym w roli sieci Web platformy Azure, roli procesu roboczego lub witrynie sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klientów platformy Azure

### <a name="install-via-composer"></a>Zainstaluj za pośrednictwem composer
1. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu i dodaj do niego następujący kod:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Pobierz **[composer.phar][composer-phar]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i wykonaj następujące polecenie w katalogu głównym projektu
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Aby użyć interfejsów API usługi Service Bus:

1. Odwołanie się do pliku automatycznego ładowania przy użyciu [instrukcji require_once.][require-once]
2. Odwoływać się do wszystkich klas, które można użyć.

W poniższym przykładzie pokazano, jak dołączyć plik autoloader i odwołać się do **ServiceBusService** klasy.

> [!NOTE]
> W tym przykładzie (i innych przykładach w tym artykule) przyjęto założenie, że zainstalowano biblioteki klienta PHP dla platformy Azure za pośrednictwem programu Composer. Jeśli biblioteki zostały zainstalowane ręcznie lub jako pakiet PEAR, należy odwołać się do pliku autoloadera **WindowsAzure.php.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcja jest zawsze wyświetlana, ale odwołuje się tylko do klas niezbędnych do wykonania.

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Aby utworzyć wystąpienie klienta usługi Service Bus, musisz najpierw mieć prawidłowy ciąg połączenia w tym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` jest zazwyczaj format `https://[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolnego klienta usługi platformy `ServicesBuilder` Azure, należy użyć klasy. Możesz:

* Przekaż ciąg połączenia bezpośrednio do niego.
* Użyj **cloudconfigurationManager (CCM),** aby sprawdzić wiele źródeł zewnętrznych dla ciągu połączenia:
  * Domyślnie jest wyposażony w obsługę jednego źródła zewnętrznego - zmiennych środowiskowych.
  * Możesz dodać nowe źródła, rozszerzając klasę `ConnectionStringSource`.

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Tworzenie tematu
Operacje zarządzania dla tematów usługi `ServiceBusRestProxy` Service Bus można wykonywać za pośrednictwem klasy. Obiekt `ServiceBusRestProxy` jest konstruowany `ServicesBuilder::createServiceBusService` za pomocą metody fabrycznej z odpowiednim ciągiem połączenia, który hermetyzuje uprawnienia tokenu do zarządzania nim.

W poniższym przykładzie pokazano, `ServiceBusRestProxy` jak `ServiceBusRestProxy->createTopic` utworzyć wystąpienia `mytopic` i `MySBNamespace` wywołać, aby utworzyć temat o nazwie w obszarze nazw:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
> Można użyć `listTopics` metody `ServiceBusRestProxy` na obiektach, aby sprawdzić, czy temat o określonej nazwie już istnieje w obszarze nazw usługi.
> 
> 

## <a name="create-a-subscription"></a>Tworzenie subskrypcji
Subskrypcje tematów są `ServiceBusRestProxy->createSubscription` również tworzone za pomocą metody. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów przesyłany do wirtualnej kolejki subskrypcji.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Jeśli podczas tworzenia nowej subskrypcji nie określono żadnego filtru, używany jest filtr **MatchAll** (domyślny). Gdy używany jest filtr **MatchAll,** wszystkie komunikaty opublikowane w temacie są umieszczane w kolejce wirtualnej subskrypcji. Poniższy przykład tworzy `mysubscription` subskrypcję o nazwie i używa domyślnego **filtru MatchAll.**

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
Można również ustawiać filtry pozwalające określić, które komunikaty wysyłane do tematu powinny znajdować się w subskrypcji określonego tematu. Najbardziej elastycznym typem filtru obsługiwanego przez subskrypcje jest [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), który implementuje podzbiór sql92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat sqlfilters, zobacz [SqlFilter.SqlExpression Właściwości][sqlfilter].

> [!NOTE]
> Każda reguła w subskrypcji przetwarza przychodzące wiadomości niezależnie, dodając ich komunikaty wynikowe do subskrypcji. Ponadto każda nowa subskrypcja ma domyślny **obiekt reguły** z filtrem, który dodaje wszystkie komunikaty z tematu do subskrypcji. Aby odbierać tylko wiadomości pasujące do filtru, należy usunąć regułę domyślną. Regułę domyślną można usunąć `ServiceBusRestProxy->deleteRule` za pomocą tej metody.
> 
> 

Poniższy przykład tworzy `HighMessages` subskrypcję o nazwie z **SqlFilter,** `MessageNumber` który wybiera tylko wiadomości, które mają właściwość niestandardową większą niż 3. Zobacz [Wysyłanie wiadomości do tematu, aby](#send-messages-to-a-topic) uzyskać informacje na temat dodawania właściwości niestandardowych do wiadomości.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Ten kod wymaga użycia dodatkowego `WindowsAzure\ServiceBus\Models\SubscriptionInfo`obszaru nazw: .

Podobnie w poniższym przykładzie `LowMessages` tworzy `SqlFilter` subskrypcję o nazwie z, która wybiera tylko wiadomości, które mają właściwość mniejszą lub równą `MessageNumber` 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Teraz, gdy wiadomość jest `mytopic` wysyłana do tematu, jest zawsze dostarczany `mysubscription` do odbiorców subskrybowanych do subskrypcji `HighMessages` i `LowMessages` selektywnie dostarczane do odbiorców subskrybowanych i subskrypcji (w zależności od zawartości wiadomości).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać wiadomość do tematu usługi `ServiceBusRestProxy->sendTopicMessage` Service Bus, aplikacja wywołuje metodę. Poniższy kod pokazuje, jak wysłać wiadomość do `mytopic` `MySBNamespace` tematu wcześniej utworzonego w obszarze nazw usługi.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami klasy [BrokeredMessage][BrokeredMessage]. [BrokeredMessage][BrokeredMessage] obiekty mają zestaw standardowych właściwości i metod, a także właściwości, które mogą służyć do przechowywania właściwości specyficzne dla aplikacji niestandardowych. W poniższym przykładzie pokazano, jak `mytopic` wysłać pięć wiadomości testowych do tematu wcześniej utworzonego. Metoda `setProperty` służy do dodawania właściwości`MessageNumber`niestandardowej ( ) do każdej wiadomości. Wartość `MessageNumber` właściwości różni się w zależności od każdej wiadomości (można użyć tej wartości, aby określić, które subskrypcje ją otrzymują, jak pokazano w sekcji [Utwórz subskrypcję):](#create-a-subscription)

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

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Ten górny limit rozmiaru tematu wynosi 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie wiadomości z subskrypcji
Najlepszym sposobem odbierania wiadomości z subskrypcji `ServiceBusRestProxy->receiveSubscriptionMessage` jest użycie metody. Wiadomości mogą być odbierane w dwóch różnych trybach: [ *ReceiveAndDelete* i *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Ustawienie domyślne to **PeekLock**.

W przypadku używania trybu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) odbieranie jest operacją pojedynczego zrzutu. Oznacza to, że kiedy usługa Service Bus odbiera żądanie odczytu komunikatu w subskrypcji, oznacza komunikat jako wykorzystywany i zwraca go do aplikacji. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * tryb jest najprostszym modelem i działa najlepiej dla scenariuszy, w których aplikacja może tolerować nie przetwarzania wiadomości, gdy wystąpi błąd. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ usługa Service Bus oznaczyła komunikat jako zużyty, a następnie po ponownym uruchomieniu aplikacji i ponownym rozpoczęciu korzystania z wiadomości, nie odebrano komunikatu, który został zużyty przed awarią.

W domyślnym trybie [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) odbieranie wiadomości staje się operacją dwuetapową, która umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości przez aplikację (lub niezawodnym jej magazynowaniu) kończy drugi etap procesu odbierania, przekazując odebraną wiadomość do `ServiceBusRestProxy->deleteMessage`. Gdy usługa Service `deleteMessage` Bus widzi wywołanie, oznacza komunikat jako zużyty i usuwa ją z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać wiadomość przy użyciu trybu [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (tryb domyślny). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Jak: obsługa awarii aplikacji i nieczytelnych komunikatów
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie jest w stanie przetworzyć `unlockMessage` wiadomości z jakiegoś powodu, a `deleteMessage` następnie można wywołać metodę na odebranej wiadomości (zamiast metody). Powoduje to, że usługa Service Bus, aby odblokować wiadomość w kolejce i udostępnić go do odebrania ponownie, za pomocą tej samej aplikacji zużywającej lub przez inną aplikację zużywającą.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce, a jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokowuje wiadomość i umożliwia jej odblokowywanie można otrzymać ponownie.

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed wystawieniem `deleteMessage` żądania, komunikat jest ponownie dostarczony do aplikacji po ponownym uruchomieniu. Ten typ przetwarzania jest często nazywany *co najmniej raz* przetwarzania; oznacza to, że każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowane przetwarzania, deweloperzy aplikacji należy dodać dodatkową logikę do aplikacji do obsługi dostarczania zduplikowanych wiadomości. Często jest to osiągane przy użyciu `getMessageId` metody wiadomości, która pozostaje stała w próbach dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Aby usunąć temat lub subskrypcję, `ServiceBusRestProxy->deleteSubscripton` użyj odpowiednio `ServiceBusRestProxy->deleteTopic` lub metod. Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem.

W poniższym przykładzie pokazano, `mytopic` jak usunąć temat o nazwie i jego zarejestrowanych subskrypcji.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
