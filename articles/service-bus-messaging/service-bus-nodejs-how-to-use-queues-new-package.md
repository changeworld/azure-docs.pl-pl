---
title: Jak używać kolejek azure/service-bus w pliku Node.js
description: Dowiedz się, jak napisać program Nodejs do wysyłania wiadomości i @azure/service-bus odbierania wiadomości z kolejki usługi Service Bus przy użyciu nowego pakietu.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: c2e24e9dea2c8463294c85f04c9e4d7d2da17261
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330655"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Szybki start: jak używać kolejek usługi Service Bus z node.js i pakietem azure/service-bus
W tym samouczku dowiesz się, jak napisać program Nodejs do wysyłania wiadomości [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) i odbierać wiadomości z kolejki usługi Service Bus przy użyciu nowego pakietu. Ten pakiet używa szybszego [protokołu AMQP 1.0,](service-bus-amqp-overview.md) podczas gdy starszy pakiet [azure-sb](https://www.npmjs.com/package/azure-sb) używany [interfejsów API rest usługi Service Bus.](/rest/api/servicebus/service-bus-runtime-rest) Przykłady są zapisywane w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz kolejki do pracy, wykonaj kroki w [witrynie Azure portal, aby utworzyć kolejkę usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę. Zanotuj ciąg połączenia dla wystąpienia usługi Service Bus i nazwę utworzonej kolejki. Użyjemy tych wartości w przykładach.

> [!NOTE]
> - Ten samouczek działa z przykładami, które można skopiować i uruchomić za pomocą [Nodejs](https://nodejs.org/). Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/app-service-web-get-started-nodejs.md)lub usługi w [chmurze Node.js przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nowy [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakiet nie obsługuje jeszcze tworzenia kolejek. Użyj pakietu, [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) jeśli chcesz programowo je utworzyć.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla usługi Service Bus, otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, zmień katalog na folder, w którym chcesz mieć próbki, a następnie uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Interakcja z kolejką usługi Service Bus rozpoczyna się od wystąpienia klasy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) i używania jej do tworzenia wystąpienia klasy [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Gdy masz klienta kolejki, można utworzyć nadawcę i użyć albo [wyślij](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) lub [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metody na nim do wysyłania wiadomości.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik `send.js` o nazwie i wklej do niego poniższy kod. Ten kod wyśle 10 wiadomości do kolejki.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
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
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź ciąg połączenia i nazwę kolejki w powyższym kodzie.
4. Następnie uruchom `node send.js` polecenie w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie wysłano wiadomości do kolejki usługi Service Bus.

Wiadomości mają pewne standardowe `label` `messageId` właściwości, takie jak i które można ustawić podczas wysyłania. Jeśli chcesz ustawić dowolne właściwości `userProperties`niestandardowe, użyj obiektu , który jest obiektem json, który może zawierać pary klucz-wartość danych niestandardowych.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nie ma limitu liczby wiadomości przechowywanych w kolejce, ale istnieje ograniczenie całkowitego rozmiaru wiadomości przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały usługi Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki
Interakcja z kolejką usługi Service Bus rozpoczyna się od wystąpienia klasy [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) i używania jej do tworzenia wystąpienia klasy [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Po uruchomieniu klienta kolejki można utworzyć odbiornik i użyć [metody receiveMessage lub](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) [registerMessageHandler,](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) aby odbierać wiadomości.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik `recieve.js` o nazwie i wklej do niego poniższy kod. Ten kod spróbuje odebrać 10 wiadomości z kolejki. Rzeczywista liczba otrzymanych zależy od liczby wiadomości w kolejce i opóźnienia sieci.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź ciąg połączenia i nazwę kolejki w powyższym kodzie.
4. Następnie uruchom `node receiveMessages.js` polecenie w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie odebrano wiadomości z kolejki usługi Service Bus.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) Metoda przyjmuje `ReceiveMode` w który jest wyliczenia z [wartościami ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) i [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Pamiętaj, aby [rozliczyć wiadomości,](message-transfers-locks-settlement.md#settling-receive-operations) jeśli używasz `PeekLock` trybu za pomocą dowolnego , `complete()` `abandon()`, `defer()`lub `deadletter()` metod na wiadomości.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz następujące zasoby.
- [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
- Wyewidencjonuj inne [przykłady Nodejs dla usługi Service Bus w usłudze GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](https://azure.microsoft.com/develop/nodejs/)

