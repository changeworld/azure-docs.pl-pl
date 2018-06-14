---
title: Jak mają być rejestrowane zdarzenia do usługi Azure Event Hubs w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Informacje o sposobie rejestrowania zdarzeń do usługi Azure Event Hubs w usłudze Azure API Management.
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
ms.openlocfilehash: 3f4da70d94d28496f5b08035ead0ef7acf1ca3bc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29969601"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak mają być rejestrowane zdarzenia do usługi Azure Event Hubs w usłudze Azure API Management
Azure Event Hubs to wysoce skalowalna usługa transferu danych przychodzących, która może obsługiwać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Usługa Event Hubs działa jako "drzwi wejściowe" dla potoku zdarzeń, a po pobraniu danych do Centrum zdarzeń, można je przekształcać i przechowywane za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub kart przetwarzania wsadowego i magazynowania. Usługa Event Hubs oddziela wytwarzanie strumienia zdarzeń od użycia tych zdarzeń, dzięki czemu odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem.

W tym artykule jest dodatek do [zintegrować zarządzanie interfejsami API Azure z usługą Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) wideo i opisuje sposób rejestrowania zdarzeń interfejsu API zarządzania przy użyciu usługi Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Tworzenie Centrum zdarzeń platformy Azure

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia Centrum zdarzeń i pobrać parametry połączenia, które są potrzebne do wysyłania i odbierania zdarzeń z Centrum zdarzeń i zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą portalu Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Utworzyć zarządzanie interfejsami API rejestratora
Teraz, gdy masz Centrum zdarzeń, następnym krokiem jest skonfigurowanie [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) w zarządzania infrastrukturą interfejsu API usługi tak, aby móc zarejestrować zdarzenia do Centrum zdarzeń.

Zarządzanie interfejsami API rejestratorów są skonfigurowane przy użyciu [interfejsu API REST zarządzania interfejsu API](http://aka.ms/smapi). Przed rozpoczęciem korzystania z interfejsu API REST po raz pierwszy, przejrzyj [wymagania wstępne](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) i upewnij się, że masz [włączony dostęp do interfejsu API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Aby utworzyć rejestrator, należy żądanie HTTP PUT, korzystając z poniższego szablonu adresu URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Zastąp `{your service}` nazwą wystąpienia usługi Zarządzanie interfejsami API.
* Zastąp `{new logger name}` z żądaną nazwą dla Twojego nowego rejestratora. Ta nazwa jest odwołanie podczas konfigurowania [dziennika do Centrum eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) zasad

Dodaj następujące nagłówki na żądanie:

* Content-Type: application/json
* Autoryzacji: SharedAccessSignature 58...
  * Aby uzyskać instrukcje dotyczące generowania `SharedAccessSignature` zobacz [Azure API Management REST API uwierzytelniania](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Określ treści żądania przy użyciu następującego szablonu:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType` należy wybrać opcję `AzureEventHub`.
* `description` zapewnia opcjonalny opis rejestratora i w razie potrzeby może być ciągiem o zerowej długości.
* `credentials` zawiera `name` i `connectionString` Azure Centrum zdarzeń.

Po dokonaniu żądania, jeśli rejestrator jest tworzony kod stanu `201 Created` jest zwracany.

> [!NOTE]
> Pozostałe możliwe kody powrotu i ich przyczyny, zobacz [utworzyć Rejestrator](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Aby sprawdzić, jak wykonywanie innych operacji, takich jak listy, update i delete, zobacz [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dokumentacji jednostki.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurowanie zasad dziennika do eventhubs

Po Twoje rejestratora jest skonfigurowany w usłudze API Management, można skonfigurować zasad dziennika do eventhubs żądanego zdarzenia logowania. Zasada dziennika do eventhubs może służyć w sekcji zasady ruchu przychodzącego lub sekcji zasady ruchu wychodzącego.

1. Przejdź do swojego wystąpienia usługi APIM.
2. Wybierz kartę interfejsu API.
3. Wybierz interfejs API, do której chcesz dodać zasady. W tym przykładzie Trwa dodawanie zasad, aby **Echo API** w **nieograniczone** produktu.
4. Wybierz **wszystkie operacje**.
5. W górnej części ekranu wybierz karty Projekt.
6. W oknie przetwarzanie przychodzącej lub wychodzącej kliknij trójkąt (obok ołówka).
7. Wybierz edytora kodu. Aby uzyskać więcej informacji, zobacz [jak ustawić lub edytować zasady](set-edit-policies.md).
8. Umieść kursor w `inbound` lub `outbound` sekcji zasad.
9. W oknie po prawej stronie wybierz **zaawansowane zasady** > **dziennika do Centrum EventHub**. Wstawia `log-to-eventhub` szablon zasad w instrukcji.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Zastąp `logger-id` nazwą rejestratora zarządzanie interfejsami API skonfigurowana w poprzednim kroku.

Można użyć dowolne wyrażenie zwracające ciąg jako wartość `log-to-eventhub` elementu. W tym przykładzie jest rejestrowane ciąg zawierający daty i godziny, nazwę usługi, identyfikator żądania, żądania adresu ip oraz nazwy operacji.

Kliknij przycisk **zapisać** Aby zapisać konfigurację zaktualizowane zasady. Natychmiast po ich zapisaniu zasady są aktywne, a zdarzenia są rejestrowane w wyznaczonym Centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat usługi Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik programowania w usłudze Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej na temat integracji usługi API Management i usługi Event Hubs
  * [Odwołanie do jednostki rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [informacje o zasadach dziennika do Centrum eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitoruj swoje interfejsy API z usługi Azure API Management, usługa Event Hubs i Runscope](api-management-log-to-eventhub-sample.md)    

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
