---
title: Jak używać kolejek usługi Service Bus w środowisku Node.js | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure z poziomu aplikacji Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 7bc291c3a453b4dec5e6c47ab4a7f2e2ac2b9967
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641445"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Jak używać kolejek usługi Service Bus za pomocą języka Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym artykule opisano, jak używać kolejek usługi Service Bus za pomocą języka Node.js. Przykłady są napisane w języku JavaScript i użyć modułu programu Node.js Azure. Omówione scenariusze obejmują **tworzenie kolejek**, **wysyłania i odbierania wiadomości**, i **usuwanie kolejek**. Aby uzyskać więcej informacji dotyczących kolejek, zobacz [następne kroki](#next-steps) sekcji.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [tworzenie i wdrażanie aplikacji Node.js do witryny sieci Web platformy Azure][Create and deploy a Node.js application to an Azure Website], lub [Node.js usługi w chmurze] [ Node.js Cloud Service] przy użyciu programu Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Skonfigurować aplikację do użycia z magistralą usług
Aby korzystać z usługi Azure Service Bus, pobranie i użycie pakietu Node.js Azure. Ten pakiet zawiera zestaw bibliotek, które komunikują się z usługi Service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Umożliwia uzyskanie pakietu węzeł Menedżera pakietów (NPM)
1. Użyj **środowiska Windows PowerShell dla środowiska Node.js** okno polecenia, aby przejść do **c:\\węzła\\sbqueues\\WebRole1** folder, w której utworzono przykładu aplikacja.
2. Typ **azure instalacji narzędzia npm** w oknie wiersza polecenia, co powinno spowodować dane wyjściowe podobne do następującego:

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
3. Możesz ręcznie uruchomić **ls** polecenie, aby sprawdzić, czy **node_modules** folder został utworzony. W tym folderze znaleźć **azure** pakiet, który zawiera biblioteki, musisz uzyskać dostępu do kolejek usługi Service Bus.

### <a name="import-the-module"></a>Zaimportuj moduł
Za pomocą Notatnika lub innego edytora tekstu, Dodaj następujący element do góry **server.js** pliku aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Skonfiguruj połączenie usługi Azure Service Bus
Moduł Azure odczytuje zmiennej środowiskowej `AZURE_SERVICEBUS_CONNECTION_STRING` uzyskać informacje wymagane do nawiązania połączenia usługi Service Bus. Jeśli nie ustawiono tej zmiennej środowiskowej, należy określić informacje o koncie podczas wywoływania metody `createServiceBusService`.

Ustawianie zmiennych środowiskowych, na przykład [portalu Azure] [ Azure portal] dla witryny sieci Web Azure, zobacz [aplikacji sieci Web Node.js z magazynem] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Tworzenie kolejki
**ServiceBusService** obiektu umożliwia pracę z kolejek usługi Service Bus. Poniższy kod tworzy **ServiceBusService** obiektu. Dodaj ją w górnej części **server.js** pliku po instrukcji, aby zaimportować moduł Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Wywołując `createQueueIfNotExists` na **ServiceBusService** obiektu, zwracany jest określoną kolejkę (jeśli istnieje) lub utworzeniu nowej kolejki o określonej nazwie. Poniższy kod używa `createQueueIfNotExists` można utworzyć lub połączyć do kolejki o nazwie `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService` Metoda obsługuje również dodatkowe opcje, które umożliwiają zastąpić domyślne ustawienia kolejki, takie jak czas wiadomości do kolejki na żywo lub maksymalny rozmiar. Poniższy przykład przedstawia maksymalny rozmiar kolejki 5 GB i czas wygaśnięcia (TTL) wartości 1 minuty:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtry
Opcjonalne operacjach filtrowania może odnosić się do operacji wykonywanych przy użyciu **ServiceBusService**. Filtrowanie operacje mogą obejmować rejestrowania, Automatyczne ponawianie próby itp. Obiekty, które implementują metodę o sygnaturze są następujące filtry:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu jej przetwarzanie wstępne opcje żądania, należy wywołać metodę `next`, przekazywanie wywołania zwrotnego z następującą sygnaturą:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołania zwrotnego, a po zakończeniu przetwarzania `returnObject` (odpowiedzi z żądania do serwera), albo należy wywołać metodę wywołania zwrotnego `next` Jeśli istnieje, aby kontynuować przetwarzanie inne filtry lub po prostu Wywołaj `finalCallback`, która kończy się usługi wywołania.

Dwa filtry, które implementują logikę ponawiania wchodzą w skład zestawu Azure SDK dla środowiska Node.js, `ExponentialRetryPolicyFilter` i `LinearRetryPolicyFilter`. Poniższy kod tworzy `ServiceBusService` obiekt, który używa `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać wiadomość do kolejki usługi Service Bus, wywołania aplikacji `sendQueueMessage` metoda **ServiceBusService** obiektu. Komunikaty wysłane do (i odebrane z) usługi Service Bus są kolejki **BrokeredMessage** obiekty i mają zestaw właściwości standardowych (takich jak **etykiety** i **TimeToLive**), słownik, który służy do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja może ustawić treść komunikatu przez przekazanie ciąg jako komunikat. Wszelkie wymagane właściwości standardowe są wypełniane przy użyciu wartości domyślnych.

W poniższym przykładzie pokazano sposób wysyłania wiadomości testowej do kolejki o nazwie `myqueue` przy użyciu `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Odebrane komunikaty z kolejki przy użyciu `receiveQueueMessage` metoda **ServiceBusService** obiektu. Domyślnie komunikaty są usuwane z kolejki one są w trybie do odczytu; można jednak odczytać (peek) i zablokować wiadomość bez jego usuwania z kolejki, ustawiając parametr opcjonalny `isPeekLock` do **true**.

Domyślne zachowanie odczytywanie i usuwanie wiadomości w ramach operacji receive jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus zostanie oznaczona komunikat jako wykorzystany, a następnie po uruchomieniu i rozpocznie korzystanie z komunikatów ponownie aplikacji, pominie utracony komunikat, który został wykorzystany przed awarią.

Jeśli `isPeekLock` ustawiono parametr **true**, odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `deleteMessage` — metoda i dostarczający komunikat, który ma zostać usunięty jako parametr. `deleteMessage` Metoda oznacza komunikat jako wykorzystany i usunie go z kolejki.

W poniższym przykładzie pokazano sposób odbierały i przetwarzały wiadomości przy użyciu `receiveQueueMessage`. Przykład otrzymuje najpierw i usuwa komunikat i otrzyma komunikat przy użyciu `isPeekLock` ustawioną **true**, następnie usuwa komunikat przy użyciu `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metoda **ServiceBusService** obiektu. Spowoduje to odblokowanie komunikatu w kolejce i udostępnienie go do ponownego odbioru, przez tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady wygaśnięcia (np. Jeśli wystąpiła awaria aplikacji), Usługa Service Bus zostanie automatycznie odblokowanie komunikatu i stał się dostępny do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `deleteMessage` metoda jest wywoływana, a następnie komunikat zostanie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*, oznacza to, że każdy komunikat będzie przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **MessageId** właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat kolejek, zobacz następujące zasoby.

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]
* [Zestaw Azure SDK dla węzła] [ Azure SDK for Node] repozytorium w witrynie GitHub
* [Centrum deweloperów środowiska Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
