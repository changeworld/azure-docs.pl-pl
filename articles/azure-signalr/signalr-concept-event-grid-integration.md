---
title: Reagowanie na zdarzenia usługi Azure SignalR Service
description: Usługa Azure Event Grid do subskrybowania zdarzenia usługi Azure SignalR Service.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789191"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagowanie na zdarzenia usługi Azure SignalR Service

Zdarzenia usługi Azure SignalR Service umożliwiają aplikacjom reagować na połączenia klienckie połączone lub odłączona przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności skomplikowanego kodu lub sondowania kosztowne i nieefektywna usług.  Zamiast tego zdarzenia są przekazywane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) dla subskrybentów, takie jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet własne odbiornika http niestandardowego, a tylko płatność za rzeczywiste użycie.

Azure SignalR Service — wydarzenia niezawodnie są wysyłane do usługi Event Grid, która zapewnia niezawodne dostarczanie usług do aplikacji dzięki rozbudowanym funkcjom ponawiania, zasad i dostarczania wiadomości utraconych. Aby dowiedzieć się więcej, zobacz [dostarczanie komunikatów usługi Event Grid i ponów próbę](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Model siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Bez użycia serwera stanu
Azure SignalR Service zdarzenia są aktywne tylko w przypadku, gdy połączenia klientów znajdują się w stanie bez użycia serwera. Ogólnie rzecz biorąc Jeśli klient nie kierować do serwera centralnego, go przechodzi w stan bez użycia serwera. Praca z trybu klasycznego tylko wtedy, gdy koncentratora, w którym połączeń klienckich nawiązać połączenie, nie ma serwera koncentratora. Jednak aby uniknąć problemu, niektóre zalecany jest tryb bez użycia serwera. Aby uzyskać więcej informacji o trybie usługi, zobacz [jak wybrać tryb usługi](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Dostępności zdarzenia usługi Azure SignalR Service
Korzysta z usługi Event grid [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Subskrypcje zdarzeń w usłudze Azure SignalR Service obsługuje dwa typy zdarzeń:  

|Nazwa zdarzenia|Opis|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Wywoływane, gdy połączenia klienta.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Wywoływane, gdy połączenie klienta jest odłączony.|

## <a name="event-schema"></a>Schemat zdarzeń
Azure SignalR Service zdarzeń zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować usługi Azure SignalR Service rozpoczęciu zdarzenia z właściwością typ zdarzenia za pomocą "Microsoft.SignalRService". Dodatkowe informacje na temat użycia właściwości zdarzeń usługi Event Grid jest udokumentowany na [schematu zdarzeń usługi Event Grid](../event-grid/event-schema.md).  

Oto przykład zdarzenia połączone połączenia klienta:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Aby uzyskać więcej informacji, zobacz [schematu zdarzeń usługi SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Event Grid i wypróbuj zdarzeń usługi Azure SignalR Service:

> [!div class="nextstepaction"]
> [Wypróbuj przykładowe integracji usługi Event Grid, za pomocą usługi Azure SignalR Service](./signalr-howto-event-grid-integration.md)
> [Event Grid](../event-grid/overview.md)
