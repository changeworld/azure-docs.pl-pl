---
title: Jak używać tematów i subskrypcji Azure Service Bus za pomocą języka Node.js | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tematów usługi Service Bus i subskrypcji platformy Azure z poziomu aplikacji Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: d3a7ebd135f705a6a3ea91feb4e037a9ed6d0c79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641309"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Sposób użycia usługi Service Bus tematów i subskrypcji za pomocą języka Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym przewodniku opisano, jak używać tematów usługi Service Bus i subskrypcji z poziomu aplikacji Node.js. Omówione scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Odbieranie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji 

Aby uzyskać więcej informacji o tematach i subskrypcjach, zobacz [następne kroki](#next-steps) sekcji.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js
Utwórz pustą aplikację Node.js. Instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [tworzenie i wdrażanie aplikacji Node.js do witryny sieci Web Azure], [usługi w chmurze Node.js] [ Node.js Cloud Service] przy użyciu programu Windows PowerShell lub witryny sieci Web za pomocą programu WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Skonfigurować aplikację do użycia z magistralą usług
Aby korzystać z usługi Service Bus, Pobierz pakiet Node.js Azure. Ten pakiet zawiera zestaw bibliotek, które komunikują się z usługi Service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Umożliwia uzyskanie pakietu węzeł Menedżera pakietów (NPM)
1. Otwórz interfejs wiersza polecenia, takich jak **PowerShell** (system Windows), **terminali** (Mac), lub **Bash** (Unix).
2. Przejdź do folderu, w którym utworzono przykładowej aplikacji.
3. Typ **azure instalacji narzędzia npm** w oknie wiersza polecenia, co powinno spowodować następujące dane wyjściowe:

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. Możesz ręcznie uruchomić **ls** polecenie, aby sprawdzić, czy **węzła\_modułów** folder został utworzony. W tym folderze znaleźć **azure** pakiet, który zawiera biblioteki, musisz mieć dostęp tematów usługi Service Bus.

### <a name="import-the-module"></a>Zaimportuj moduł
Za pomocą Notatnika lub innego edytora tekstu, Dodaj następujący element do góry **server.js** pliku aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie usługi Service Bus
Moduł Azure odczytuje zmiennej środowiskowej `AZURE_SERVICEBUS_CONNECTION_STRING` ciągu połączenia, który uzyskane w poprzednim kroku, "uzyskać poświadczenia". Jeśli nie ustawiono tej zmiennej środowiskowej, należy określić informacje o koncie podczas wywoływania metody `createServiceBusService`.

Na przykład ustawienia zmiennych środowiskowych dla usługi w chmurze platformy Azure, zobacz [Node.js usługi chmury z magazynem][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Tworzenie tematu
**ServiceBusService** obiektu umożliwia pracę z tematów. Poniższy kod tworzy **ServiceBusService** obiektu. Dodaj ją w górnej części **server.js** pliku po instrukcji, aby zaimportować moduł azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Jeśli należy wywołać `createTopicIfNotExists` na **ServiceBusService** obiektu określonego tematu jest zwracany (jeśli istnieje) lub utworzono nowy temat o określonej nazwie. Poniższy kod używa `createTopicIfNotExists` można utworzyć lub połączyć się z tematem o nazwie `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

`createServiceBusService` Metoda obsługuje również dodatkowe opcje, które umożliwiają zastąpienie domyślnych ustawień tematu, takie jak czas wygaśnięcia wiadomości lub temat maksymalny rozmiar. 

Poniższy przykład przedstawia rozmiar maksymalny tematu 5 GB z czasem wygaśnięcia jednej minuty:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtry
Opcjonalne operacjach filtrowania może odnosić się do operacji wykonywanych przy użyciu **ServiceBusService**. Filtrowanie operacje mogą obejmować rejestrowania, Automatyczne ponawianie próby itp. Obiekty, które implementują metodę o sygnaturze są następujące filtry:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu przetwarzanie wstępne opcje żądania, wywołuje metodę `next`i przekazuje wywołania zwrotnego z następującą sygnaturą:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołania zwrotnego, a po zakończeniu przetwarzania `returnObject` (odpowiedzi z żądania do serwera), wywołania zwrotnego należy wywołać obok (jeśli istnieje), aby kontynuować przetwarzanie inne filtry albo wywołania `finalCallback` na koniec wywołania usługi.

Dwa filtry, które implementują logikę ponawiania wchodzą w skład zestawu Azure SDK dla środowiska Node.js, **ExponentialRetryPolicyFilter** i **LinearRetryPolicyFilter**. Tworzy następujące **ServiceBusService** obiekt, który używa **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematu są również tworzone za pomocą **ServiceBusService** obiektu. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów dostarczonych do wirtualnej kolejki subskrypcji.

> [!NOTE]
> Subskrypcje są trwałe, dopóki albo one lub temat są skojarzone z, zostaną usunięte. Jeśli Twoja aplikacja logiki, aby utworzyć subskrypcję, jej należy najpierw sprawdź, czy subskrypcja istnieje przy użyciu `getSubscription` metody.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
**MatchAll** filtru jest filtrem domyślnym, używane podczas tworzenia subskrypcji. Kiedy używasz filtru **MatchAll**, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie AllMessages i używa domyślnej **MatchAll** filtru.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Istnieje również możliwość utworzenia filtrów, które zezwalają na zakresu, które komunikaty wysyłane do tematu powinny być widoczne w subskrypcji określonego tematu.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest **SqlFilter**, która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, przejrzyj [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] składni.

Filtry można dodać do subskrypcji przy użyciu `createRule` metody **ServiceBusService** obiektu. Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

> [!NOTE]
> Ponieważ domyślnego filtru jest automatycznie stosowane do wszystkich nowych subskrypcji, należy najpierw usunąć domyślny filtr lub **MatchAll** zastępują inne filtry, można określić. Należy usunąć domyślną regułę przy użyciu `deleteRule` metody **ServiceBusService** obiektu.
>
>

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` z **SqlFilter** który wybiera tylko komunikaty, które mają niestandardowy `messagenumber` właściwości wyższej niż 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Podobnie poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z **SqlFilter** który wybiera tylko komunikaty, które mają `messagenumber` właściwości mniej niż 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Teraz wysłania komunikatu do `MyTopic`, będą dostarczane do odbiorców `AllMessages` subskrypcję tematu i selektywnie dostarczany do odbiorców mających subskrypcję `HighMessages` i `LowMessages` subskrypcje tematu (w zależności od zawartość komunikatu).

## <a name="how-to-send-messages-to-a-topic"></a>Sposób wysyłania komunikatów do tematu
Aby wysłać komunikat do tematu usługi Service Bus, aplikacja musi używać `sendTopicMessage` metody **ServiceBusService** obiektu.
Komunikaty wysyłane do tematów usługi Service Bus są **BrokeredMessage** obiektów.
**BrokeredMessage** obiekty mają zestaw właściwości standardowych (takich jak `Label` i `TimeToLive`), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść danych ciągu. Aplikacja możne ustawić treść komunikatu przez przekazanie wartości ciągu na `sendTopicMessage` i wszystkie wymagane właściwości standardowych zostaną wypełnione przez wartości domyślne.

W poniższym przykładzie pokazano sposób wysyłania pięciu testowych komunikatów do `MyTopic`. `messagenumber` Wartość właściwości każdego komunikatu różni się w iteracji pętli (określa subskrypcje, które go otrzymają):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w tematu, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Komunikaty są odbierane z subskrypcją za pomocą `receiveSubscriptionMessage` metoda **ServiceBusService** obiektu. Domyślnie komunikaty są usuwane z subskrypcji one są w trybie odczytu. Jednak można ustawić opcjonalny parametr `isPeekLock` do **true** do odczytu (peek) i Zablokuj wiadomość bez usuwania go z subskrypcji.

Domyślne zachowanie odczytywanie i usuwanie wiadomości w ramach operacji receive jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby zrozumieć to zachowanie, Rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus zostanie oznaczona komunikat jako wykorzystany, a następnie po uruchomieniu i rozpocznie korzystanie z komunikatów ponownie aplikacji, pominie utracony komunikat, który został wykorzystany przed awarią.

Jeśli `isPeekLock` ustawiono parametr **true**, odbieranie staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Kiedy Usługa Service Bus odbiera żądanie, znajduje następny komunikat korzystać, blokuje go, aby uniemożliwić innym klientom odebrania go i zwraca go do aplikacji.
Kiedy aplikacja przetwarzania komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody **deleteMessage** metody i przekazuje komunikat, aby usunąć jako parametr. **DeleteMessage** metoda oznacza komunikat jako wykorzystany i usuwa go z subskrypcji.

W poniższym przykładzie pokazano, jak mogą być odbierane wiadomości i przetworzone przy użyciu `receiveSubscriptionMessage`. Przykład otrzymuje najpierw i usuwa komunikat z subskrypcji "LowMessages" i otrzyma komunikat z subskrypcji "HighMessages" przy użyciu `isPeekLock` ustawioną na true. Następnie usuwa komunikat przy użyciu `deleteMessage`:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metoda **ServiceBusService** obiektu. Ta metoda powoduje to odblokowanie komunikatu w subskrypcji i udostępnienie go do ponownego odbioru usługę Service Bus. W takim przypadku przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w subskrypcji. Jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli wystąpiła awaria aplikacji), usługi Service Bus automatycznie odblokowuje komunikat i udostępnia go do ponownego odbioru.

W przypadku gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `deleteMessage` metoda jest wywoływana, komunikat jest dostarczony do aplikacji po jej ponownym uruchomieniu. To zachowanie jest często nazywana *co najmniej raz przetwarzania*. Oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje dwukrotnego przetwarzania, należy dodać logikę do Twojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Można użyć **MessageId** właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe i musi zostać jawnie usunięte przez [portalu Azure] [ Azure portal] lub programowo.
W poniższym przykładzie pokazano sposób usuwania tematu o nazwie `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy przykład przedstawia sposób usuwania subskrypcji o nazwie `HighMessages` z `MyTopic` tematu:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy tematów usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].
* Dokumentacja interfejsów API dla elementu [SqlFilter][SqlFilter].
* Odwiedź stronę [zestawu Azure SDK dla węzła] [ Azure SDK for Node] repozytorium w witrynie GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Tworzenie i wdrażanie aplikacji Node.js do witryny sieci Web Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

