---
title: Jak używać kolejek usługi Azure Service Bus w środowisku Node.js — azure/service-bus | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988346"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Jak używać kolejek usługi Service Bus przy użyciu środowiska Node.js i pakietu azure/service-bus
> [!div class="op_multi_selector" title1="Język programowania" title2="Pacakge środowiska node.js"]
> - [(Node.js | azure sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

W tym samouczku dowiesz się, jak napisać program Nodejs, aby wysyłać i odbierać komunikaty z kolejki usługi Service Bus za pomocą nowego [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pakietu. Ten pakiet używa szybciej [protokołu AMQP 1.0](service-bus-amqp-overview.md) natomiast starszej wersji [azure sb](https://www.npmjs.com/package/azure-sb) używany pakiet [interfejsów API środowiska wykonawczego interfejsu REST usługi Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Przykłady są napisane w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki chcesz pracować, wykonaj czynności opisane w [użycia usługi Azure portal można utworzyć kolejki usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę. Zanotuj parametry połączenia dla swojego wystąpienia usługi Service Bus i nazwę kolejki, który został utworzony. Użyjemy tych wartości w próbce.

> [!NOTE]
> - W tym samouczku współpracuje z przykładów, które można skopiować i uruchomić za pomocą [Nodejs](https://nodejs.org/). Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji w technologii Node.js, zobacz [tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/app-service-web-get-started-nodejs.md), lub [usługi w chmurze Node.js przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nowy [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pakiet nie obsługuje jeszcze tworzenia kolejek. Użyj [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) pakietu, jeśli chcesz programowo tworzyć je.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla usługi Service Bus, Otwórz okno Wiersz polecenia, który ma `npm` w ścieżce, zmień katalog na folder, w którym chcesz mieć Twoich przykładów, a następnie uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Wchodzenie w interakcje z usługą Service Bus kolejki zaczyna się od tworzenia wystąpienia [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) klasy i użyjesz go w celu utworzenia wystąpienia [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) klasy. Po utworzeniu klienta kolejki, możesz utworzyć nadawcy i użyć [wysyłania](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) lub [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metody na niej w celu wysyłania komunikatów.

1. Otwórz ulubionym edytorze, takich jak [programu Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `send.js` i Wklej poniższego kodu, które do niej. Ten kod będzie wysyłać 10 komunikatów do kolejki.

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
3. Wprowadź parametry połączenia i nazwę swojej kolejki w powyższym kodzie.
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia do wykonania tego pliku.

Gratulacje! Wysłane wiadomości do kolejki usługi Service Bus.

Komunikaty mają niektóre standardowe właściwości, takie jak `label` i `messageId` ustawioną podczas wysyłania. Można ustawić żadnych właściwości niestandardowych, należy użyć `userProperties`, który jest obiekt json, który może zawierać pary klucz wartość danych niestandardowych.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat limitów przydziału, zobacz [przydziały usługi Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Wchodzenie w interakcje z usługą Service Bus kolejki zaczyna się od tworzenia wystąpienia [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) klasy i użyjesz go w celu utworzenia wystąpienia [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) klasy. Po utworzeniu klienta kolejki, można utworzyć odbiornik i użyć [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) lub [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metody na niej w celu odbierania komunikatów.

1. Otwórz ulubionym edytorze, takich jak [programu Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `recieve.js` i Wklej poniższego kodu, które do niej. Ten kod będzie podejmować próby odbieranie 10 komunikatów z kolejki. Rzeczywista liczba, które otrzymujesz zależy od liczby wiadomości w opóźnienia sieci i kolejki.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
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
3. Wprowadź parametry połączenia i nazwę swojej kolejki w powyższym kodzie.
4. Następnie uruchom polecenie `node receiveMessages.js` w wierszu polecenia do wykonania tego pliku.

Gratulacje! Właśnie odebrano komunikaty z kolejki usługi Service Bus.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) metoda przyjmuje `ReceiveMode` czyli wyliczenie z wartościami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) i [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Pamiętaj, aby [rozliczenia wiadomości](message-transfers-locks-settlement.md#settling-receive-operations) Jeśli używasz `PeekLock` trybie przy użyciu dowolnej z `complete()`, `abandon()`, `defer()`, lub `deadletter()` metod w komunikacie.

> [!NOTE]
> Możesz zarządzać zasobami usługi Service Bus przy użyciu [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus pozwala użytkownikom na łączenie do przestrzeni nazw usługi Service Bus i administrować jednostek obsługi komunikatów w łatwy sposób. To narzędzie zawiera zaawansowane funkcje, takie jak funkcja Importuj/Eksportuj lub możliwość testowania tematu, kolejek, subskrypcji, usługi przekazywania, usługi notification hubs i centrów zdarzeń. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej, zobacz następujące zasoby.
- [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
- Zapoznaj się z innymi [Nodejs przykłady dla usługi Service Bus w witrynie GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centrum deweloperów środowiska Node.js](https://azure.microsoft.com/develop/nodejs/)

