---
title: Jak używać tematów Azure Service Bus i subskrypcji za pomocą środowiska Node. js | Microsoft Docs
description: Dowiedz się, jak używać tematów i subskrypcji Service Bus na platformie Azure z poziomu aplikacji node. js.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: f927274e1e866a9cba72330280316cc5ee7d8047
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178052"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Jak używać tematów Service Bus i subskrypcji za pomocą środowiska Node. js i pakietu Azure/Service-Bus
> [!div class="op_multi_selector" title1="Język programowania" title2="Uaktualniający środowiska Node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

W ramach tego samouczka nauczysz się pisać program Node. js w celu wysyłania komunikatów do Service Bus tematu i odbierania komunikatów z subskrypcji Service Bus przy użyciu nowego pakietu [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Ten pakiet używa szybszego [protokołu AMQP 1,0](service-bus-amqp-overview.md) , podczas gdy starszy pakiet [platformy Azure-SB](https://www.npmjs.com/package/azure-sb) używany [Service Bus interfejsów API czasu wykonywania REST](/rest/api/servicebus/service-bus-runtime-rest). Przykłady są zapisywane w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz tematu i subskrypcji do współpracy z programem, wykonaj czynności opisane w [Azure Portal Użyj, aby utworzyć tematy Service Bus i subskrypcje](service-bus-quickstart-topics-subscriptions-portal.md) , aby je utworzyć. Zanotuj parametry połączenia dla wystąpienia Service Bus i nazwy utworzonego tematu i subskrypcji. Będziemy używać tych wartości w przykładach.

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można kopiować i uruchamiać przy użyciu [NodeJS](https://nodejs.org/). Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji node. js, zobacz [Tworzenie i wdrażanie aplikacji node. js w witrynie sieci Web platformy Azure](../app-service/app-service-web-get-started-nodejs.md)lub [usługi w chmurze Node. js przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nowy pakiet [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nie obsługuje jeszcze tworzenia topcis i subskrypcji. Użyj pakietu [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) , jeśli chcesz programowo je utworzyć.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla Service Bus, Otwórz wiersz polecenia, który ma `npm` w swojej ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Korzystanie z tematu Service Bus rozpoczyna się od tworzenia wystąpienia klasy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) i korzystania z niej w celu utworzenia wystąpienia klasy [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) . Gdy masz klienta tematu, możesz utworzyć nadawcę i użyć metody [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) lub [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) do wysyłania wiadomości.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `send.js` i wklej do niego Poniższy kod. Ten kod wyśle 10 komunikatów do tematu.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
        try {
            for (let i = 0; i < 10; i++) {
              const message= {
                body: `Hello world! ${i}`,
                label: `test`,
                userProperties: {
                    myCustomPropertyName: `my custom property value ${i}`
                }
              };
              console.log(`Sending message: ${message.body}`);
              await sender.send(message);
            }

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę tematu w powyższym kodzie.
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia, aby wykonać ten plik. 

Gratulacje! Właśnie wysłano komunikaty do kolejki Service Bus.

Komunikaty mają pewne właściwości standardowe, takie jak `label` i `messageId`, które można ustawić podczas wysyłania. Aby ustawić właściwości niestandardowe, użyj `userProperties`, który jest obiektem JSON, który może przechowywać pary klucz-wartość danych niestandardowych.

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nie ma żadnego limitu liczby komunikatów przechowywanych w temacie, ale istnieje limit całkowitego rozmiaru komunikatów przechowywanych w temacie. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Korzystanie z subskrypcji Service Bus rozpoczyna się od utworzenia wystąpienia klasy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) i użycia jej do utworzenia wystąpienia klasy [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) . Gdy masz klienta subskrypcji, możesz utworzyć odbiorcę i użyć metody [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) lub [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) do odbierania komunikatów.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `recieve.js` i wklej do niego Poniższy kod. Ten kod będzie próbował otrzymywać 10 komunikatów z subskrypcji. Rzeczywista liczba jest zależna od liczby komunikatów w ramach subskrypcji i opóźnienia sieci.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwy tematu i subskrypcji w powyższym kodzie.
4. Następnie uruchom polecenie `node receiveMessages.js` w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie odebrano komunikaty z subskrypcji Service Busej.

Metoda [getreceive](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) przyjmuje `ReceiveMode`, który jest wyliczeniem z wartościami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) i [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Należy pamiętać o [rozliczeniu komunikatów](message-transfers-locks-settlement.md#settling-receive-operations) , jeśli używasz trybu `PeekLock` przy użyciu dowolnych z `complete()`, `abandon()`, `defer()` lub `deadletter()` metod w komunikacie.

## <a name="subscription-filters-and-actions"></a>Filtry i akcje subskrypcji
Service Bus obsługuje [filtry i akcje dotyczące subskrypcji](topic-filters.md), co umożliwia filtrowanie komunikatów przychodzących do subskrypcji i edytowanie ich właściwości.

Po wystąpieniu `SubscriptionClient` można użyć poniższych metod w celu uzyskania, dodania i usunięcia reguł w ramach subskrypcji, aby kontrolować filtry i akcje.

- GetRules
- AddRule
- removeRule

Każda subskrypcja ma regułę domyślną, która używa filtru true, aby zezwalać na wszystkie komunikaty przychodzące. Po dodaniu nowej reguły Pamiętaj, aby usunąć domyślny filtr, aby filtr w nowej regule działał. Jeśli subskrypcja nie ma żadnych reguł, nie otrzyma żadnych komunikatów.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz następujące zasoby.

- [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
- Wyewidencjonuj inne [przykłady NodeJS dla Service Bus w witrynie GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centrum deweloperów środowiska Node.js](https://azure.microsoft.com/develop/nodejs/)


