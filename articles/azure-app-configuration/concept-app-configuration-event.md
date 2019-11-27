---
title: Reagowanie na zdarzenia klucza konfiguracji aplikacji platformy Azure | Microsoft Docs
description: Użyj Azure Event Grid, aby subskrybować zdarzenia konfiguracji aplikacji.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185297"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure

Zdarzenia konfiguracji aplikacji platformy Azure umożliwiają aplikacjom reagowanie na zmiany w kluczowych wartościach. Jest to wykonywane bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zamiast tego zdarzenia są wypychane za pośrednictwem [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)lub nawet do własnego niestandardowego odbiornika HTTP i płacisz tylko za to, czego używasz.

Zdarzenia konfiguracji aplikacji platformy Azure są wysyłane do Azure Event Grid, które zapewniają niezawodne usługi dostarczania dla aplikacji dzięki rozbudowanym zasadom ponowień i dostarczaniu wiadomości utraconych. Aby dowiedzieć się więcej, zobacz [Event Grid dostarczania komunikatów i ponów próbę](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Typowe scenariusze zdarzeń konfiguracji aplikacji obejmują odświeżanie konfiguracji aplikacji, wyzwalanie wdrożeń lub przepływ pracy zorientowany na konfigurację. Gdy zmiany są rzadko wykonywane, ale scenariusz wymaga natychmiastowej reakcji, Architektura oparta na zdarzeniach może być szczególnie wydajna.

Zapoznaj się z tematem [kierowanie zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego punktu końcowego sieci Web — interfejs wiersza polecenia](./howto-app-configuration-event.md) na potrzeby szybkiego przykładu. 

![Model Event Grid](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Dostępne zdarzenia konfiguracji aplikacji platformy Azure
Funkcja Event Grid używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Subskrypcje zdarzeń konfiguracji aplikacji platformy Azure mogą zawierać dwa typy zdarzeń:  

> |Nazwa zdarzenia|Opis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Uruchamiany, gdy wartość klucza jest tworzona lub zastępowana|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Uruchamiany po usunięciu wartości klucza|

## <a name="event-schema"></a>Schemat zdarzeń
Zdarzenia konfiguracji aplikacji platformy Azure zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie konfiguracji aplikacji, ponieważ właściwość eventType zaczyna się od "Microsoft. AppConfiguration". Dodatkowe informacje na temat użycia właściwości zdarzeń Event Grid są udokumentowane w [Event Grid schemacie zdarzeń](../event-grid/event-schema.md).  

> |Właściwość|Typ|Opis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |temat|ciąg|Pełny Azure Resource Manager Identyfikator konfiguracji aplikacji, która emituje zdarzenie.|
> |subject|ciąg|Identyfikator URI wartości klucz-wartość, która jest podmiotem zdarzenia.|
> |eventTime|ciąg|Data/godzina wygenerowania zdarzenia w formacie ISO 8601.|
> |eventType|ciąg|"Microsoft. AppConfiguration. KeyValueModified" lub "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Identyfikator|ciąg|Unikatowy identyfikator tego zdarzenia.|
> |dataVersion|ciąg|Wersja schematu obiektu danych.|
> |metadataVersion|ciąg|Wersja schematu właściwości najwyższego poziomu.|
> |data|obiekt|Kolekcja danych zdarzeń specyficznych dla konfiguracji aplikacji platformy Azure|
> |Data. Key|ciąg|Klucz wartości, który został zmodyfikowany lub usunięty.|
> |Data. Label|ciąg|Etykieta (jeśli istnieje) wartości klucz-wartość, która została zmodyfikowana lub usunięta.|
> |Data. ETag|ciąg|Dla `KeyValueModified` element ETag nowej wartości key-value. Dla `KeyValueDeleted` element ETag, który został usunięty.|

Oto przykład zdarzenia KeyValueModified:
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

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń
Aplikacje, które obsługują zdarzenia konfiguracji aplikacji, powinny spełniać kilka zalecanych praktyk:
> [!div class="checklist"]
> * Można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, dlatego ważne jest, aby nie przyjąć zdarzeń z określonego źródła, ale w celu upewnienia się, że pochodzi on z konfiguracji aplikacji, której oczekujesz.
> * Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
> * Ponieważ komunikaty mogą się pojawiać poza kolejnością i po pewnym opóźnieniu, należy użyć pól ETag, aby zrozumieć, czy informacje o obiektach są nadal aktualne.  Ponadto należy użyć pól programu Sequencer do zrozumienia kolejności zdarzeń dla każdego określonego obiektu.
> * Użyj pola podmiot, aby uzyskać dostęp do zmodyfikowanej wartości klucz-wartość.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i wydawanie zdarzeń konfiguracji aplikacji platformy Azure spróbuj:

- [Event Grid — informacje](../event-grid/overview.md)
- [Kierowanie zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego internetowego punktu końcowego](./howto-app-configuration-event.md)