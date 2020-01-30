---
title: Filtry tematu Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak subskrybenci mogą definiować komunikaty, które chcą otrzymywać z tematu przez określenie filtrów.
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
ms.date: 01/27/2020
ms.author: spelluru
ms.openlocfilehash: b8ffbb16763bfe6485ebf2ab770f4537ddbc8569
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774498"
---
# <a name="topic-filters-and-actions"></a>Filtry tematów i akcje

Subskrybenci mogą zdefiniować, które komunikaty chcą odbierać z tematu. Te komunikaty są określone w postaci jednej lub więcej nazwanych reguł subskrypcji. Każda reguła składa się z warunku, który wybiera określone komunikaty i akcję, która oznacza wybrany komunikat. Dla każdego pasującego warunku reguły subskrypcja tworzy kopię komunikatu, która może być inaczej adnotowana dla każdej pasującej reguły.

Każda nowo utworzona subskrypcja tematu ma wstępną regułę domyślnej subskrypcji. Jeśli nie określisz jawnie warunku filtru dla reguły, stosowany filtr jest filtrem **true** , który umożliwia wybranie wszystkich komunikatów do subskrypcji. Reguła domyślna nie ma skojarzonej akcji adnotacji.

Service Bus obsługuje trzy warunki filtrowania:

-   *Filtry logiczne* — **TrueFilter** i **FalseFilter** powodują, że wszystkie przychodzące komunikaty (**true**) lub żaden z przychodzących komunikatów (**false**) do wybrania dla subskrypcji.

-   *Filtry SQL* — element **sqlfilter** zawiera wyrażenie warunkowe podobne do programu SQL, które jest oceniane w brokerze względem przychodzących komunikatów i właściwości systemu, które zostały zdefiniowane przez użytkownika. Wszystkie właściwości systemu muszą być poprzedzone `sys.` w wyrażeniu warunkowym. [Podzbiór języka SQL w przypadku testów warunków filtru](service-bus-messaging-sql-filter.md) dla istnienia właściwości (`EXISTS`), a także dla wartości null (`IS NULL`), logicznego nie/i/lub, operatorów relacyjnych, prostych arytmetycznych obliczeń i prostych wzorców tekstu pasujących do `LIKE`.

-   *Filtry korelacji* — **CorrelationFilter** przechowuje zestaw warunków, które są dopasowane do co najmniej jednej właściwości użytkownika i systemu przychodzącego komunikatu. Typowym zastosowaniem jest dopasowanie do właściwości **Identyfikator korelacji** , ale aplikacja może również wybrać dopasowanie do **elementów ContentType**, **Label**, **MessageID**, **ReplyTo**, **ReplyToSessionId**, **SessionID**i **do**oraz wszelkich właściwości zdefiniowanych przez użytkownika. Dopasowanie istnieje, gdy wartość przychodzącego komunikatu dla właściwości jest równa wartości określonej w filtrze korelacji. W przypadku wyrażeń ciągów w porównaniu jest rozróżniana wielkość liter. Podczas określania wielu właściwości dopasowania filtr łączy je jako logiczne i warunkowe, co oznacza, że filtr jest zgodny, wszystkie warunki muszą być zgodne.

Wszystkie filtry obliczają właściwości wiadomości. Filtry nie mogą oszacować treści komunikatu.

Złożone reguły filtru wymagają pojemności przetwarzania. W szczególności użycie reguł filtru SQL powoduje obniżenie ogólnej przepływności komunikatów na poziomie subskrypcji, tematu i przestrzeni nazw. Zawsze, gdy jest to możliwe, aplikacje powinny wybierać filtry korelacji dla filtrów przypominających SQL, ponieważ są one znacznie bardziej wydajne w przetwarzaniu i w związku z tym mają mniej wpływu na przepływność.

## <a name="actions"></a>Akcje

Warunki filtru SQL umożliwiają zdefiniowanie akcji, która może dodawać adnotacje do wiadomości przez dodawanie, usuwanie lub zastępowanie właściwości i ich wartości. Akcja [używa wyrażenia podobnej do języka SQL](service-bus-messaging-sql-filter.md) , które luźno pochylenie w SKŁADNI instrukcji SQL Update. Akcja jest wykonywana na komunikacie po dopasowaniu i przed wybraniem komunikatu do subskrypcji. Zmiany właściwości wiadomości są prywatne dla wiadomości skopiowanej do subskrypcji.

## <a name="usage-patterns"></a>Wzorce użycia

Najprostszy scenariusz użycia tematu polega na tym, że każda subskrypcja pobiera kopię każdej wiadomości wysyłanej do tematu, co umożliwia użycie wzorca emisji.

Filtry i akcje umożliwiają włączenie dwóch grup wzorców: partycjonowanie i Routing.

Partycjonowanie używa filtrów do dystrybuowania komunikatów w kilku istniejących subskrypcjach tematu w sposób przewidywalny i wzajemnie wykluczający. Wzorzec partycjonowania jest używany, gdy system jest skalowany w celu obsługi wielu różnych kontekstów w funkcjonalnie identyczne przedziały, które każdy z nich utrzymuje podzestaw ogólnych danych; na przykład informacje o profilu klienta. W przypadku partycjonowania Wydawca przesyła komunikat do tematu bez konieczności znajomości modelu partycjonowania. Wiadomość zostanie przeniesiona do odpowiedniej subskrypcji, z której będzie można ją pobrać przy użyciu obsługi komunikatów partycji.

Funkcja routingu używa filtrów do dystrybuowania komunikatów między subskrypcjami tematów w przewidywalny sposób, ale niekoniecznie na wyłączność. W połączeniu z funkcją [autoprzekazywania](service-bus-auto-forwarding.md) filtry tematu mogą służyć do tworzenia złożonych wykresów routingu w ramach przestrzeni nazw Service Bus na potrzeby dystrybucji komunikatów w regionie świadczenia usługi Azure. W przypadku Azure Functions lub Azure Logic Apps działającego jako Most między przestrzeniami nazw Azure Service Bus można tworzyć złożone topologie globalne z bezpośrednią integracją z aplikacjami biznesowymi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Składnia elementu SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)