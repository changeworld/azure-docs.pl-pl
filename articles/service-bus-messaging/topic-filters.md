---
title: Filtry tematów w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Filtruj tematów usługi Azure Service Bus
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 41af53dbfbb5c863007a332445a2f184fcbcbf81
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332231"
---
# <a name="topic-filters-and-actions"></a>Filtry tematów i akcje

Subskrybenci mogą zdefiniować, które komunikaty chcą odbierać z tematu. Te komunikaty są określane w postaci co najmniej jedną regułę o nazwie subskrypcji. Każda reguła zawiera warunek, który wybiera określone wiadomości i akcji, która oznacza stosowanym wybranego komunikatu. Dla każdego pasującego warunku reguły subskrypcja tworzy kopię komunikatu, która może być inaczej adnotowana dla każdej pasującej reguły.

Każda subskrypcja nowo utworzony temat ma regułę początkowej domyślnej subskrypcji. Jeśli nie określisz warunek filtru dla tej reguły, jest zastosowany filtr **true** filtr, który włącza wszystkie komunikaty, należy wybrać do subskrypcji. Domyślna reguła ma żadnej akcji skojarzonych adnotacji.

Usługa Service Bus obsługuje trzy warunki filtru:

-   *Filtry logiczne* — **TrueFilter** i **FalseFilter** albo spowodować, że wszystkie komunikaty o nadchodzących (**true**) lub Brak nadchodzących wiadomości (**false**) należy wybrać dla subskrypcji.

-   *Filtry SQL* — **SqlFilter** przechowuje podobnego do SQL wyrażenia warunkowego, które jest obliczane w brokerze przed nadchodzących wiadomości właściwości zdefiniowane przez użytkownika i właściwości systemu. Wszystkie właściwości systemu musi być poprzedzona znakiem `sys.` w wyrażeniu warunkowym. [Podzbioru języka SQL dla warunków filtrowania](service-bus-messaging-sql-filter.md) testy istnienia właściwości (`EXISTS`), jak również, jak w przypadku wartości null (`IS NULL`), operator logiczny NOT/i/lub, operatory relacyjne i proste Arytmetyka numeryczna, i dopasowanie wzorca zwykłego tekstu za pomocą `LIKE`.

-   *Filtry korelacji* — **CorrelationFilter** zawiera zestaw warunków, które są dopasowywane do co najmniej jeden komunikat nadchodzących właściwości użytkownika i systemu. Zazwyczaj jest używane do dopasowywania **CorrelationId** właściwości, ale aplikacja może także dopasować **ContentType**, **etykiety**,  **Identyfikator komunikatu**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **do**i wszelkie zdefiniowane przez użytkownika właściwości. Istnieje dopasowanie, gdy komunikat nadchodzących wartość właściwości jest równa wartości określonej w filtr korelacji. Dla wyrażenia ciągu porównania jest uwzględniana wielkość liter. Podczas określania wielu właściwości dopasowanie, filtr łączy je jako warunku logicznego i, co oznacza dla filtru dopasować, wszystkie warunki muszą być zgodne.

Wszystkie filtry oceny właściwości komunikatu. Filtry nie można obliczyć treść komunikatu.

Reguły filtrowania złożonych wymagają możliwości przetwarzania. W szczególności użycie reguły filtrowania SQL powoduje niższe ogólną przepływność komunikatów na poziomie subskrypcji, tematu i przestrzeni nazw. Zawsze, gdy jest to możliwe, aplikacje należy wybrać filtry korelacji ponad filtrów podobnego do SQL, ponieważ są znacznie bardziej efektywne, przetwarzania i w związku z tym mają mniej wpływ na przepustowość.

## <a name="actions"></a>Akcje

Za pomocą warunków filtru SQL można zdefiniować akcję, która może dodawać adnotacje komunikat poprzez dodawanie, usuwanie lub zastępowanie właściwości i ich wartości. Akcja [korzysta z wyrażenia podobnego do SQL](service-bus-messaging-sql-filter.md) , luźno leans składni instrukcji SQL UPDATE. Akcja odbywa się w komunikacie po jego dopasowany i przed wybrano wiadomość w subskrypcji. Zmiany właściwości wiadomości są prywatne komunikat skopiowane do subskrypcji.

## <a name="usage-patterns"></a>Wzorce użycia

Najprostszy scenariusz użycia dla tematu jest, że każda subskrypcja otrzymuje kopię każdy komunikat wysyłany do tematu, który umożliwia emisji wzorzec.

Filtry i akcje umożliwiają dwie dodatkowe grupy wzorców: partycjonowania i routing.

Partycjonowanie używa filtry, aby dystrybuować komunikaty do wielu istniejących subskrypcji tematu w sposób przewidywalny i wzajemnie się wykluczają. Wzór partycjonowania jest używany, gdy system jest skalowana w poziomie do obsługi wielu różnych kontekstach w funkcjonalnie identyczny przedziały, że każdy przechowywania podzbiór danych ogólny; na przykład informacje o profilu klienta. Z podziałem na partycje, wydawca przesyła wiadomość do tematu bez konieczności znajomości partycjonowania modelu. Komunikat jest następnie przenoszony do poprawną subskrypcję, z którego mogą następnie być pobierane przez program obsługi komunikatów w partycji.

Routing wykorzystuje filtry, aby dystrybuować komunikaty do różnych subskrypcji tematu w sposób przewidywalny, ale nie zawsze wyłączności. W połączeniu z [automatycznego przekazywania](service-bus-auto-forwarding.md) funkcji tematu filtry umożliwiają tworzenie, złożonym routingiem wykresów w przestrzeni nazw usługi Service Bus dla dystrybucji komunikatów w regionie platformy Azure. Usługi Azure Functions i Azure Logic Apps, działając jako Most między przestrzeni nazw usługi Azure Service Bus umożliwia tworzenie złożonych topologii globalnych za pomocą bezpośredniej integracji w aplikacjach line-of-business.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Składnia elementu SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)