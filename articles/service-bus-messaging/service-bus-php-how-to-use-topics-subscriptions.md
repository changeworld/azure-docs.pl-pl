---
title: Jak używać tematów usługi Service Bus za pomocą języka PHP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tematów usługi Service Bus za pomocą języka PHP na platformie Azure.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 1ce9c5ddb08f3e81a0f0050048a8afef24e4c625
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607538"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Jak korzystać z subskrypcji i tematów usługi Service Bus za pomocą języka PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule pokazano, jak używać tematów usługi Service Bus i subskrypcji. Przykłady są zapisywane w języku PHP i użyj [zestawu Azure SDK dla języka PHP](../php-download-sdk.md). Omówione scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Odbieranie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów programu Visual Studio i MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj czynności opisane w [Szybki Start: Użyj witryny Azure portal do utworzenia tematu usługi Service Bus i subskrypcji do tematu](service-bus-quickstart-topics-subscriptions-portal.md) do utworzenia usługi Service Bus **przestrzeni nazw** i Uzyskaj **parametry połączenia**.

    > [!NOTE]
    > Utworzysz **tematu** i **subskrypcji** do tematu przy użyciu **PHP** w tym przewodniku Szybki Start. 

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem dla tworzenia aplikacji w języku PHP, który uzyskuje dostęp do usługi Azure Blob to odwoływać się do klas w [zestawu Azure SDK dla języka PHP](../php-download-sdk.md) z w obrębie kodu. Można użyć dowolnego narzędzia programistyczne do tworzenia aplikacji lub Notatnik.

> [!NOTE]
> Instalacja PHP musi mieć również [rozszerzenia OpenSSL](https://php.net/openssl) zainstalowane i włączone.
> 
> 

W tym artykule opisano, jak korzystać z funkcji usługi, które mogą być wywoływane w ramach aplikacji PHP lokalnie lub w kodzie, w ramach roli sieci web platformy Azure, rola procesu roboczego lub witryny sieci Web.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klienta usługi Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Aby użyć interfejsów API usługi Service Bus:

1. Odwołanie do automatycznej ładowarki pliku przy użyciu [require_once] [ require-once] instrukcji.
2. Odwoływać się do dowolnej klasy, których można użyć.

Poniższy przykład pokazuje, jak to plik automatycznej ładowarki i odwołania **ServiceBusService** klasy.

> [!NOTE]
> W tym przykładzie (i inne przykłady w tym artykule) przyjęto założenie, że zainstalowano biblioteki klienckie PHP na platformie Azure za pośrednictwem Composer (kompozytor). Po zainstalowaniu bibliotek ręcznie lub jako pakiet GRUSZKI, użytkownik musi odwoływać się **WindowsAzure.php** automatycznej ładowarki pliku.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach `require_once` instrukcji jest zawsze widoczne, ale są wywoływane tylko klasy konieczne na przykład do wykonania.

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Do utworzenia wystąpienia klienta usługi Service Bus, musisz mieć prawidłowe parametry połączenia w następującym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` ma zazwyczaj format `https://[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolny klient usługi platformy Azure, należy użyć `ServicesBuilder` klasy. Możesz:

* Przekaż parametry połączenia do niego bezpośrednio.
* Użyj **CloudConfigurationManager (CCM)** do sprawdzenia wiele źródeł zewnętrznych dla parametrów połączenia:
  * Domyślnie pochodzi z obsługą jednego źródła zewnętrznego — zmienne środowiskowe.
  * Możesz dodać nowe źródła, rozszerzając klasę `ConnectionStringSource`.

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Tworzenie tematu
Można wykonywać operacje zarządzania dla tematów usługi Service Bus za pośrednictwem `ServiceBusRestProxy` klasy. A `ServiceBusRestProxy` obiekt jest konstruowany przy użyciu `ServicesBuilder::createServiceBusService` metoda fabryki parametrami odpowiednie połączenie, który hermetyzuje tokenu uprawnieniami do zarządzania nim.

Poniższy przykład pokazuje, jak utworzyć wystąpienie `ServiceBusRestProxy` i wywołać `ServiceBusRestProxy->createTopic` Aby utworzyć temat o nazwie `mytopic` w ramach `MySBNamespace` przestrzeni nazw:

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
> Możesz użyć `listTopics` metody `ServiceBusRestProxy` obiekty do sprawdzenia, czy temat o określonej nazwie już istnieje w przestrzeni nazw usługi.
> 
> 

## <a name="create-a-subscription"></a>Tworzenie subskrypcji
Subskrypcje tematu są również tworzone przy użyciu `ServiceBusRestProxy->createSubscription` metody. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów przesyłany do wirtualnej kolejki subskrypcji.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Jeśli po utworzeniu nowej subskrypcji został określony żaden filtr **MatchAll** filtr (ustawienie domyślne) jest używany. Gdy **MatchAll** filtr jest stosowany, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie `mysubscription` i używa domyślnego **MatchAll** filtru.

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
Można również ustawiać filtry pozwalające określić, które komunikaty wysyłane do tematu powinny znajdować się w subskrypcji określonego tematu. Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat SqlFilters zobacz [właściwość SqlFilter.SqlExpression][sqlfilter].

> [!NOTE]
> Każda reguła w ramach subskrypcji przetwarza komunikaty przychodzące niezależnie, dodając ich wiadomości wynik do subskrypcji. Ponadto każda nowa subskrypcja ma domyślny **reguły** obiektu za pomocą filtru, który dodaje wszystkie komunikaty z tematu do subskrypcji. Aby odbierać tylko komunikaty z pasujących do Twojego filtru, należy usunąć reguły domyślnej. Możesz usunąć domyślną regułę przy użyciu `ServiceBusRestProxy->deleteRule` metody.
> 
> 

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` z **SqlFilter** wybiera tylko komunikaty o niestandardowej `MessageNumber` właściwość wyższej niż 3. Zobacz [wysyłanie komunikatów do tematu](#send-messages-to-a-topic) informacje dotyczące dodawania właściwości niestandardowych do wiadomości.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Ten kod wymaga użycia dodatkowej przestrzeni nazw: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Podobnie poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z `SqlFilter` wybiera tylko komunikaty, które mają `MessageNumber` właściwość mniejszą lub równą 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Teraz, gdy komunikat jest wysyłany do `mytopic` tematu, zawsze jest dostarczany do odbiorców mających `mysubscription` subskrypcji i selektywnie dostarczany do odbiorców mających subskrypcję `HighMessages` i `LowMessages` subskrypcje (w zależności od zawartości komunikatu).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do tematu usługi Service Bus, wywołania aplikacji `ServiceBusRestProxy->sendTopicMessage` metody. Poniższy kod pokazuje, jak można wysłać wiadomości do `mytopic` utworzone wcześniej w temacie `MySBNamespace` przestrzeni nazw usługi.

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

Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami [BrokeredMessage] [ BrokeredMessage] klasy. [BrokeredMessage] [ BrokeredMessage] obiekty mają zestaw właściwości standardowych i metody, a także właściwości, które mogą służyć do przechowywania niestandardowych właściwości specyficzne dla aplikacji. Poniższy przykład przedstawia sposób wysyłania pięciu testowych komunikatów do `mytopic` wcześniej utworzony temat. `setProperty` Metoda służy do dodawania właściwości niestandardowych (`MessageNumber`) do każdej wiadomości. `MessageNumber` Różni się w wartości właściwości dla każdej wiadomości (tej wartości można użyć, aby określić subskrypcje, które otrzymają, jak pokazano na [Utwórz subskrypcję](#create-a-subscription) sekcji):

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

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Ta górny limit na rozmiar tematu jest 5 GB. Aby uzyskać więcej informacji na temat limitów przydziału, zobacz [przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Najlepszym sposobem na odbieranie komunikatów z subskrypcji jest użycie `ServiceBusRestProxy->receiveSubscriptionMessage` metody. Komunikaty mogą być odbierane w dwóch różnych trybach: [*ReceiveAndDelete* i *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Ustawienie domyślne to **PeekLock**.

W przypadku używania trybu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) odbieranie jest operacją pojedynczego zrzutu. Oznacza to, że kiedy usługa Service Bus odbiera żądanie odczytu komunikatu w subskrypcji, oznacza komunikat jako wykorzystywany i zwraca go do aplikacji. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * tryb jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie niepowodzenia. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Usługa Service Bus oznaczyła komunikat jako wykorzystany, następnie gdy aplikacja zostanie ponownie uruchomiona i ponownie rozpocznie korzystanie z komunikatów, jego ma utracony komunikat, który został wykorzystany przed awarią.

W domyślnym [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) trybie odebranie komunikatu staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez przekazanie odebranego komunikatu do `ServiceBusRestProxy->deleteMessage`. Gdy Usługa Service Bus widzi `deleteMessage` wywołanie, jego oznacza komunikat jako wykorzystany i usuń go z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać komunikat przy użyciu [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) mode (tryb domyślny). 

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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Porady: Obsługa awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metody dla odebranego komunikatu (zamiast `deleteMessage` metody). Powoduje to odblokowanie komunikatu w kolejce i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed limit czasu blokady upływem (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i przypisz ją dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed `deleteMessage` wystawić żądania, a następnie wiadomości są przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Tego rodzaju przetwarzania jest często nazywane *co najmniej raz* oznacza, że przetwarzanie; każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do aplikacji do obsługi dwukrotnego dostarczania komunikatów. Jest często osiągane przy użyciu `getMessageId` metoda wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Aby usunąć tematu lub subskrypcji, użyj `ServiceBusRestProxy->deleteTopic` lub `ServiceBusRestProxy->deleteSubscripton` metod, odpowiednio. Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem.

Poniższy przykład pokazuje sposób usuwania tematu o nazwie `mytopic` i jego zarejestrowanej subskrypcji.

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

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
