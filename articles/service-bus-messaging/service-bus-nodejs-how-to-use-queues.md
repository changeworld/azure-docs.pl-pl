---
title: Używanie kolejek usługi Azure Service Bus w pliku Node.js przy użyciu pakietu azure-sb
description: Dowiedz się, jak tworzyć aplikacje Node.js do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Azure Service Bus przy użyciu pakietu azure-sb.
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
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7ee3939c1a1b450f2458267ab0b70e3924a4869b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330604"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Szybki start: używanie kolejek usługi Service Bus na platformie Azure z plikem Node.js i pakietem azure-sb
W tym samouczku dowiesz się, jak tworzyć aplikacje Node.js do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Azure Service Bus przy użyciu pakietu [azure-sb.](https://www.npmjs.com/package/azure-sb) Przykłady są zapisywane w języku JavaScript i używają [modułu Platformy Azure](https://www.npmjs.com/package/azure) Node.js, który wewnętrznie używa pakietu azure-sb.

> [!IMPORTANT]
> Pakiet [azure-sb](https://www.npmjs.com/package/azure-sb) używa interfejsów [API w czasie wykonywania usługi Service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). Możesz uzyskać szybsze doświadczenie [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) przy użyciu nowego, który używa szybszego [protokołu AMQP 1.0](service-bus-amqp-overview.md). 
> 
> Aby dowiedzieć się więcej o nowym pakiecie, zobacz [Jak używać @azure/service-bus kolejek usługi Service Bus z node.js i package](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), w przeciwnym razie kontynuuj czytanie, aby zobaczyć, jak używać pakietu platformy [Azure.](https://www.npmjs.com/package/azure)

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz kolejki do pracy, wykonaj kroki w [witrynie Azure portal, aby utworzyć kolejkę usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.
    1. Przeczytaj krótki **przegląd** **kolejek**usługi Service Bus . 
    2. Tworzenie obszaru **nazw**usługi Service Bus . 
    3. Pobierz **ciąg połączenia**. 

        > [!NOTE]
        > **Kolejkę** utworzysz w obszarze nazw usługi Service Bus przy użyciu pliku Node.js w tym samouczku. 
 

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js
Utwórz pustą aplikację Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure][Create and deploy a Node.js application to an Azure Website]lub usługi w [chmurze Node.js][Node.js Cloud Service] przy użyciu programu Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Aby korzystać z usługi Azure Service Bus, pobierz pakiet Node.js Azure i użyj go. Ten pakiet zawiera zestaw bibliotek, które komunikują się z usługami REST usługi Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Użyj Menedżera pakietów węzłów (NPM), aby uzyskać pakiet
1. Okno polecenia **Windows PowerShell for Node.js** umożliwia przejście do **folderu\\C: node\\sbqueues\\WebRole1,** w którym utworzono przykładową aplikację.
2. Wpisz **npm install azure** w oknie polecenia, co powinno spowodować wyjście podobne do następującego przykładu:

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
3. Możesz ręcznie uruchomić polecenie **ls**, aby sprawdzić, czy utworzono folder **node_modules**. Wewnątrz tego folderu znajdź pakiet **platformy Azure,** który zawiera biblioteki potrzebne do uzyskania dostępu do kolejek usługi Service Bus.

### <a name="import-the-module"></a>Importowanie modułu
Za pomocą Notatnika lub innego edytora tekstu dodaj następujące elementy w górnej części pliku **server.js** aplikacji:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Konfigurowanie połączenia usługi Azure Service Bus
Moduł platformy Azure odczytuje zmienną `AZURE_SERVICEBUS_CONNECTION_STRING` środowiskową w celu uzyskania informacji wymaganych do połączenia z usługą Service Bus. Jeśli ta zmienna środowiskowa nie jest ustawiona, `createServiceBusService`należy określić informacje o koncie podczas wywoływania .

Na przykład ustawiania zmiennych środowiskowych w [witrynie Azure portal][Azure portal] dla witryny sieci Web platformy Azure zobacz [Node.js Web Application with Storage][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Tworzenie kolejki
**Obiekt ServiceBusService** umożliwia pracę z kolejkami usługi Service Bus. Poniższy kod tworzy **obiekt Usługi ServiceBusService.** Dodaj go w górnej części pliku **server.js,** po instrukcji, aby zaimportować moduł platformy Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

`createQueueIfNotExists` Wywołując obiekt **ServiceBusService,** zwracana jest określona kolejka (jeśli istnieje) lub tworzona jest nowa kolejka o określonej nazwie. Następujący kod służy `createQueueIfNotExists` do tworzenia lub łączenia się z kolejką o nazwie: `myqueue`

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Metoda `createServiceBusService` obsługuje również dodatkowe opcje, które umożliwiają zastąpienie domyślnych ustawień kolejki, takich jak czas wiadomości do bieżącego lub maksymalny rozmiar kolejki. W poniższym przykładzie ustawia maksymalny rozmiar kolejki na 5 GB, a wartość czasu wygaśnięcia (TTL) 1 minuty:

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
Opcjonalne operacje filtrowania mogą być stosowane do operacji wykonywanych za pomocą **usługi ServiceBusService**. Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie próby itp. Filtry są obiektami, które implementują metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po wykonaniu wstępnego przetwarzania na opcje żądania, `next`metoda musi wywołać , przekazywanie wywołania zwrotnego z następującym podpisem:

```javascript
function (returnObject, finalCallback, next)
```

W tym wywołaniu zwrotnym `returnObject` i po przetworzeniu (odpowiedź z żądania do serwera), wywołanie zwrotne `next` musi wywołać, `finalCallback`jeśli istnieje, aby kontynuować przetwarzanie innych filtrów lub wywołać , co kończy wywołanie usługi.

Dwa filtry implementujące logikę ponawiania prób są dołączone `ExponentialRetryPolicyFilter` do `LinearRetryPolicyFilter`zestawu Azure SDK dla node.js i . Poniższy kod `ServiceBusService` tworzy obiekt, `ExponentialRetryPolicyFilter`który używa :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać wiadomość do kolejki usługi Service `sendQueueMessage` Bus, aplikacja wywołuje metodę w **obiekcie ServiceBusService.** Wiadomości wysyłane do (i odebrane z) kolejek usługi Service Bus są **brokeredMessage** obiektów i mają zestaw właściwości standardowych (takich jak **Label** i **TimeToLive**), słownik, który jest używany do przechowywania niestandardowych właściwości specyficznych dla aplikacji i treść dowolnych danych aplikacji. Aplikacja może ustawić treść wiadomości, przekazując ciąg jako wiadomość. Wszystkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

W poniższym przykładzie pokazano, jak wysłać `myqueue` `sendQueueMessage`wiadomość testową do kolejki o nazwie przy użyciu:

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

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma limitu liczby wiadomości przechowywanych w kolejce, ale istnieje ograniczenie całkowitego rozmiaru wiadomości przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki
Wiadomości są odbierane z `receiveQueueMessage` kolejki przy użyciu metody w **obiekcie ServiceBusService.** Domyślnie wiadomości są usuwane z kolejki podczas ich odczytywania; można jednak odczytać (zajrzeć) i zablokować wiadomość bez usuwania jej z `isPeekLock` kolejki, ustawiając opcjonalny parametr na **true**.

Domyślne zachowanie odczytu i usuwania wiadomości w ramach operacji odbierania jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzanie wiadomości, gdy wystąpi błąd. Aby zrozumieć to zachowanie, należy wziąć pod uwagę scenariusz, w którym konsument wystawia żądanie odbierania, a następnie ulega awarii przed przetworzeniem. Ponieważ usługa Service Bus oznaczy komunikat jako zużyty, po ponownym uruchomieniu aplikacji i ponownym rozpoczęciu korzystania z wiadomości, zostanie wyświetlony komunikat, który został zużyty przed awarią.

Jeśli `isPeekLock` parametr jest ustawiony na **true,** receive staje się operacją dwuetapową, która umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości (lub magazynuje ją niezawodnie do przetwarzania w przyszłości), kończy `deleteMessage` drugi etap procesu odbierania przez metodę wywoływania i dostarczanie wiadomości do usunięcia jako parametr. Metoda `deleteMessage` oznacza komunikat jako zużyty i usuwa go z kolejki.

W poniższym przykładzie pokazano, jak `receiveQueueMessage`odbierać i przetwarzać wiadomości za pomocą programu . Przykład najpierw odbiera i usuwa wiadomość, a następnie `isPeekLock` odbiera wiadomość używającą ustawionej `deleteMessage`na **true,** a następnie usuwa wiadomość za pomocą:

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
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie jest w stanie przetworzyć `unlockMessage` komunikatu z jakiegoś powodu, a następnie można wywołać metodę na **ServiceBusService** obiektu. spowoduje to, że usługa Service Bus, aby odblokować wiadomość w kolejce i udostępnić go do odebrania ponownie, za pomocą tej samej aplikacji zużywającej lub przez inną aplikację zużywającą.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce, a jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokuje wiadomość i ją udostępni. można otrzymać ponownie.

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed wywołaniem `deleteMessage` metody, komunikat zostanie ponownie dostarczona do aplikacji po ponownym uruchomieniu. Takie podejście jest często nazywane *Co najmniej raz przetwarzania*, oznacza to, że każda wiadomość zostanie przetworzona co najmniej raz, ale w niektórych sytuacjach ten sam komunikat może być ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowane przetwarzania, deweloperzy aplikacji należy dodać dodatkową logikę do ich aplikacji do obsługi dostarczania zduplikowanych wiadomości. Często jest osiągana przy użyciu **MessageId** właściwości wiadomości, która pozostanie stała w próbach dostarczenia.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o kolejkach, zobacz następujące zasoby.

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]
* [Zestaw SDK platformy Azure dla repozytorium węzłów][Azure SDK for Node] w usłudze GitHub
* [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
