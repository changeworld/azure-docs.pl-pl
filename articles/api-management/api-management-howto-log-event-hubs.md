---
title: Jak rejestrować zdarzenia w centrum zdarzeń platformy Azure w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak rejestrować zdarzenia w centrum zdarzeń platformy Azure w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 2f07f6a27e78ee4df8c64a09918758d02c28c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76898789"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak rejestrować zdarzenia w centrum zdarzeń platformy Azure w usłudze Azure API Management
Azure Event Hubs to wysoce skalowalna usługa transferu danych przychodzących, która może obsługiwać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Usługa Event Hubs działa jako "drzwi wejściowe" dla potoku zdarzeń, a gdy dane są zbierane w centrum zdarzeń, mogą być przekształcane i przechowywane przy użyciu dowolnego dostawcy analizy w czasie rzeczywistym lub kart przetwarzania wsadowego/magazynu. Usługa Event Hubs oddziela wytwarzanie strumienia zdarzeń od użycia tych zdarzeń, dzięki czemu odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem.

Ten artykuł jest towarzyszem [integracji usługi Azure API Management z centrum zdarzeń](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) wideo i opisano, jak rejestrować zdarzenia zarządzania interfejsami API przy użyciu usługi Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia centrum zdarzeń i pobierania ciągów połączeń, które należy wysyłać i odbierać zdarzenia do i z Centrum zdarzeń, zobacz [Tworzenie obszaru nazw centrum zdarzeń i centrum zdarzeń za pomocą portalu Azure.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

## <a name="create-an-api-management-logger"></a>Tworzenie rejestratora zarządzania interfejsami API
Teraz, gdy masz Centrum zdarzeń, następnym krokiem jest skonfigurowanie [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) w usłudze zarządzania interfejsami API, tak aby mógł rejestrować zdarzenia w Centrum zdarzeń.

Rejestratory zarządzania interfejsami API są konfigurowane przy użyciu [interfejsu API REST zarządzania interfejsem API](https://aka.ms/apimapi). Aby uzyskać szczegółowe przykłady żądań, zobacz [jak utworzyć rejestratory](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate).

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurowanie zasad dzienników do zdarzeń

Po skonfigurowaniu rejestratora w usłudze API Management można skonfigurować zasady witryn logowania do zdarzeń w celu rejestrowania żądanych zdarzeń. Zasady log-to-eventhubs mogą być używane w sekcji zasad przychodzących lub w sekcji zasad ruchu wychodzącego.

1. Przejdź do swojego wystąpienia usługi APIM.
2. Wybierz kartę API.
3. Wybierz interfejs API, do którego chcesz dodać zasady. W tym przykładzie dodajemy zasady do **interfejsu API Echo** w produkcie **Unlimited.**
4. Wybierz opcję **Wszystkie operacje**.
5. W górnej części ekranu wybierz kartę Projektowanie.
6. W oknie Przetwarzania przychodzące lub Wychodzące kliknij trójkąt (obok ołówka).
7. Wybierz edytor kodu. Aby uzyskać więcej informacji, zobacz [Jak ustawić lub edytować zasady](set-edit-policies.md).
8. Umieść kursor w `inbound` sekcji `outbound` lub zasad.
9. W oknie po prawej stronie wybierz pozycję **Zaawansowane zasady** > **Logowanie do usługi EventHub**. Spowoduje to `log-to-eventhub` wstawienie szablonu instrukcji zasad.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Zamień `logger-id` na wartość `{new logger name}` używaną w adresie URL, aby utworzyć rejestrator w poprzednim kroku.

Można użyć dowolnego wyrażenia, które zwraca ciąg `log-to-eventhub` jako wartość dla elementu. W tym przykładzie rejestrowany jest ciąg zawierający datę i godzinę, nazwę usługi, identyfikator żądania, adres IP żądania i nazwę operacji.

Kliknij **przycisk Zapisz,** aby zapisać zaktualizowaną konfigurację zasad. Po zapisaniu zasady są aktywne, a zdarzenia są rejestrowane w wyznaczonym Centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik po programowaniu centrów zdarzeń](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej o integracji z centrum zarządzania interfejsami API i centrami zdarzeń
  * [Odwołanie do encji rejestratora](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [log-to-eventhub odwołanie do zasad](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorowanie interfejsów API za pomocą usługi Azure API Management, Event Hubs i Moesif](api-management-log-to-eventhub-sample.md)  
* Dowiedz się więcej o [integracji z usługą Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
