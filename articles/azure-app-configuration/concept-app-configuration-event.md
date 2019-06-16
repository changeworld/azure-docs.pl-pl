---
title: Dające dodatnią reakcję na zdarzenia pary klucz wartość konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Usługa Azure Event Grid do subskrybowania zdarzenia konfiguracji aplikacji.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735649"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure

Azure — wydarzenia konfiguracji aplikacji umożliwiają aplikacjom reagować na zmiany w wartościach klucza. Odbywa się bez konieczności skomplikowanego kodu lub sondowania kosztowne i nieefektywna usług. Zamiast tego zdarzenia są przekazywane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) dla subskrybentów, takie jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet własne odbiornika http niestandardowego, a tylko płatność za rzeczywiste użycie.

Azure — wydarzenia konfiguracji aplikacji są wysyłane do usługi Azure Event Grid, która zapewnia niezawodne dostarczanie usług do aplikacji dzięki rozbudowanym funkcjom ponawiania, zasad i dostarczania wiadomości utraconych. Aby dowiedzieć się więcej, zobacz [dostarczanie komunikatów usługi Event Grid i ponów próbę](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Typowe scenariusze zdarzeń konfiguracji obejmują odświeżanie konfiguracji aplikacji, wyzwalania wdrożeń lub przepływ pracy korzystający z konfiguracji. Gdy zmiany są rzadkie, ale dany scenariusz wymaga natychmiastowej reakcji, oparte na zdarzeniach architektury może być szczególnie wydajne.

Przyjrzyj się [konfiguracji aplikacji usługi Azure Route zdarzenia niestandardowe sieci web punktu końcowego — interfejs wiersza polecenia](./howto-app-configuration-event.md) potrzeby krótkiego przykładu. 

![Model siatki zdarzeń](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Dostępności zdarzenia konfiguracji aplikacji platformy Azure
Korzysta z usługi Event grid [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Subskrypcje zdarzeń konfiguracji aplikacji może zawierać dwa typy zdarzeń:  

> |Nazwa zdarzenia|Opis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Po utworzeniu lub zastąpić pary klucz wartość|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Wywoływane, gdy klucz wartość zostanie usunięta.|

## <a name="event-schema"></a>Schemat zdarzeń
Konfiguracja aplikacji Azure — wydarzenia zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Zdarzenia konfiguracji aplikacji można zidentyfikować, ponieważ właściwość Typ zdarzenia, który rozpoczyna się od "Microsoft.AppConfiguration". Dodatkowe informacje na temat użycia właściwości zdarzeń usługi Event Grid jest udokumentowany w [schematu zdarzeń usługi Event Grid](../event-grid/event-schema.md).  

> |Właściwość|Typ|Opis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Pełny identyfikator usługi Azure Resource Manager konfiguracji aplikacji, który emituje zdarzenia.|
> |subject|string|Identyfikator URI klucz wartość, która jest przedmiotem zdarzenia.|
> |eventTime|string|Data/Godzina wygenerowania zdarzenia, w formacie ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" or "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Identyfikator|string|Unikatowy identyfikator tego zdarzenia.|
> |dataVersion|string|Wersja schematu dla obiektu danych.|
> |metadataVersion|string|Wersja schematu właściwości najwyższego poziomu.|
> |data|obiekt|Zbieranie danych określonego zdarzenia konfiguracji aplikacji platformy Azure|
> |Data.key|string|Klucz klucz wartość, która została zmieniona lub usunięta.|
> |data.label|string|Etykieta, jeśli istnieje klucz wartość, która została zmieniona lub usunięta.|
> |data.etag|string|Aby uzyskać `KeyValueModified` tag etag nowy klucz wartość. Aby uzyskać `KeyValueDeleted` tag etag klucz wartość, która została usunięta.|

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

Aby uzyskać więcej informacji, zobacz [schematu zdarzeń konfiguracji aplikacji usługi Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia
Aplikacje, które obsługują zdarzenia konfiguracji aplikacji, należy wykonać kilka zalecanych rozwiązań:
> [!div class="checklist"]
> * Zgodnie z wieloma subskrypcjami można skonfigurować w celu kierowanie zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, nie przyjęto założenie, że są zdarzenia z konkretnego źródła, ale Sprawdź tematu wiadomości, aby upewnić się, że pochodzi on z konfiguracji aplikacji, które są oczekiwane.
> * Podobnie upewnij się, że zdarzenia jest przygotowany do procesów, a nie należy zakładać, że wszystkie zdarzenia, które otrzymujesz będzie typów, których oczekujesz.
> * Nadejścia nowych wiadomości mogą poza kolejnością i po pewne opóźnienie, należy użyć pól element etag, aby zrozumieć, jeśli Twoje informacje, informacje o obiektach są nadal aktualne.  Ponadto użyć pola program sequencer, aby zrozumieć kolejności zdarzeń dla dowolnego określonego obiektu.
> * Używać pola tematu do dostępu klucz wartość, która została zmodyfikowana.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Event Grid i wypróbuj zdarzenia konfiguracji aplikacji platformy Azure:

- [Event Grid — informacje](../event-grid/overview.md)
- [Kierowanie zdarzeń usługi konfiguracji aplikacji platformy Azure do niestandardowego internetowego punktu końcowego](./howto-app-configuration-event.md)