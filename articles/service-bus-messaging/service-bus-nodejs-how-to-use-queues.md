---
title: Jak używać kolejek usługi Service Bus w środowisku Node.js | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure z poziomu aplikacji Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6159609f894f967e8ee372a0ee316eb900537aba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500842"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Jak używać kolejek usługi Service Bus przy użyciu środowiska Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym samouczku dowiesz się, jak tworzyć aplikacje Node.js do wysyłania komunikatów i odbiera komunikaty z kolejki usługi Service Bus. Przykłady są napisane w języku JavaScript i korzystać z modułu Node.js Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Jeśli nie masz kolejki chcesz pracować, wykonaj czynności opisane w [użycia usługi Azure portal można utworzyć kolejki usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.
    1. Przeczytaj szybkiego **Przegląd** usługi Service Bus **kolejek**. 
    2. Tworzenie usługi Service Bus **przestrzeni nazw**. 
    3. Pobierz **parametry połączenia**. 

        > [!NOTE]
        > Utworzysz **kolejki** w przestrzeni nazw usługi Service Bus przy użyciu środowiska Node.js w ramach tego samouczka. 
 

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js
Tworzenie pustej aplikacji Node.js. Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji w technologii Node.js, zobacz [tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure][Create and deploy a Node.js application to an Azure Website], lub [Node.js usługi w chmurze] [ Node.js Cloud Service] przy użyciu programu Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Aby korzystać z usługi Azure Service Bus, Pobierz i za pomocą pakietu Node.js Azure. Ten pakiet zawiera zbiór bibliotek, które komunikują się z usługami interfejsu REST usługi Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Uzyskiwanie pakietu przy użyciu Node Package Manager (NPM)
1. Użyj **programu Windows PowerShell dla platformy Node.js** okno polecenia, aby przejść do **c:\\węzła\\sbqueues\\WebRole1** folderu, w którym zostały utworzone przykładu aplikacja.
2. Typ **npm zainstalować azure** w oknie wiersza polecenia, co powinno spowodować dane wyjściowe podobne do następujących:

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
3. Możesz ręcznie uruchomić polecenie **ls**, aby sprawdzić, czy utworzono folder **node_modules**. Wewnątrz tego folderu Znajdź **azure** pakiet, który zawiera biblioteki, musisz mieć dostęp do kolejek usługi Service Bus.

### <a name="import-the-module"></a>Zaimportuj moduł
Za pomocą Notatnika lub innego edytora tekstu, Dodaj następujący kod do górnej części **server.js** pliku aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Skonfigurować połączenie usługi Azure Service Bus
Moduł Azure odczytuje zmiennej środowiskowej `AZURE_SERVICEBUS_CONNECTION_STRING` Aby uzyskać informacje wymagane do połączenia z usługą Service Bus. Jeśli nie ustawiono tej zmiennej środowiskowej, należy określić informacje o koncie podczas wywoływania `createServiceBusService`.

Na przykład Ustawianie zmiennych środowiskowych [witryny Azure portal] [ Azure portal] dla witryny internetowej platformy Azure, zobacz [aplikację sieci Web Node.js za pomocą magazynu] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Tworzenie kolejki
**ServiceBusService** obiekt umożliwia pracę z kolejkami usługi Service Bus. Poniższy kod tworzy **ServiceBusService** obiektu. Dodaj ją w górnej części **server.js** pliku po instrukcji, aby zaimportować moduł platformy Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Przez wywołanie metody `createQueueIfNotExists` na **ServiceBusService** obiektu, zwracany jest określoną kolejkę (jeśli istnieje) lub utworzeniu nowej kolejki o określonej nazwie. Poniższy kod używa `createQueueIfNotExists` utworzyć lub połączyć się kolejkę o nazwie `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService` Metoda obsługuje również dodatkowe opcje, które umożliwiają zastąpić domyślne ustawienia kolejki, np. czas komunikat do kolejki na żywo lub maksymalny rozmiar. Poniższy przykład ustawia maksymalny rozmiar kolejki 5 GB i czas wygaśnięcia (TTL) wartości 1 minuty:

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
Opcjonalne filtrowania operacje mogą być stosowane do operacji wykonywanych przy użyciu **ServiceBusService**. Filtrowanie operacji mogą obejmować rejestrowanie automatyczne ponawianie, itp. Filtry to obiekty, które implementują metodę o następującej sygnaturze:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu tej jej wstępne przetwarzanie na temat opcji żądania, należy wywołać metodę `next`, przekazując wywołanie zwrotne z następującą sygnaturą:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołaniu zwrotnym, a po zakończeniu przetwarzania `returnObject` (odpowiedź z żądania do serwera), wywołania zwrotnego albo musi zostać wywołany `next` istnienia Kontynuuj przetwarzanie innych filtrów lub wywołania `finalCallback`, który kończy się wywołania usługi .

Dwa filtry, które implementują logikę ponawiania prób są uwzględniane w zestawie Azure SDK dla środowiska Node.js, `ExponentialRetryPolicyFilter` i `LinearRetryPolicyFilter`. Poniższy kod tworzy `ServiceBusService` obiektu, który używa `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki usługi Service Bus, wywołania aplikacji `sendQueueMessage` metody **ServiceBusService** obiektu. Komunikaty wysłane do (i otrzymane z) usługi Service Bus są kolejki **BrokeredMessage** obiektów i mają zestaw właściwości standardowych (takich jak **etykiety** i **TimeToLive**), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikację można ustawić treść komunikatu przez przekazanie ciąg jako komunikat. Wszelkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

Poniższy przykład pokazuje, jak wysyłać wiadomości testowej kolejkę o nazwie `myqueue` przy użyciu `sendQueueMessage`:

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

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat limitów przydziału, zobacz [przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Komunikaty są odbierane z kolejki za pomocą `receiveQueueMessage` metody **ServiceBusService** obiektu. Domyślnie komunikaty są usuwane z kolejki, ponieważ są odczytywane; można jednak odczytać (peek) i blokowanie wiadomość bez usuwania go z kolejki, ustawiając parametr opcjonalny `isPeekLock` do **true**.

Domyślne zachowanie odczytywanie i usuwanie wiadomości jako część operacji odbierania jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ usługi Service Bus będą oznaczyła komunikat jako wykorzystany, a następnie, gdy aplikacja ponownie uruchamia i ponownie rozpocznie korzystanie z komunikatów, pominie utracony komunikat, który został wykorzystany przed awarią.

Jeśli `isPeekLock` parametr ma wartość **true**, odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `deleteMessage` metody i dostarczający komunikat do usunięcia jako parametr. `deleteMessage` Metoda oznacza komunikat jako wykorzystany i usuwa go z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać komunikaty przy użyciu `receiveQueueMessage`. Przykład otrzymuje najpierw usuwa wiadomości i odbiera komunikat przy użyciu `isPeekLock` równa **true**, następnie usuwa komunikat przy użyciu `deleteMessage`:

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
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlockMessage` metody **ServiceBusService** obiektu. Spowoduje to odblokowanie komunikatu w kolejce i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus zostanie automatycznie odblokować wiadomości i ułatwiają dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed `deleteMessage` metoda jest wywoływana, a następnie komunikat zostanie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*, oznacza to, że każdy komunikat będzie przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu właściwości **MessageId** komunikatu, która pozostaje niezmienna między kolejnymi próbami dostarczenia.

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
