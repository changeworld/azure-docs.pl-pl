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
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073486"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management
Azure Event Hubs to wysoce skalowalna usługa transferu danych przychodzących, która może obsługiwać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Event Hubs działa jako "drzwi tylne" dla potoku zdarzeń, a po zebraniu danych do centrum zdarzeń można je przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub kart wsadowych/magazynowych. Usługa Event Hubs oddziela wytwarzanie strumienia zdarzeń od użycia tych zdarzeń, dzięki czemu odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem.

Ten artykuł stanowi pomocnika [integracji API Management platformy Azure z Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) wideo i opisuje sposób rejestrowania zdarzeń API Management przy użyciu usługi Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia centrum zdarzeń i uzyskiwania parametrów połączenia, które są potrzebne do wysyłania i odbierania zdarzeń do i z centrum zdarzeń, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Tworzenie rejestratora API Management
Teraz, gdy masz centrum zdarzeń, następnym krokiem jest skonfigurowanie rejestratora w usłudze [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) API Management, dzięki czemu może on rejestrować zdarzenia w centrum zdarzeń.

Rejestratory API Management są konfigurowane przy użyciu [interfejsu API REST API Management](https://aka.ms/smapi). Przed rozpoczęciem korzystania z interfejsu API REST po raz pierwszy Sprawdź [wymagania wstępne](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) i upewnij się, że [włączono dostęp do interfejsu API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Aby utworzyć rejestrator, wprowadź żądanie HTTP PUT przy użyciu następującego szablonu adresu URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Zamień `{your service}` na nazwę wystąpienia usługi API Management.
* Zamień `{new logger name}` na żądaną nazwę nowego rejestratora. Odwołuje się do tej nazwy podczas konfigurowania zasad [logowania do centrum eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)

Dodaj następujące nagłówki do żądania:

* Content-Type: Application/JSON
* Zgody SharedAccessSignature 58...
  * Aby uzyskać instrukcje dotyczące generowania `SharedAccessSignature` , zobacz temat [uwierzytelnianie interfejsu API REST platformy Azure API Management](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Określ treść żądania przy użyciu następującego szablonu:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`musi być ustawiony na `AzureEventHub`.
* `description`zawiera opcjonalny opis rejestratora, w razie potrzeby może być ciągiem o zerowej długości.
* `credentials``name` zawiera i `connectionString` centrum zdarzeń platformy Azure.

Po wprowadzeniu żądania, gdy zostanie utworzony rejestrator, zwracany jest kod stanu elementu `201 Created` . Poniżej przedstawiono przykładową odpowiedź opartą na powyższym żądaniu przykładowym.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Aby uzyskać inne możliwe kody powrotne i ich przyczyny, zobacz [Tworzenie rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Aby dowiedzieć się, jak wykonywać inne operacje, takie jak list, Update i DELETE, [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) Zobacz dokumentację jednostki rejestratora.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurowanie zasad logowania do eventhubs

Po skonfigurowaniu rejestratora w API Management można skonfigurować zasady logowania do eventhubs w celu rejestrowania żądanych zdarzeń. Zasad logowanie do eventhubs można użyć w sekcji zasady ruchu przychodzącego lub w sekcji zasady wychodzące.

1. Przejdź do swojego wystąpienia usługi APIM.
2. Wybierz kartę interfejs API.
3. Wybierz interfejs API, do którego chcesz dodać zasady. W tym przykładzie dodamy zasady do **interfejsu API ECHA** w nieograniczonego produktu .
4. Wybierz opcję **Wszystkie operacje**.
5. W górnej części ekranu wybierz kartę projektowanie.
6. W oknie przetwarzanie przychodzące lub wychodzące kliknij trójkąt (obok ołówka).
7. Wybierz Edytor kodu. Aby uzyskać więcej informacji, zobacz [jak ustawić lub edytować zasady](set-edit-policies.md).
8. Umieść kursor w `inbound` sekcji lub `outbound` zasad.
9. W oknie po prawej stronie wybierz pozycję dziennik **zasad** > zaawansowanych w**centrum EventHub**. Spowoduje to wstawienie `log-to-eventhub` szablonu instrukcji zasad.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Zamień `logger-id` na wartość `{new logger name}` użytą w adresie URL, aby utworzyć rejestrator w poprzednim kroku.

Możesz użyć dowolnego wyrażenia, które zwraca ciąg jako wartość `log-to-eventhub` elementu. W tym przykładzie jest rejestrowany ciąg zawierający datę i godzinę, nazwę usługi, identyfikator żądania, adres IP żądania i nazwę operacji.

Kliknij przycisk **Zapisz** , aby zapisać zaktualizowaną konfigurację zasad. Gdy tylko zostanie zapisany, zasady są aktywne, a zdarzenia są rejestrowane w wydzielonym centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy eventprocessorhost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik programowania w usłudze Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej o integracji API Management i Event Hubs
  * [Odwołanie do jednostki rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
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
