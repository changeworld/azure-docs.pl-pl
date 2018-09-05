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
ms.date: 01/26/2018
ms.author: spelluru
ms.openlocfilehash: a1616150ebf696654bc0ca9a79d39c3877c363d9
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699390"
---
# <a name="topic-filters-and-actions"></a>Filtry tematów i akcje

Subskrybenci mogą definiować wiadomości, które chcą otrzymywać z tematu. Te komunikaty są określane w postaci co najmniej jedną regułę o nazwie subskrypcji. Każda reguła zawiera warunek, który wybiera określone wiadomości i akcji, która oznacza stosowanym wybranego komunikatu. Dla każdego pasującego warunku reguły subskrypcji tworzy kopię wiadomości, która może być inaczej adnotację dla każdej reguły dopasowania.

Każda subskrypcja nowo utworzony temat ma regułę początkowej domyślnej subskrypcji. Jeśli nie określisz warunek filtru dla tej reguły, jest zastosowany filtr **true** filtr, który włącza wszystkie komunikaty, należy wybrać do subskrypcji. Domyślna reguła ma żadnej akcji skojarzonych adnotacji.

Usługa Service Bus obsługuje trzy warunki filtru:

-   *Filtry logiczne* — **TrueFilter** i **FalseFilter** albo spowodować, że wszystkie komunikaty o nadchodzących (**true**) lub Brak nadchodzących wiadomości (**false**) należy wybrać dla subskrypcji.

-   *Filtry SQL* — **SqlFilter** przechowuje podobnego do SQL wyrażenia warunkowego, które jest obliczane w brokerze przed nadchodzących wiadomości właściwości zdefiniowane przez użytkownika i właściwości systemu. Wszystkie właściwości systemu musi być poprzedzona znakiem `sys.` w wyrażeniu warunkowym. [Podzbioru języka SQL dla warunków filtrowania](service-bus-messaging-sql-filter.md) testy istnienia właściwości (EXISTS), a także — wartości null (IS NULL), operatory logiczne NOT/i/lub, relacyjne, proste Arytmetyka numeryczna i wzorzec zwykłego tekstu dopasowanie za pomocą NOTACJI.

-   *Filtry korelacji* — **CorrelationFilter** zawiera zestaw warunków, które są dopasowywane do co najmniej jeden komunikat nadchodzących właściwości użytkownika i systemu. Zazwyczaj jest używane do dopasowywania **CorrelationId** właściwości, ale aplikacja może także dopasować **ContentType**, **etykiety**,  **Identyfikator komunikatu**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **do**i wszelkie zdefiniowane przez użytkownika właściwości. Istnieje dopasowanie, gdy komunikat nadchodzących wartość właściwości jest równa wartości określonej w filtr korelacji. Dla wyrażenia ciągu porównania jest uwzględniana wielkość liter. Podczas określania wielu właściwości dopasowanie, filtr łączy je jako warunku logicznego i, co oznacza dla filtru dopasować, wszystkie warunki muszą być zgodne.

Wszystkie filtry oceny właściwości komunikatu. Filtry nie można obliczyć treść komunikatu.

Reguły filtrowania złożonych wymagają możliwości przetwarzania. W szczególności użycie reguły filtrowania SQL powoduje niższe ogólną przepływność komunikatów na poziomie subskrypcji, tematu i przestrzeni nazw. Zawsze, gdy jest to możliwe, aplikacje należy wybrać filtry korelacji ponad filtrów podobnego do SQL, ponieważ są znacznie bardziej efektywne, przetwarzania i w związku z tym mają mniej wpływ na przepustowość.

## <a name="actions"></a>Akcje

Za pomocą warunków filtrowania SQL, a tylko osobom można zdefiniować akcję, która może dodawać adnotacje komunikat poprzez dodawanie, usuwanie lub zastępowanie właściwości i ich wartości. Akcja [korzysta z wyrażenia podobnego do SQL](service-bus-messaging-sql-filter.md) , luźno leans składni instrukcji SQL UPDATE. Akcja odbywa się w komunikacie po jego dopasowany i przed wybrano komunikat do tematu. Zmiany właściwości wiadomości są prywatne komunikat skopiowane do subskrypcji.

## <a name="usage-patterns"></a>Wzorce użycia

Najprostszy scenariusz użycia dla tematu jest, że każda subskrypcja otrzymuje kopię każdy komunikat wysyłany do tematu, który umożliwia emisji wzorzec.

Filtry i akcje umożliwiają dwie dodatkowe grupy wzorców: partycjonowania i routing.

Partycjonowanie używa filtry, aby dystrybuować komunikaty do wielu istniejących subskrypcji tematu w sposób przewidywalny i wzajemnie się wykluczają. Wzór partycjonowania jest używany, gdy system jest skalowana w poziomie do obsługi wielu różnych kontekstach w funkcjonalnie identyczny przedziały, że każdy przechowywania podzbiór danych ogólny; na przykład informacje o profilu klienta. Z podziałem na partycje, wydawca przesyła wiadomość do tematu bez konieczności znajomości partycjonowania modelu. Komunikat jest następnie przenoszony do poprawną subskrypcję, z którego mogą następnie być pobierane przez program obsługi komunikatów w partycji.

Routing wykorzystuje filtry, aby dystrybuować komunikaty do różnych subskrypcji tematu w sposób przewidywalny, ale nie zawsze wyłączności. W połączeniu z [automatycznego przekazywania](service-bus-auto-forwarding.md) funkcji tematu filtry umożliwiają tworzenie, złożonym routingiem wykresów w przestrzeni nazw usługi Service Bus dla dystrybucji komunikatów w regionie platformy Azure. Za pomocą usługi Azure Functions lub Azure Logic Apps, działając jako Most między przestrzeni nazw usługi Azure Service Bus można utworzyć złożonych topologii globalnego dzięki bezpośredniej integracji aplikacji biznesowych.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Składnia elementu SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)