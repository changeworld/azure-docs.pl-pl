---
title: Używanie tematów i subskrypcji usługi Azure/service-bus w pliku Node.js
description: 'Szybki start: dowiedz się, jak korzystać z tematów i subskrypcji usługi Service Bus na platformie Azure za pomocą aplikacji Node.js.'
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
ms.openlocfilehash: 6088b4c54ed16c5ef46d2c0671e619884cad29d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330621"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Szybki start: jak korzystać z tematów i subskrypcji usługi Service Bus z node.js i pakietem azure/service-bus
W tym samouczku dowiesz się, jak napisać program Node.js do wysyłania wiadomości do tematu usługi Service Bus i odbierać wiadomości z subskrypcji usługi Service Bus przy użyciu nowego [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakietu. Ten pakiet używa szybszego [protokołu AMQP 1.0,](service-bus-amqp-overview.md) podczas gdy starszy pakiet [azure-sb](https://www.npmjs.com/package/azure-sb) używany [interfejsów API rest usługi Service Bus.](/rest/api/servicebus/service-bus-runtime-rest) Przykłady są zapisywane w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz tematu i subskrypcji do pracy z, wykonaj kroki w [witrynie Azure portal, aby utworzyć artykuł tematów i subskrypcji usługi Service Bus,](service-bus-quickstart-topics-subscriptions-portal.md) aby je utworzyć. Zanotuj ciąg połączenia dla wystąpienia usługi Service Bus oraz nazwy utworzonego tematu i subskrypcji. Użyjemy tych wartości w przykładach.

> [!NOTE]
> - Ten samouczek działa z przykładami, które można skopiować i uruchomić za pomocą [Nodejs](https://nodejs.org/). Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/app-service-web-get-started-nodejs.md)lub usługi w [chmurze Node.js przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nowy [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakiet nie obsługuje jeszcze tworzenia topcis i subskrypcji. Użyj pakietu, [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) jeśli chcesz programowo je utworzyć.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla usługi Service Bus, otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, zmień katalog na folder, w którym chcesz mieć próbki, a następnie uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Interakcja z tematem usługi Service Bus rozpoczyna się od wystąpienia klasy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) i używania jej do tworzenia wystąpienia klasy [TopicClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) Gdy masz klienta tematu, można utworzyć nadawcę i użyć albo [wyślij](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) lub [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metody na nim do wysyłania wiadomości.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik `send.js` o nazwie i wklej do niego poniższy kod. Ten kod wyśle 10 wiadomości do tematu.

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
4. Następnie uruchom `node send.js` polecenie w wierszu polecenia, aby wykonać ten plik. 

Gratulacje! Właśnie wysłano wiadomości do kolejki usługi Service Bus.

Wiadomości mają pewne standardowe `label` `messageId` właściwości, takie jak i które można ustawić podczas wysyłania. Jeśli chcesz ustawić dowolne właściwości `userProperties`niestandardowe, użyj obiektu , który jest obiektem json, który może zawierać pary klucz-wartość danych niestandardowych.

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nie ma limitu liczby wiadomości przechowywanych w temacie, ale istnieje limit całkowitego rozmiaru wiadomości przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały usługi Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Odbieranie wiadomości z subskrypcji
Interakcja z subskrypcją usługi Service Bus rozpoczyna się od wystąpienia klasy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) i używania jej do tworzenia wystąpienia klasy [SubscriptionClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) Po uzyskaniu klienta subskrypcji, można utworzyć odbiornik i użyć [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) lub [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metody na nim do odbierania wiadomości.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik `recieve.js` o nazwie i wklej do niego poniższy kod. Ten kod spróbuje odebrać 10 wiadomości z subskrypcji. Rzeczywista liczba otrzymanych zależy od liczby komunikatów w subskrypcji i opóźnienia sieci.

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
4. Następnie uruchom `node receiveMessages.js` polecenie w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie odebrano wiadomości z subskrypcji usługi Service Bus.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) Metoda przyjmuje `ReceiveMode` w który jest wyliczenia z [wartościami ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) i [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Pamiętaj, aby [rozliczyć wiadomości,](message-transfers-locks-settlement.md#settling-receive-operations) jeśli używasz `PeekLock` trybu za pomocą dowolnego , `complete()` `abandon()`, `defer()`lub `deadletter()` metod na wiadomości.

## <a name="subscription-filters-and-actions"></a>Filtry i akcje subskrypcji
Usługa Service Bus obsługuje [filtry i akcje w ramach subskrypcji,](topic-filters.md)co umożliwia filtrowanie wiadomości przychodzących do subskrypcji i edytowanie ich właściwości.

Gdy masz wystąpienie `SubscriptionClient` a można użyć poniższych metod na nim, aby uzyskać, dodać i usunąć reguły subskrypcji, aby kontrolować filtry i akcje.

- getRules (zasady getRules)
- addRule (Reguła)
- removeRule (reguła)

Każda subskrypcja ma domyślną regułę, która używa filtru true, aby zezwolić na wszystkie przychodzące wiadomości. Po dodaniu nowej reguły należy pamiętać o usunięciu filtru domyślnego, aby filtr w nowej regule działał. Jeśli subskrypcja nie ma żadnych reguł, nie otrzyma żadnych wiadomości.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz następujące zasoby.

- [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
- Wyewidencjonuj inne [przykłady Nodejs dla usługi Service Bus w usłudze GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](https://azure.microsoft.com/develop/nodejs/)


