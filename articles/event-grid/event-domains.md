---
title: Domeny zdarzeń w siatce zdarzeń platformy Azure
description: W tym artykule opisano sposób używania domen zdarzeń do zarządzania przepływem zdarzeń niestandardowych do różnych organizacji biznesowych, klientów lub aplikacji.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898866"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Opis domen zdarzeń do zarządzania tematami siatki zdarzeń

W tym artykule opisano sposób używania domen zdarzeń do zarządzania przepływem zdarzeń niestandardowych do różnych organizacji biznesowych, klientów lub aplikacji. Domeny zdarzeń za pomocą:

* Zarządzaj architekturami wielodostępnych zdarzeń na dużą skalę.
* Zarządzaj autoryzacją i uwierzytelnianiem.
* Podziel swoje tematy bez zarządzania każdym z nich indywidualnie.
* Unikaj indywidualnego publikowania do każdego z punktów końcowych tematu.

## <a name="event-domain-overview"></a>Omówienie domeny zdarzeń

Domena zdarzeń jest narzędziem do zarządzania dużą liczbą tematów usługi Event Grid związanych z tą samą aplikacją. Można to potraktować jako meta-temat, który może mieć tysiące pojedynczych tematów.

Domeny zdarzeń udostępniają ci tę samą architekturę, która jest używana przez usługi platformy Azure (takie jak Magazyn i Usługa IoT Hub) do publikowania ich zdarzeń. Pozwalają one na publikowanie wydarzeń na tysiące tematów. Domeny zapewniają również autoryzację i kontrolę uwierzytelniania nad każdym tematem, dzięki czemu można podzielić dzierżawców.

### <a name="example-use-case"></a>Przykładowy przypadek użycia

Domeny zdarzeń są najłatwiej wyjaśnić za pomocą przykładu. Załóżmy, że prowadzisz contoso Construction Machinery, gdzie produkujesz ciągniki, sprzęt do kopania i inne ciężkie maszyny. W ramach prowadzenia działalności firmy, można wypychać w czasie rzeczywistym do klientów o konserwacji sprzętu, kondycji systemu i aktualizacji umów. Wszystkie te informacje trafiają do różnych punktów końcowych, w tym aplikacji, punktów końcowych klientów i innej infrastruktury skonfigurowanej przez klientów.

Domeny zdarzeń umożliwiają modelowanie contoso construction machinery jako pojedynczej jednostki eventingowej. Każdy z twoich klientów jest reprezentowany jako temat w domenie. Uwierzytelnianie i autoryzacja są obsługiwane przy użyciu usługi Azure Active Directory. Każdy z Twoich klientów może subskrybować swój temat i otrzymywać swoje wydarzenia. Dostęp zarządzany za pośrednictwem domeny zdarzeń zapewnia, że mogą uzyskać dostęp tylko do swojego tematu.

Zapewnia również jeden punkt końcowy, który można opublikować wszystkie zdarzenia klienta. Usługa Event Grid zadba o upewnienie się, że każdy temat jest świadomy tylko zdarzeń o zakresie do jego dzierżawy.

![Przykład konstrukcji Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Zarządzanie dostępem

W domenie otrzymasz precyzyjną autoryzację i kontrolę uwierzytelniania nad każdym tematem za pośrednictwem kontroli dostępu opartej na rolach platformy Azure (RBAC). Za pomocą tych ról można ograniczyć każdej dzierżawy w aplikacji tylko tematy, które chcesz udzielić im dostępu do.

RBAC w domenach zdarzeń działa w taki sam sposób, jak [zarządzana kontrola dostępu](security-authorization.md) działa w pozostałej części usługi Event Grid i platformy Azure. Użyj rbac do tworzenia i wymuszania niestandardowych definicji ról w domenach zdarzeń.

### <a name="built-in-roles"></a>Wbudowane role

Usługa Event Grid ma dwie wbudowane definicje ról ułatwiające pracę z domenami zdarzeń. Te role to **EventGrid EventSubscription Contributor (Preview)** i **EventGrid EventSubscription Reader (Wersja zapoznawcza).** Te role można przypisać użytkownikom, którzy muszą subskrybować tematy w domenie zdarzeń. Możesz zakres przypisania roli tylko do tematu, który użytkownicy muszą subskrybować.

Aby uzyskać informacje o tych rolach, zobacz [Wbudowane role dla siatki zdarzeń](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Subskrybowanie tematów

Subskrybowanie zdarzeń w temacie w domenie zdarzeń jest takie samo jak [tworzenie subskrypcji zdarzeń w temacie niestandardowym](./custom-event-quickstart.md) lub subskrybowanie zdarzenia z usługi platformy Azure.

### <a name="domain-scope-subscriptions"></a>Subskrypcje zakresu domeny

Domeny zdarzeń umożliwiają również subskrypcje zakresu domeny. Subskrypcja zdarzeń w domenie zdarzeń otrzyma wszystkie zdarzenia wysyłane do domeny, niezależnie od tematu, do który są wysyłane zdarzenia. Subskrypcje zakresu domeny mogą być przydatne do celów zarządzania i inspekcji.

## <a name="publishing-to-an-event-domain"></a>Publikowanie w domenie zdarzeń

Podczas tworzenia domeny zdarzeń, otrzymasz punkt końcowy publikowania podobne do jeśli utworzono temat w uszami zdarzeń. 

Aby opublikować zdarzenia w dowolnym temacie w domenie zdarzeń, wypchnij zdarzenia do punktu końcowego domeny w [taki sam sposób, jak w przypadku tematu niestandardowego](./post-to-custom-topic.md). Jedyną różnicą jest to, że należy określić temat, który ma być dostarczany do zdarzenia.

Na przykład publikowanie następującej tablicy `"id": "1111"` zdarzeń `foo` spowoduje wysłanie `"id": "2222"` zdarzenia do `bar`tematu, podczas gdy zdarzenie z zostanie wysłane do tematu:

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

Domeny zdarzeń obsługują publikowanie w tematach. Zamiast publikowania zdarzeń do każdego tematu, który zarządzasz indywidualnie, można opublikować wszystkie zdarzenia w punkcie końcowym domeny. Usługa Event Grid zapewnia, że każde zdarzenie jest wysyłane do prawidłowego tematu.

## <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia
Oto limity i przydziały związane z domenami zdarzeń:

- 100 000 tematów na domenę wydarzenia 
- 100 domen zdarzeń na subskrypcję platformy Azure 
- 500 subskrypcji zdarzeń na temat w domenie zdarzeń
- 50 subskrypcji zakresu domeny 
- 5000 zdarzeń na sekundę (do domeny)

Jeśli te limity ci nie odpowiadają, skontaktuj się z zespołem produktu, [askgrid@microsoft.com](mailto:askgrid@microsoft.com)otwierając bilet pomocy technicznej lub wysyłając wiadomość e-mail na adres . 

## <a name="pricing"></a>Cennik
Domeny zdarzeń używają tych [samych cen operacji,](https://azure.microsoft.com/pricing/details/event-grid/) które używają wszystkie inne funkcje w uszami zdarzeń.

Operacje działają tak samo w domenach zdarzeń, jak w tematach niestandardowych. Każdy przychodzących zdarzenia do domeny zdarzenia jest operacją, a każda próba dostarczenia dla zdarzenia jest operacją.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o konfigurowaniu domen zdarzeń, tworzeniu tematów, tworzeniu subskrypcji zdarzeń i publikowaniu zdarzeń, zobacz [Zarządzanie domenami zdarzeń](./how-to-event-domains.md).
