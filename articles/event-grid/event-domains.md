---
title: Zdarzenie domeny w usłudze Azure Event Grid
description: W tym artykule opisano, jak domen zdarzenia są używane do zarządzania tematów w usłudze Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634055"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Omówienie domen zdarzeń związanych z zarządzaniem tematy usługi Event Grid

W tym artykule opisano sposób używania domen zdarzenie pozwala na zarządzanie przepływem zdarzeń niestandardowych w celu różne organizacje biznesowych, klientów lub aplikacji. Użyj domen zdarzeń do:

* Zarządzaj architektury wielodostępnej zdarzeń na dużą skalę.
* Zarządzaj autoryzacji i uwierzytelniania.
* Partycji tematów bez konieczności zarządzania każdym indywidualnie.
* Unikaj publikowania w indywidualnie do poszczególnych punktów końcowych tematu.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Omówienie domen zdarzeń

Domena zdarzeń jest narzędziem do zarządzania dla dużej liczby tematy usługi Event Grid powiązane z tej samej aplikacji. Można traktować go jako meta temat, który może zawierać tysięcy dla poszczególnych tematów.

Zdarzenie domeny należy architektury usług platformy Azure, takie jak Storage i Centrum IoT Hub umożliwia publikowanie ich zdarzenia, które są dostępne do użycia w systemie użytkownika. Pozwalają one na publikowanie zdarzeń do tysięcy tematów. Domen również zapewniają autoryzację i uwierzytelnianie kontrolę nad każdego tematu, dzięki czemu można podzielić dzierżawcy.

### <a name="example-use-case"></a>Przykład przypadek użycia

Zdarzenie domeny najłatwiej zostały wyjaśnione przy użyciu przykładu. Załóżmy, że uruchomieniu maszyny konstrukcji firmy Contoso, w którym produkowanych kół, digging urządzenia i inne duże maszyny. Jako część prowadzenie działalności w czasie rzeczywistym informacje wypychania dla klientów dotyczące konserwacji sprzętu, kondycję systemów kontraktu aktualizacje itp. Wszystkie te informacje przechodzi do różnych punktów końcowych, w tym aplikacji, punktów końcowych klienta, a inni klienci infrastruktury zostało skonfigurowane.

Zdarzenie domeny umożliwia model Contoso Construction maszyny jako całość jednym obsługi zdarzeń. Każdy z klientów jest reprezentowany jako tematu w obrębie domeny / uwierzytelnianie i autoryzacja są obsługiwane przy użyciu usługi Azure Active Directory. Każda z klientom subskrybowanie ich i przechodzić do nich ich zdarzenia dostarczone. Zarządzany dostęp za pośrednictwem domeny zdarzeń gwarantuje, że mają dostęp tylko ich temat.

Oferuje on również pojedynczego punktu końcowego, który można opublikować wszystkie zdarzenia klienta. Usługa Event Grid zajmie się upewnienie się, że każdy temat ma informacje tylko zdarzeń o określonym zakresie do swojej dzierżawy.

![Przykład konstrukcji firmy Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Zarządzanie dostępem

 Z domeną uzyskasz szczegółową kontrolę autoryzację i uwierzytelnianie za pośrednictwem każdego tematu za pomocą platformy Azure na podstawie ról dostępu Sprawdź (RBAC). Te role można użyć do ograniczenia poszczególnych dzierżaw w aplikacji do tematów, które chcesz przyznać im dostęp do.

Rola dostępu na podstawie Sprawdź (RBAC) w domenach zdarzeń działa tak samo [zarządzane kontroli dostępu](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) działa w pozostałej części usługi Event Grid i na platformie Azure. Tworzenie i wymuszanie definicji ról niestandardowych w domenach zdarzeń przy użyciu kontroli RBAC.

### <a name="built-in-roles"></a>Wbudowane role

Usługa Event Grid ma dwie definicje rolę wbudowaną, aby ułatwić RBAC:

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>Współautor EventSubscription EventGrid (wersja zapoznawcza)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>Czytnik EventSubscription EventGrid (wersja zapoznawcza)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>Subskrybowanie tematów

Subskrybowanie zdarzeń dla tematu w domenie zdarzeń jest taka sama jak [Tworzenie subskrypcji zdarzeń dla tematu niestandardowego](./custom-event-quickstart.md) lub subskrypcji wbudowanych ofert Azure wydawców zdarzeń.

### <a name="domain-scope-subscriptions"></a>Subskrypcje zakres domeny

Zdarzenie domeny pozwalają również subskrypcje zakres domeny. Subskrypcję zdarzeń w domenie usługi Event będą otrzymywać wszystkie zdarzenia wysyłane do domeny, niezależnie od wysłania zdarzenia do tematu. Subskrypcje zakres domeny może być przydatne do zarządzania i inspekcji.

## <a name="publishing-to-an-event-domain"></a>Publikowanie zdarzeń domeny

Podczas tworzenia domeny zdarzeń, jeśli temat została utworzona w usłudze Event Grid otrzymuje publikowania punktu końcowego, podobnego do. 

Do publikowania zdarzeń dowolnego tematu w domenie zdarzeń, wypychanie zdarzeń do punktu końcowego domeny [taki sam sposób jak w przypadku niestandardowego tematu](./post-to-custom-topic.md). Jedyna różnica polega na tym, czy należy określić temat chcesz zdarzeń, które mają zostać dostarczone do.

Na przykład następującą tablicę zdarzenia publikowania prześle zdarzenie z `"id": "1111"` do tematu `foo` podczas gdy zdarzenie o `"id": "2222"` będą wysyłane do tematu `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Zdarzenie domeny obsługiwać publikowanie tematów dla Ciebie. Zamiast publikowania zdarzeń do każdego tematu, z którymi można zarządzać indywidualnie, możesz opublikować wszystkie zdarzenia do punktu końcowego domeny i usługi Event Grid dba o zapewnienie, że każde zdarzenie jest wysyłane do prawidłowy temat.

## <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia

### <a name="control-plane"></a>Płaszczyzna kontroli

W trakcie okresu zapoznawczego domen zdarzeń będzie ograniczone do 1000 tematów w domenie i 50 subskrypcji zdarzeń dla tematu w domenie. Subskrypcje zakres domeny zdarzeń również będzie ograniczona do 50.

### <a name="data-plane"></a>Płaszczyzna danych

W trakcie okresu zapoznawczego przepływność zdarzeń dla zdarzenia domeny będzie ograniczona do tego samego zdarzenia 5000 na drugim stawka pozyskiwania tematy niestandardowe są ograniczone do.

## <a name="pricing"></a>Cennik

W trakcie okresu zapoznawczego domen zdarzeń będzie używać tego samego [operacje ceny](https://azure.microsoft.com/pricing/details/event-grid/) używanego przez wszystkie inne funkcje w usłudze Event Grid.

Operacje działać tak samo w domenach zdarzeń, jak w tematy niestandardowe. Każdy transferu danych przychodzących zdarzenia do domeny zdarzeń jest operacją i kolejnymi próbami dostarczenia zdarzeń jest operacją.

## <a name="next-steps"></a>Kolejne kroki

* Informacje na temat konfigurowania domen zdarzeń, zobacz temat Tworzenie tematów, Tworzenie subskrypcji zdarzeń i publikowania zdarzeń, [Zarządzanie domenami zdarzeń](./how-to-event-domains.md).