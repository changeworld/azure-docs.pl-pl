---
title: Azure filtry tematu usługi Service Bus | Dokumentacja firmy Microsoft
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
ms.author: sethm
ms.openlocfilehash: 305c017bd49f233c10479e2c33ec8db72cae3aa7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28198872"
---
# <a name="topic-filters-and-actions"></a>Filtry tematu i akcji

Subskrybenci można zdefiniować wiadomości, które mają otrzymywać tematu. Komunikaty te są określone w formie co najmniej jednej reguły o nazwie subskrypcji. Każda reguła zawiera warunek, który wybiera określone wiadomości i akcji, która oznacza wybranego komunikatu. Dla każdego pasującego warunku reguły subskrypcji tworzy kopię wiadomości, która może być inaczej adnotacji dla każdej reguły dopasowywania.

Każda subskrypcja nowo utworzony temat ma początkowej domyślna reguła subskrypcji. Jeśli nie zostanie jawnie określony warunek filtru dla reguły, zastosowanego filtru jest **true** filtr, który włącza wszystkie komunikaty, należy wybrać w subskrypcji. Domyślna reguła nie ma adnotacji skojarzonych działań.

Usługa Service Bus obsługuje trzy warunki filtrowania:

-   *Filtrów logicznych* - **TrueFilter** i **FalseFilter** albo spowodować, że wszystkie komunikaty o nadchodzących (**true**) lub Brak nadchodzących wiadomości (**false**) należy wybrać subskrypcję.

-   *Filtry SQL* — **SqlFilter** zawiera wyrażenie warunkowe przypominającego SQL, które jest oceniane w brokerze przed nadchodzących wiadomości właściwości zdefiniowane przez użytkownika i właściwości systemu. Wszystkie właściwości systemu musi być poprzedzona znakiem `sys.` w wyrażeniu warunkowym. [Podzbiór języka SQL dla warunków filtrowania](service-bus-messaging-sql-filter.md) testy istnienia właściwości (EXISTS), jak również-wartości null (IS NULL), nie/i/lub, relacyjne operatory logiczne, prosty Arytmetyka numeryczna i wzorzec zwykły tekst Dopasowywanie podobne.

-   *Filtry korelacji* — **CorrelationFilter** zawiera zestaw warunków, które są dopasowywane do przynajmniej jednej z właściwości wiadomości nadchodzących użytkownika i systemu. Zazwyczaj jest używane do dopasowywania **CorrelationId** właściwości, ale aplikacji można także wybrać opcję dopasowywania **ContentType**, **etykiety**,  **Identyfikator komunikatu**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **do**, a wszelkie zdefiniowane przez użytkownika właściwości. Dopasowanie istnieje, gdy komunikat nadchodzących wartość właściwości jest równa wartości określonej w filtrze korelacji. Wyrażenia parametrów porównanie jest rozróżniana wielkość liter. Podczas określania wielu właściwości dopasowania, filtr łączy je jako warunek i logiczne, co oznacza filtru do dopasowania, wszystkie warunki muszą być zgodne.

Wszystkie filtry, które należy ocenić właściwości wiadomości. Filtry nie można obliczyć treść komunikatu.

Reguły filtrowania złożonych wymagają możliwości przetwarzania. W szczególności stosowania reguły filtrowania SQL powoduje niższe ogólną przepustowość wiadomości na poziomie subskrypcji, tematu i przestrzeni nazw. Jeśli to możliwe, aplikacje należy wybrać filtry korelacji ponad filtrów przypominającego SQL, ponieważ są bardziej wydajne przetwarzania i dlatego mają mniej wpływ na przepustowość.

## <a name="actions"></a>Akcje

Z warunkami filtru SQL, a tylko z tymi można określić akcję, która może dodawać adnotacje do wiadomości przez dodanie, usunięcie lub wymiana, właściwości i ich wartości. Akcja [korzysta z wyrażenia przypominającego SQL](service-bus-messaging-sql-filter.md) który luźno leans na składni instrukcji SQL UPDATE. Akcja jest wykonywana na komunikat po ma dopasowane i przed wybrano komunikat na temat. Zmiany właściwości wiadomości są prywatne komunikat kopiowane do subskrypcji.

## <a name="usage-patterns"></a>Wzorce użycia

Najprostszy scenariusz użycia dla tematu jest, że każdej subskrypcji pobiera kopię każdy komunikat wysłany do tematu, dzięki czemu wzorzec emisji.

Filtry i akcje należy włączyć dwie grupy dalsze wzorców: partycjonowania i routing.

Filtry partycjonowania używa do dystrybucji wiadomości między kilka istniejące subskrypcje tematu w sposób przewidywalną i wykluczają się wzajemnie. Wzorzec partycjonowania jest używany podczas skalowania systemu do obsługi wielu kontekstów w przedziałach funkcjonalnie identyczne, czy każdy przechowywania podzbiór danych ogólną; na przykład informacje o profilu klienta. Z partycjonowania, wydawca przesyła komunikat na temat bez znajomości partycjonowania modelu. Wiadomość jest następnie przeniesiona do poprawną subskrypcję, z którego mogą następnie być pobierane przez program obsługi komunikatów w partycji.

Routing używa filtrów do rozpowszechniają wiadomości subskrypcje tematu w sposób przewidywalne, ale nie zawsze wyłącznego. W połączeniu z [automatycznego przekazywania](service-bus-auto-forwarding.md) funkcji tematu może używać filtrów do utworzenia złożonego routingu wykresach w przestrzeni nazw usługi Service Bus dla dystrybucji komunikatów w obrębie regionu platformy Azure. Funkcji Azure lub usługi Azure Logic Apps, działając jako mostka między obszarami nazw usługi Azure Service Bus można utworzyć złożonej topologii globalnych z integracją bezpośrednich do aplikacji LOB.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Składnia elementu SQLFilter](service-bus-messaging-sql-filter.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)