---
title: Reagowanie na zdarzenia klucza-wartość konfiguracji aplikacji platformy Azure
description: Użyj usługi Azure Event Grid, aby subskrybować zdarzenia konfiguracji aplikacji.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523802"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure

Zdarzenia konfiguracji aplikacji platformy Azure umożliwiają aplikacjom reagowanie na zmiany wartości klucza. Odbywa się to bez konieczności skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zamiast tego zdarzenia są wypychane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak usługi Azure [Functions,](https://azure.microsoft.com/services/functions/) [Usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), a nawet do własnego niestandardowego odbiornika http. Co ważne, płacisz tylko za to, czego używasz.

Zdarzenia konfiguracji aplikacji platformy Azure są wysyłane do usługi Azure Event Grid, która zapewnia niezawodne usługi dostarczania do aplikacji za pośrednictwem rozszerzonych zasad ponawiania prób i dostarczania utraconych wiadomości. Aby dowiedzieć się więcej, zobacz [Dostarczanie wiadomości w ucho i ponów próbę .](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

Typowe scenariusze zdarzeń konfiguracji aplikacji obejmują odświeżanie konfiguracji aplikacji, wyzwalanie wdrożeń lub dowolny przepływ pracy zorientowany na konfigurację. Gdy zmiany są rzadkie, ale scenariusz wymaga natychmiastowej reakcji, architektura oparta na zdarzeniach może być szczególnie wydajna.

Spójrz na [routać zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego punktu końcowego sieci web — interfejs wiersza polecenia](./howto-app-configuration-event.md) na przykład. 

![Model siatki zdarzeń](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Zdarzenia konfiguracji dostępnej aplikacji platformy Azure
Siatka zdarzeń używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania wiadomości zdarzeń do subskrybentów. Subskrypcje zdarzeń konfiguracji aplikacji platformy Azure mogą zawierać dwa typy zdarzeń:  

> |Nazwa wydarzenia|Opis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Uruchamiane po utworzeniu lub zastąpieniu wartości klucza|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Uruchamiane po usunięciu wartości klucza|

## <a name="event-schema"></a>Schemat zdarzeń
Zdarzenia konfiguracji aplikacji platformy Azure zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować zdarzenie konfiguracji aplikacji, ponieważ właściwość eventType rozpoczyna się od "Microsoft.AppConfiguration". Dodatkowe informacje na temat użycia właściwości zdarzeń event grid są udokumentowane w [schemacie zdarzeń event grid](../event-grid/event-schema.md).  

> |Właściwość|Typ|Opis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |temat|ciąg|Pełny identyfikator usługi Azure Resource Manager konfiguracji aplikacji, który emituje zdarzenie.|
> |Temat|ciąg|Identyfikator URI wartości klucza, która jest przedmiotem zdarzenia.|
> |eventTime|ciąg|Data/godzina wygenerowania zdarzenia w formacie ISO 8601.|
> |Eventtype|ciąg|"Microsoft.AppConfiguration.KeyValueModified" lub "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Identyfikator|ciąg|Unikatowy identyfikator tego zdarzenia.|
> |dataVersion|ciąg|Wersja schematu obiektu danych.|
> |metadataVersion|ciąg|Wersja schematu właściwości najwyższego poziomu.|
> |dane|obiekt|Zbieranie danych zdarzeń określonych w konfiguracji aplikacji platformy Azure|
> |data.key|ciąg|Klucz klucza wartość, który został zmodyfikowany lub usunięty.|
> |data.label|ciąg|Etykieta, jeśli istnieje, wartości klucza, który został zmodyfikowany lub usunięty.|
> |data.etag|ciąg|Dla `KeyValueModified` etag nowej wartości klucza. Dla `KeyValueDeleted` etag wartości klucza, który został usunięty.|

Oto przykład keyvaluemodified zdarzenia:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń konfiguracji aplikacji platformy Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Praktyki dotyczące spożywania zdarzeń
Aplikacje obsługujące zdarzenia konfiguracji aplikacji powinny stosować się do następujących zalecanych rozwiązań:
> [!div class="checklist"]
> * Wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń, więc nie zakładaj, że zdarzenia pochodzą z określonego źródła. Zamiast tego sprawdź temat wiadomości, aby upewnić się, że wystąpienie konfiguracji aplikacji wysyłające zdarzenie.
> * Sprawdź eventType i nie zakładaj, że wszystkie zdarzenia, które otrzymasz będą typy, których oczekujesz.
> * Użyj pól etag, aby dowiedzieć się, czy informacje o obiektach są nadal aktualne.  
> * Użyj pól sekwencera, aby zrozumieć kolejność zdarzeń na dowolnym konkretnym obiekcie.
> * Użyj pola tematu, aby uzyskać dostęp do zmodyfikowanej wartości klucza.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o siatce zdarzeń i wypróbuj zdarzenia konfiguracji aplikacji platformy Azure:

- [Event Grid — informacje](../event-grid/overview.md)
- [Kierowanie zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego punktu końcowego sieci Web](./howto-app-configuration-event.md)