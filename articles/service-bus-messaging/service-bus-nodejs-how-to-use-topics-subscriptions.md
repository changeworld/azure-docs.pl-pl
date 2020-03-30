---
title: Używanie tematów usługi Azure Service Bus z pakietem Node.js platformy Azure/magistrali usług
description: Dowiedz się, jak korzystać z tematów i subskrypcji usługi Service Bus na platformie Azure z aplikacji Node.js przy użyciu pakietu azure/service-bus.'
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: c85b63b4a56e74b0fef9a122ec995b4106496cbe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330450"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Szybki start: Jak korzystać z tematów i subskrypcji usługi Service Bus z node.js i pakietem azure-sb
W tym samouczku dowiesz się, jak utworzyć aplikacje Node.js do wysyłania wiadomości do tematu usługi Service Bus i odbierania wiadomości z subskrypcji usługi Service Bus przy użyciu pakietu [azure-sb.](https://www.npmjs.com/package/azure-sb) Przykłady są zapisywane w języku JavaScript i używają [modułu Node.js Azure,](https://www.npmjs.com/package/azure) który wewnętrznie używa `azure-sb` pakietu.

> [!IMPORTANT]
> Pakiet [azure-sb](https://www.npmjs.com/package/azure-sb) używa interfejsów [API w czasie wykonywania usługi Service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). Możesz szybciej korzystać z [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nowego pakietu, który używa szybszego [protokołu AMQP 1.0.](service-bus-amqp-overview.md) 
> 
> Aby dowiedzieć się więcej o nowym pakiecie, zobacz [Jak używać tematów @azure/service-bus usługi Service Bus i subskrypcji z Node.js i pakiet](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), w przeciwnym razie kontynuuj czytanie, aby zobaczyć, jak korzystać z pakietu platformy [Azure.](https://www.npmjs.com/package/azure)

Scenariusze omówione tutaj obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie wiadomości do tematu 
- Odbieranie wiadomości z subskrypcji
- Usuwanie tematów i subskrypcji 

Aby uzyskać więcej informacji na temat tematów i subskrypcji, zobacz [Następne kroki](#next-steps) sekcji.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [korzyści dla subskrybenta programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub założyć [bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Wykonaj kroki opisane w [przewodniku Szybki start: Użyj portalu Azure, aby utworzyć temat usługi Service Bus i subskrypcje tematu, aby](service-bus-quickstart-topics-subscriptions-portal.md) utworzyć **obszar nazw** usługi Service Bus i uzyskać **ciąg połączenia**.

    > [!NOTE]
    > W tym przewodniku Szybki start utworzysz **temat** i **subskrypcję** tematu przy użyciu **pliku Node.js.** 

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure,] [usługi w chmurze Node.js][Node.js Cloud Service] przy użyciu programu Windows PowerShell lub witryny sieci Web z programem WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Aby korzystać z usługi Service Bus, pobierz pakiet Node.js Azure. Ten pakiet zawiera zestaw bibliotek, które komunikują się z usługami REST usługi Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Użyj Menedżera pakietów węzłów (NPM), aby uzyskać pakiet
1. Otwórz interfejs wiersza polecenia, taki jak **PowerShell** (Windows), **Terminal** (Mac) lub **Bash** (Unix).
2. Przejdź do folderu, w którym utworzono przykładową aplikację.
3. Wpisz **npm install azure** w oknie polecenia, co powinno spowodować następujące dane wyjściowe:

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
3. Można ręcznie uruchomić polecenie **ls,** aby sprawdzić, czy folder **\_modułów węzłów** został utworzony. Wewnątrz tego folderu znajdź pakiet **platformy Azure,** który zawiera biblioteki potrzebne do uzyskania dostępu do tematów usługi Service Bus.

### <a name="import-the-module"></a>Importowanie modułu
Za pomocą Notatnika lub innego edytora tekstu dodaj następujące elementy w górnej części pliku **server.js** aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Moduł platformy Azure odczytuje zmienną `AZURE_SERVICEBUS_CONNECTION_STRING` środowiskową dla ciągu połączenia uzyskanego z wcześniejszego kroku "Uzyskaj poświadczenia". Jeśli ta zmienna środowiskowa nie jest ustawiona, należy określić informacje o koncie podczas wywoływania `createServiceBusService`.

Na przykład ustawiania zmiennych środowiskowych dla usługi Azure Cloud Service, zobacz [Ustawianie zmiennych środowiskowych](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Tworzenie tematu
**ServiceBusService** Obiekt umożliwia pracę z tematami. Poniższy kod tworzy **obiekt Usługi ServiceBusService.** Dodaj go w górnej części pliku **server.js,** po instrukcji, aby zaimportować moduł azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Jeśli wywołasz `createTopicIfNotExists` obiekt **ServiceBusService,** zwracany jest określony temat (jeśli istnieje) lub tworzony jest nowy temat o określonej nazwie. Poniższy kod `createTopicIfNotExists` służy do tworzenia lub `MyTopic`łączenia się z tematem o nazwie:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Metoda `createTopicIfNotExists` obsługuje również dodatkowe opcje, które umożliwiają zastąpienie domyślnych ustawień tematu, takich jak czas wiadomości do na żywo lub maksymalny rozmiar tematu. 

W poniższym przykładzie ustawia maksymalny rozmiar tematu do 5 GB z czasem do żywo jednej minuty:

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
Opcjonalne operacje filtrowania mogą być stosowane do operacji wykonywanych za pomocą **usługi ServiceBusService**. Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie próby itp. Filtry są obiektami, które implementują metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu wstępnego przetwarzania na opcje żądania, `next`metoda wywołuje i przekazuje wywołanie zwrotne z następującym podpisem:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołaniu zwrotnym `returnObject` i po przetworzeniu (odpowiedź z żądania do serwera), wywołanie zwrotne musi wywołać dalej (jeśli `finalCallback` istnieje), aby kontynuować przetwarzanie innych filtrów lub wywołać, aby zakończyć wywołanie usługi.

Dwa filtry, które implementują logikę ponawiania prób, wchodzą w skład zestawu Azure SDK dla platformy Node.js: **ExponentialRetryPolicyFilter** i **LinearRetryPolicyFilter**. Poniższy kod tworzy **obiekt ServiceBusService,** który używa **filtra WykładnikPolicyPolicy:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematów są również tworzone za pomocą obiektu **ServiceBusService.** Subskrypcje są nazwane i mogą mieć opcjonalny filtr, który ogranicza zestaw wiadomości dostarczanych do kolejki wirtualnej subskrypcji.

> [!NOTE]
> Domyślnie subskrypcje są trwałe, dopóki nie zostaną usunięte, dopóki nie zostaną usunięte te lub temat, z który są skojarzone. Jeśli aplikacja zawiera logikę do tworzenia subskrypcji, należy najpierw sprawdzić, `getSubscription` czy subskrypcja istnieje przy użyciu metody.
>
> Subskrypcje można usunąć automatycznie, ustawiając [właściwość AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
**MatchAll** filtr jest domyślny filtr używany podczas tworzenia subskrypcji. Kiedy używasz filtru **MatchAll**, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie AllMessages i używa domyślnego **filtru MatchAll.**

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również utworzyć filtry, które umożliwiają zakres, które wiadomości wysyłane do tematu powinny być wyświetlane w ramach subskrypcji określonego tematu.

Najbardziej elastycznym typem filtru obsługiwanego przez subskrypcje jest **SqlFilter**, który implementuje podzbiór sql92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, przejrzyj składnię [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Filtry można dodać do subskrypcji `createRule` przy użyciu metody **ServiceBusService** obiektu. Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

> [!NOTE]
> Ponieważ filtr domyślny jest stosowany automatycznie do wszystkich nowych subskrypcji, należy najpierw usunąć filtr domyślny lub **MatchAll** zastąpi wszystkie inne filtry, które można określić. Regułę domyślną można usunąć `deleteRule` przy użyciu metody obiektu **ServiceBusService.**
>
>

Poniższy przykład tworzy `HighMessages` subskrypcję o nazwie z **SqlFilter,** `messagenumber` który wybiera tylko wiadomości, które mają właściwość niestandardową większą niż 3:

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

Podobnie w poniższym przykładzie `LowMessages` tworzy subskrypcję o nazwie z **SqlFilter,** który wybiera tylko wiadomości, które mają właściwość mniejszą lub równą `messagenumber` 3:

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

Gdy wiadomość jest teraz `MyTopic`wysyłana do , jest dostarczana `AllMessages` do odbiorców subskrybowanych subskrypcji tematu `HighMessages` i `LowMessages` selektywnie dostarczana do odbiorców subskrybowanych subskrypcji i tematów (w zależności od zawartości wiadomości).

## <a name="how-to-send-messages-to-a-topic"></a>Jak wysyłać wiadomości do tematu
Aby wysłać wiadomość do tematu usługi Service `sendTopicMessage` Bus, aplikacja musi użyć metody **ServiceBusService** obiektu.
Wiadomości wysyłane do tematów usługi Service Bus są **brokeredMessage** obiektów.
**BrokeredMessage** obiekty mają zestaw właściwości standardowych `Label` (takich jak i `TimeToLive`), słownik, który jest używany do przechowywania właściwości specyficzne dla aplikacji niestandardowych i treść danych ciągu. Aplikacja może ustawić treść wiadomości, przekazując wartość `sendTopicMessage` ciągu do i wszystkie wymagane właściwości standardowe są wypełniane przez wartości domyślne.

W poniższym przykładzie pokazano, jak `MyTopic`wysłać pięć wiadomości testowych do . Wartość `messagenumber` właściwości każdej wiadomości różni się w zależności od iteracji pętli (ta właściwość określa, które subskrypcje ją odbierają):

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

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma limitu liczby wiadomości przechowywanych w temacie, ale istnieje limit całkowitego rozmiaru wiadomości przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie wiadomości z subskrypcji
Wiadomości są odbierane z `receiveSubscriptionMessage` subskrypcji przy użyciu metody w **obiekcie ServiceBusService.** Domyślnie wiadomości są usuwane z subskrypcji podczas ich odczytywania. Można jednak ustawić parametr `isPeekLock` opcjonalny na **true** do odczytu (wglądu) i zablokować wiadomość bez usuwania go z subskrypcji.

Domyślne zachowanie odczytu i usuwania wiadomości w ramach operacji odbierania jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzanie wiadomości, gdy występuje błąd. Aby zrozumieć to zachowanie, należy wziąć pod uwagę scenariusz, w którym konsument wystawia żądanie odbierania, a następnie ulega awarii przed przetworzeniem. Ponieważ usługa Service Bus oznaczyła komunikat jako zużyty, a następnie po ponownym uruchomieniu aplikacji i ponownym rozpoczęciu korzystania z wiadomości, nie odebrano komunikatu, który został zużyty przed awarią.

Jeśli `isPeekLock` parametr jest ustawiony na **true,** receive staje się operacją dwuetapową, która umożliwia obsługę aplikacji, które nie tolerują nieodebranych wiadomości. Gdy usługa Service Bus odbiera żądanie, znajdzie następną wiadomość do wykorzystania, blokuje go, aby uniemożliwić innym odbiorcom odbieranie go i zwraca go do aplikacji.
Po aplikacji przetwarza komunikat (lub przechowuje go niezawodnie do przetwarzania w przyszłości), kończy drugi etap procesu odbierania przez wywołanie **deleteMessage** metody i przekazuje wiadomość do usunięcia jako parametr. Metoda **deleteMessage** oznacza wiadomość jako zużytą i usuwa ją z subskrypcji.

W poniższym przykładzie pokazano, jak wiadomości `receiveSubscriptionMessage`mogą być odbierane i przetwarzane za pomocą programu . Przykład najpierw odbiera i usuwa wiadomość z subskrypcji "LowMessages", a następnie odbiera komunikat z subskrypcji "HighMessages" przy użyciu `isPeekLock` zestawu true. Następnie usuwa wiadomość za `deleteMessage`pomocą:

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
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie jest w stanie przetworzyć `unlockMessage` komunikatu z jakiegoś powodu, a następnie można wywołać metodę na **ServiceBusService** obiektu. Ta metoda powoduje, że usługa Service Bus, aby odblokować komunikat w ramach subskrypcji i udostępnić go do odebraniu ponownie. W tym przypadku przez tę samą aplikację zużywającą lub przez inną aplikację zużywającą.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w ramach subskrypcji. Jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokowuje wiadomość i udostępnia ją do odebraniu ponownie.

W przypadku awarii aplikacji po przetworzeniu `deleteMessage` wiadomości, ale przed wywołaniem metody, komunikat jest ponownie dostarczony do aplikacji po ponownym uruchomieniu. To zachowanie jest często nazywane *Co najmniej raz przetwarzania*. Oznacza to, że każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowane przetwarzania, należy dodać logikę do aplikacji do obsługi dostarczania zduplikowanych wiadomości. Można użyć **MessageId** właściwości wiadomości, która pozostaje stała w próbach dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe, chyba że [ustawiono właściwość autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) i musi zostać jawnie usunięte za pośrednictwem [witryny Azure portal][Azure portal] lub programowo.
W poniższym przykładzie pokazano, `MyTopic`jak usunąć temat o nazwie:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. W poniższym przykładzie pokazano, `HighMessages` jak `MyTopic` usunąć subskrypcję o nazwie z tematu:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy tematów usługi Service Bus, skorzystaj z tych łączy, aby dowiedzieć się więcej.

* Zobacz [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].
* Dokumentacja interfejsów API dla elementu [SqlFilter][SqlFilter].
* Odwiedź [zestaw SDK platformy Azure dla][Azure SDK for Node] repozytorium węzłów w usłudze GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

