---
title: Reagowanie na zdarzenia usługi Azure SignalR
description: Użyj usługi Azure Event Grid, aby subskrybować zdarzenia usługi Azure SignalR. Inne usługi podrzędne mogą być wyzwalane przez te zdarzenia.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158196"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagowanie na zdarzenia usługi Azure SignalR Service

Zdarzenia usługi Azure SignalR service umożliwiają aplikacjom reagowanie na połączenia klientów połączone lub rozłączone przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania.  Zamiast tego zdarzenia są wypychane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak usługi Azure [Functions,](https://azure.microsoft.com/services/functions/) [Usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet do własnego niestandardowego odbiornika http i płacisz tylko za to, czego używasz.

Zdarzenia usługi Azure SignalR service są niezawodnie wysyłane do usługi Event Grid, która zapewnia niezawodne usługi dostarczania do aplikacji za pośrednictwem rozbudowanych zasad ponawiania prób i dostarczania utraconych wiadomości. Aby dowiedzieć się więcej, zobacz [Dostarczanie wiadomości w ucho i ponów próbę .](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

![Model siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stan bezserwerowy
Zdarzenia usługi Azure SignalR service są aktywne tylko wtedy, gdy połączenia klienta są w stanie bezserwerowym. Ogólnie rzecz biorąc, jeśli klient nie kieruje do serwera centrum, przechodzi do stanu bezserwerowego. Tryb klasyczny działa tylko wtedy, gdy koncentrator, z którym łączą się połączenia klienta, nie ma serwera koncentratora. Zaleca się jednak, aby uniknąć pewnego problemu w trybie bezserwerowym. Aby dowiedzieć się więcej o trybie serwisowym, zobacz [Jak wybrać tryb usługi](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Dostępne zdarzenia usługi Azure SignalR
Siatka zdarzeń używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania wiadomości zdarzeń do subskrybentów. Subskrypcje zdarzeń usługi Azure SignalR Service obsługują dwa typy zdarzeń:  

|Nazwa wydarzenia|Opis|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Wywoływane, gdy połączenie klienta jest połączone.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Wywoływane, gdy połączenie klienta jest rozłączone.|

## <a name="event-schema"></a>Schemat zdarzeń
Zdarzenia usługi Azure SignalR zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować zdarzenie usługi Azure SignalR service z eventType właściwość rozpoczyna się od "Microsoft.SignalRService". Dodatkowe informacje na temat użycia właściwości zdarzeń event grid są udokumentowane w [schemacie zdarzeń event grid](../event-grid/event-schema.md).  

Oto przykład zdarzenia połączonego połączenia z klientem:
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

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń usługi SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze Event Grid i wypróbuj zdarzenia usługi Azure SignalR Service:

> [!div class="nextstepaction"]
> [Wypróbuj przykładową integrację z siatką zdarzeń z usługą Azure SignalR Service](./signalr-howto-event-grid-integration.md)
> [About Event Grid](../event-grid/overview.md)
