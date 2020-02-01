---
title: Jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898789"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management
Azure Event Hubs to wysoce skalowalna usługa transferu danych przychodzących, która może obsługiwać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Event Hubs działa jako "drzwi tylne" dla potoku zdarzeń, a po zebraniu danych do centrum zdarzeń można je przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub kart wsadowych/magazynowych. Usługa Event Hubs oddziela wytwarzanie strumienia zdarzeń od użycia tych zdarzeń, dzięki czemu odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem.

Ten artykuł stanowi pomocnika [integracji API Management platformy Azure z Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) wideo i opisuje sposób rejestrowania zdarzeń API Management przy użyciu usługi Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia centrum zdarzeń i uzyskiwania parametrów połączenia, które są potrzebne do wysyłania i odbierania zdarzeń do i z centrum zdarzeń, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Tworzenie rejestratora API Management
Teraz, gdy masz centrum zdarzeń, następnym krokiem jest skonfigurowanie [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) w usłudze API Management, dzięki czemu może on rejestrować zdarzenia w centrum zdarzeń.

Rejestratory API Management są konfigurowane przy użyciu [interfejsu API REST API Management](https://aka.ms/apimapi). Szczegółowe przykłady żądań można znaleźć w temacie [How to Create rejestrators](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate).

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurowanie zasad logowania do eventhubs

Po skonfigurowaniu rejestratora w API Management można skonfigurować zasady logowania do eventhubs w celu rejestrowania żądanych zdarzeń. Zasad logowanie do eventhubs można użyć w sekcji zasady ruchu przychodzącego lub w sekcji zasady wychodzące.

1. Przejdź do swojego wystąpienia usługi APIM.
2. Wybierz kartę interfejs API.
3. Wybierz interfejs API, do którego chcesz dodać zasady. W tym przykładzie dodamy zasady do **interfejsu API ECHA** w **nieograniczonego** produktu.
4. Wybierz opcję **Wszystkie operacje**.
5. W górnej części ekranu wybierz kartę projektowanie.
6. W oknie przetwarzanie przychodzące lub wychodzące kliknij trójkąt (obok ołówka).
7. Wybierz Edytor kodu. Aby uzyskać więcej informacji, zobacz [jak ustawić lub edytować zasady](set-edit-policies.md).
8. Umieść kursor w sekcji zasad `inbound` lub `outbound`.
9. W oknie po prawej stronie wybierz pozycję **Zasady zaawansowane** > **log w centrum EventHub**. Spowoduje to wstawienie szablonu instrukcji `log-to-eventhub` Policy.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Zastąp `logger-id` wartością użytą dla `{new logger name}` w adresie URL, aby utworzyć rejestrator w poprzednim kroku.

Możesz użyć dowolnego wyrażenia, które zwraca ciąg jako wartość dla elementu `log-to-eventhub`. W tym przykładzie jest rejestrowany ciąg zawierający datę i godzinę, nazwę usługi, identyfikator żądania, adres IP żądania i nazwę operacji.

Kliknij przycisk **Zapisz** , aby zapisać zaktualizowaną konfigurację zasad. Gdy tylko zostanie zapisany, zasady są aktywne, a zdarzenia są rejestrowane w wydzielonym centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy eventprocessorhost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik programowania w usłudze Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej o integracji API Management i Event Hubs
  * [Odwołanie do jednostki rejestratora](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [Dokumentacja zasad logowania do centrum eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitoruj interfejsy API przy użyciu usługi Azure API Management, Event Hubs i Moesif](api-management-log-to-eventhub-sample.md)  
* Dowiedz się więcej [na temat integracji z usługą Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
