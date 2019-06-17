---
title: Zdarzenie domeny w usłudze Azure Event Grid
description: W tym artykule opisano, jak domen zdarzenia są używane do zarządzania tematów w usłudze Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 61821caa2450096bdbdde3461316ad21a82f6f18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304297"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Omówienie domen zdarzeń związanych z zarządzaniem tematy usługi Event Grid

W tym artykule opisano sposób używania domen zdarzenie pozwala na zarządzanie przepływem zdarzenia niestandardowe do różnych organizacji biznesowych, klientów lub aplikacji. Użyj domen zdarzeń do:

* Zarządzaj architektury wielodostępnej zdarzeń na dużą skalę.
* Zarządzaj autoryzacji i uwierzytelniania.
* Partycji tematów bez konieczności zarządzania każdym indywidualnie.
* Unikaj publikowania w indywidualnie do poszczególnych punktów końcowych tematu.

## <a name="event-domain-overview"></a>Omówienie usługi Event domeny

Domena zdarzeń jest narzędziem do zarządzania dla dużej liczby tematy usługi Event Grid powiązane z tej samej aplikacji. Jego można traktować jako meta temat, który może mieć tysięcy dla poszczególnych tematów.

Zdarzenie domeny zapewnić dostępność taką samą architekturę używane przez usługi platformy Azure (takich jak Storage i Centrum IoT Hub) do publikowania ich zdarzenia. Pozwalają one na publikowanie zdarzeń do tysięcy tematów. Domen również zapewniają autoryzację i uwierzytelnianie kontrolę nad każdego tematu, dzięki czemu można podzielić dzierżawcy.

### <a name="example-use-case"></a>Przykładowy przypadek użycia

Zdarzenie domeny najłatwiej zostały wyjaśnione przy użyciu przykładu. Załóżmy, że uruchomieniu maszyny konstrukcji firmy Contoso, w którym produkowanych kół, digging urządzenia i inne duże maszyny. Jako część prowadzenie działalności w czasie rzeczywistym informacje wypchnięty do klientów o konserwacji sprzętu, systemów kondycji i aktualizacje kontraktu. Wszystkie te informacje odnoszą się do różnych punktów końcowych, w tym aplikacji, punktów końcowych klienta i innych infrastrukturę, która klientów zostały skonfigurowane.

Zdarzenie domeny umożliwiają model Contoso Construction maszyny jako całość jednym obsługi zdarzeń. Każdy z klientów jest przedstawiana jako tematu w domenie. Uwierzytelnianie i autoryzacja są obsługiwane w usłudze Azure Active Directory. Każda z klientom subskrybowanie ich i przechodzić do nich ich zdarzenia dostarczone. Zarządzany dostęp za pośrednictwem domeny zdarzeń gwarantuje, że mają dostęp tylko ich temat.

Oferuje on również pojedynczego punktu końcowego, który można opublikować wszystkie zdarzenia klienta. Usługa Event Grid zajmie się upewnienie się, że każdy temat ma informacje tylko zdarzeń o określonym zakresie do swojej dzierżawy.

![Przykład konstrukcji firmy Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Zarządzanie dostępem

Z domeną możesz uzyskać szczegółową kontrolę autoryzację i uwierzytelnianie za pośrednictwem każdego tematu przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC). Te role można użyć do ograniczenia poszczególnych dzierżaw w aplikacji do tematów, które chcesz przyznać im dostęp do.

RBAC w domenach zdarzeń działa tak samo [zarządzane kontroli dostępu](security-authentication.md#management-access-control) działa w pozostałej części usługi Event Grid i na platformie Azure. Tworzenie i wymuszanie definicji ról niestandardowych w domenach zdarzeń przy użyciu kontroli RBAC.

### <a name="built-in-roles"></a>Wbudowane role

Usługa Event Grid ma dwie definicje wbudowana rola, aby ułatwić RBAC do pracy z domenami zdarzeń. Te role są **EventGrid EventSubscription współautora (wersja zapoznawcza)** i **EventGrid EventSubscription Reader (wersja zapoznawcza)** . Te role są przypisywane do użytkowników, którzy muszą subskrybować tematy w domenie zdarzeń. Możesz określić zakres przypisania roli do tematu, który użytkownicy potrzebują do subskrybowania.

Aby uzyskać informacje o tych rolach, zobacz [wbudowanych ról dla usługi Event Grid](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Subskrybowanie tematów

Subskrybowanie zdarzeń dla tematu w domenie zdarzeń jest taka sama jak [Tworzenie subskrypcji zdarzeń dla tematu niestandardowego](./custom-event-quickstart.md) i subskrybowaniem zdarzeń z usługi platformy Azure.

### <a name="domain-scope-subscriptions"></a>Subskrypcje zakres domeny

Zdarzenie domeny pozwalają również zakres domeny subskrypcji. Subskrypcję zdarzeń w domenie usługi event będą otrzymywać wszystkie zdarzenia wysyłane do domeny, niezależnie od wysłania zdarzenia do tematu. Subskrypcje zakres domeny może być przydatne do zarządzania i inspekcji.

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

Zdarzenie domeny obsługiwać publikowanie tematów dla Ciebie. Zamiast publikowania zdarzeń do każdego tematu, z którymi można zarządzać indywidualnie możesz opublikować wszystkie zdarzenia do punktu końcowego w domenie. Usługa Event Grid sprawia, że się, że każde zdarzenie jest wysyłane do prawidłowy temat.

## <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia
Poniżej przedstawiono limity przydziału i ograniczenia związane z domenami zdarzeń:

- 100 000 tematów w każdej domenie zdarzeń 
- 100 domen zdarzeń na subskrypcję platformy Azure 
- 500 subskrypcji zdarzeń na temat w domenie zdarzeń
- 50 subskrypcji zakres domeny 
- 5000 zdarzeń na drugim szybkości pozyskiwania (do domeny)

Jeśli nie spełnia te limity, należy, korzystał z zespół pracujący nad produktem, otwierając bilet pomocy technicznej lub wysyłając wiadomość e-mail na adres [ askgrid@microsoft.com ](mailto:askgrid.microsoft.com). 

## <a name="pricing"></a>Cennik
Zdarzenie domenach używały tych samych [operacje ceny](https://azure.microsoft.com/pricing/details/event-grid/) używanego przez wszystkie inne funkcje w usłudze Event Grid.

Operacje działać tak samo w domenach zdarzeń, jak w tematy niestandardowe. Każdy transferu danych przychodzących zdarzenia do domeny zdarzeń jest operacją i kolejnymi próbami dostarczenia zdarzeń jest operacją.

## <a name="next-steps"></a>Kolejne kroki

* Informacje na temat konfigurowania domen zdarzeń, zobacz temat Tworzenie tematów, Tworzenie subskrypcji zdarzeń i publikowania zdarzeń, [Zarządzanie domenami zdarzeń](./how-to-event-domains.md).