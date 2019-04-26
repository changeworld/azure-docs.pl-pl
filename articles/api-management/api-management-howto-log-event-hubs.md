---
title: Jak rejestrowanie zdarzeń w usłudze Azure Event Hubs w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rejestrowanie zdarzeń w usłudze Azure Event Hubs w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 14f84b5380a1c106114cdab425de7f69f4e19825
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657547"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak rejestrowanie zdarzeń w usłudze Azure Event Hubs w usłudze Azure API Management
Azure Event Hubs to wysoce skalowalna usługa transferu danych przychodzących, która może obsługiwać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Usługa Event Hubs działa jako "drzwi wejściowe" dla potoku zdarzeń i zebrane dane do Centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym lub adapterów przetwarzania wsadowego/magazynowania. Usługa Event Hubs oddziela wytwarzanie strumienia zdarzeń od użycia tych zdarzeń, dzięki czemu odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem.

W tym artykule jest uzupełnieniem do [integracji usługi Azure API Management z usługą Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) wideo i zawiera opis sposobu rejestrowania zdarzeń usługi API Management przy użyciu usługi Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia Centrum zdarzeń i pobieranie parametrów połączenia, które są potrzebne do wysyłania i odbierania zdarzeń z Centrum zdarzeń i zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Tworzenie usługi API Management rejestratora
Teraz, gdy Centrum zdarzeń, następnym krokiem jest skonfigurowanie [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) w usługi API Management service, aby zarejestrować zdarzenia do Centrum zdarzeń.

Rejestratory usługi API Management są skonfigurowane przy użyciu [interfejsu API REST zarządzania interfejsu API](https://aka.ms/smapi). Przed rozpoczęciem korzystania z interfejsu API REST, po raz pierwszy, przejrzyj [wymagania wstępne](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) i upewnij się, że masz [włączono dostęp do interfejsu API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Aby utworzyć rejestratora, należy żądanie HTTP PUT, korzystając z poniższego szablonu adresu URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Zastąp `{your service}` nazwą wystąpienia usługi API Management.
* Zastąp `{new logger name}` z żądaną nazwą dla Twojego nowego rejestratora. Możesz odwoływać się do tej nazwy podczas konfigurowania [dziennika do Centrum zdarzeń](/azure/api-management/api-management-advanced-policies#log-to-eventhub) zasad

Dodaj następujące nagłówki żądania:

* Content-Type : application/json
* Autoryzacja: SharedAccessSignature 58...
  * Aby uzyskać instrukcje dotyczące generowania `SharedAccessSignature` zobacz [uwierzytelniania interfejsu API REST zarządzania interfejsu API Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Określanie treści żądania, korzystając z poniższego szablonu:

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

* `loggerType` musi być równa `AzureEventHub`.
* `description` zawiera opcjonalny opis rejestratora i może być ciągiem o zerowej długości, w razie potrzeby.
* `credentials` zawiera `name` i `connectionString` Centrum zdarzeń platformy Azure.

Po ustawieniu żądania, jeśli rejestrator jest tworzona, kod stanu `201 Created` jest zwracana. Przykładowa odpowiedź na podstawie powyższego żądania przykładowe znajdują się poniżej.

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
> Inne możliwe kody powrotne i ich przyczyn, zobacz [utworzyć rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Aby zobaczyć, jak wykonywać inne operacje, takie jak listy, update i delete, zobacz [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dokumentacja jednostki.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurowanie zasad log-eventhubs

Po skonfigurowaniu usługi rejestrowania w usłudze API Management można skonfigurować zasad log-eventhubs służące do rejestrowania zdarzeń żądaną. Zasady log-eventhubs może służyć w sekcji zasady ruchu przychodzącego lub w sekcji zasady danych wyjściowych.

1. Przejdź do swojego wystąpienia usługi APIM.
2. Wybierz kartę interfejsu API.
3. Wybierz interfejs API, do którego chcesz dodać zasady. W tym przykładzie dodajemy zasad w celu **interfejsu Echo API** w **nieograniczone** produktu.
4. Wybierz opcję **Wszystkie operacje**.
5. W górnej części ekranu wybierz kartę projektu.
6. W oknie przetwarzanie przychodzącej lub wychodzącej kliknij trójkąt (obok ołówka).
7. Wybierz edytor kodu. Aby uzyskać więcej informacji, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).
8. Umieść kursor w `inbound` lub `outbound` sekcji zasady.
9. W oknie po prawej stronie wybierz **zaawansowane zasady** > **dziennika usługi eventhub**. Spowoduje to wstawienie `log-to-eventhub` szablon zasad w instrukcji.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Zastąp `logger-id` o wartości używane dla `{new logger name}` w adresie URL, aby utworzyć rejestratora w poprzednim kroku.

Można użyć dowolnego wyrażenia, które zwraca ciąg jako wartość `log-to-eventhub` elementu. W tym przykładzie ciąg zawierający daty i godziny, nazwa usługi, identyfikator żądania, adres ip żądania oraz nazwa operacji jest rejestrowany.

Kliknij przycisk **Zapisz** można zapisać konfiguracji zaktualizowane zasady. Jak najszybciej po jego zapisaniu zasad jest aktywny i zdarzenia są rejestrowane do wyznaczonego Centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat usługi Azure Event Hubs
  * [Rozpoczynanie pracy z usługą Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik programowania w usłudze Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej na temat integracji usługi API Management i centrów zdarzeń
  * [Odwołanie do jednostki rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [informacje o zasadach dziennika do Centrum zdarzeń](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorowanie za pomocą usługi Azure API Management, Event Hubs i Moesif interfejsów API](api-management-log-to-eventhub-sample.md)  
* Dowiedz się więcej o [integracji z usługą Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
