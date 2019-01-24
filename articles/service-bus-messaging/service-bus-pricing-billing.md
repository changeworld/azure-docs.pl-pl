---
title: Usługa Service Bus, cennik i rozliczenia | Dokumentacja firmy Microsoft
description: Przegląd cennika struktury usługi Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 5b9aae979a25a1f175b3d5a5e24960d6f392b9b4
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852936"
---
# <a name="service-bus-pricing-and-billing"></a>Usługa Service Bus, cen i rozliczeń

Usługa Azure Service Bus jest oferowana w standardzie i [Premium](service-bus-premium-messaging.md) warstw. Możesz wybrać warstwę usług dla każdej przestrzeni nazw usługi Service Bus, którą tworzysz, a następnie zaznacz to pole wyboru warstwy stosowany do wszystkich obiektów utworzonych w tej przestrzeni nazw.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat obecnego modelu cen usługi Service Bus, zobacz [stronę z cennikiem usługi Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/)i [— często zadawane pytania do usługi Service Bus](service-bus-faq.md#pricing).
>
>

Usługa Service Bus używa następujących liczników 2 w przypadku kolejek i tematów/subskrypcji:

1. **Operacje obsługi komunikatów**: Definiowane jako wywołania interfejsu API względem punktów końcowych usługi kolejki lub tematu/subskrypcji. Ten licznik zastępuje wiadomości wysyłane lub odbierane jako podstawowa jednostka płatne użycie w przypadku kolejek i tematów/subskrypcji.
2. **Połączenia obsługiwane przez brokera**: Określone szczytowa liczba połączeń trwałych Otwórz kolejek, tematów lub subskrypcji w okresie danym jednogodzinnym pobierania próbek. Ten licznik ma zastosowanie tylko w warstwie standardowa, w którym można otworzyć dodatkowe połączenia (wcześniej, połączenia zostały ograniczone do 100 na kolejki/tematu/subskrypcji) połączenia nominalna za opłatą.

**Standardowa** warstwy wprowadza stopniowanych ceny dla operacji wykonywanych przy użyciu kolejek i tematów/subskrypcji, co opartego na ilości rabat wynoszący maksymalnie 80% na najwyższych poziomach użycia. Dostępna jest również w warstwie standardowa podstawowa opłata za 10 USD miesięcznie, co umożliwia wykonywanie maksymalnie 12,5 mln operacji miesięcznie bez ponoszenia dodatkowych kosztów.

**Premium** warstwa zapewnia izolację zasobów w warstwie Procesora i pamięci, tak aby był uruchamiany obciążenia poszczególnych klientów w izolacji. Ten kontener zasobów jest nazywany *jednostką obsługi komunikatów*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Możesz kupić 1, 2 lub 4 jednostki obsługi komunikatów dla każdej przestrzeni nazw usługi Service Bus w warstwie Premium. Pojedyncze obciążenie lub jednostka może obejmować wiele jednostek obsługi komunikatów, a ich liczbę można dowolnie zmieniać, chociaż opłaty są naliczane w cyklu 24-godzinnym lub codziennie. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus. Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania.

> [!NOTE]
> Tematy i subskrypcje są dostępne tylko w cenowej standardowa lub Premium warstw; Warstwa podstawowa obsługuje tylko kolejki.

Opłata podstawowa w warstwie standardowa jest naliczana tylko raz na miesiąc dla każdej subskrypcji platformy Azure. Oznacza to, że po utworzeniu przestrzeni nazw usługi Service Bus warstwa jeden standardowy, można utworzyć dowolną liczbę dodatkowych standardowych przestrzeni nazw dowolną w ramach tej samej subskrypcji platformy Azure bez naliczania dodatkowych opłat podstawowych.

[Cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabeli podsumowano różnice funkcjonalne między warstwami standardowa i Premium.

## <a name="messaging-operations"></a>Operacje obsługi komunikatów

Kolejki i tematy/subskrypcje są naliczane opłaty za "Operacja", nie dla konkretnego komunikatu. Działanie odnosi się do każde wywołanie interfejsu API skierowanego punkt końcowy usługi kolejki lub tematu/subskrypcji. Dotyczy to również operacji zarządzania, wysyłania/odbierania i obsługi stanu sesji.

| Typ operacji | Opis |
| --- | --- |
| Zarządzanie |Tworzenia, odczytu, aktualizacji i usuwania (CRUD) kolejek lub tematów/subskrypcji. |
| Obsługa komunikatów |Wysyłanie i odbieranie komunikatów z kolejek lub tematów/subskrypcji. |
| Stan sesji |Pobieranie lub ustawianie stanu sesji na kolejki lub tematu/subskrypcji. |

Koszt szczegółowe informacje, zobacz ceny podane na [cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) strony.

## <a name="brokered-connections"></a>Połączenia obsługiwane przez brokera

*Połączenia obsługiwane przez brokera* pomieścić wzorcach użycia, które obejmują wiele "trwałe połączono" nadawcy/odbiorcy kolejek, tematów lub subskrypcji. Trwałe połączonych nadawcy/odbiorcy są to te, które łączą się przy użyciu protokołu AMQP lub HTTP z niezerowym otrzymywać limit czasu (na przykład HTTP długim sondowaniem). HTTP nadawców i odbiorców, z bezpośrednim przekroczeniem limitu czasu nie generują połączeń obsługiwanych przez brokera.

Przydziały połączenia i inne limity usługi można znaleźć [przydziały usługi Service Bus](service-bus-quotas.md) artykułu. Aby uzyskać więcej informacji na temat połączeń obsługiwanych przez brokera, zobacz [— często zadawane pytania](#faq) sekcję w dalszej części tego artykułu.

Warstwa standardowa usuwa limit połączeń obsługiwanych przez brokera na przestrzeń nazw i zlicza użycia agregacji połączenia obsługiwane przez brokera dla subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [połączeń obsługiwanych przez brokera](https://azure.microsoft.com/pricing/details/service-bus/) tabeli.

> [!NOTE]
> 1000 połączeń obsługiwanych przez brokera są dołączone w warstwie standardowej obsługi komunikatów (w ramach opłaty podstawowej) i mogą być współużytkowane przez wszystkie kolejki, tematy i subskrypcje w obrębie skojarzonej subskrypcji platformy Azure.
>
>

<br />

> [!NOTE]
> Opłata zależy do szczytowej liczby jednoczesnych połączeń i jest naliczana proporcjonalnie w oparciu o godziny przy założeniu 744 godzin na miesiąc.
>
>

### <a name="premium-tier"></a>Warstwa Premium

W warstwie Premium nie są naliczane opłaty za połączenia obsługiwane przez brokera.

## <a name="faq"></a>Często zadawane pytania

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Co to są połączenia obsługiwane przez brokera i jak rozliczane dla nich?

Połączenie obsługiwane przez brokera jest definiowane jako jedna z następujących sytuacji:

1. Połączenie AMQP od klienta do usługi Service Bus, kolejki lub tematu/subskrypcji.
2. Wywołanie HTTP mające na celu odebranie komunikatu z tematu lub kolejki usługi Service Bus, dla którego wartość limitu czasu odbierania jest większa od zera.

Usługa Service Bus opłaty za szczytową liczbę jednoczesnych połączeń obsługiwanych przez brokera, która przekracza wliczoną w usługę wartość (1000 połączeń w warstwie standardowa). Wartości szczytowe są mierzone godzinowo, proporcjonalnie dzielone przez 744 godziny w miesiącu i dodawane w ciągu miesięcznego okresu rozliczeniowego. Uwzględniona liczba (1000 połączeń obsługiwanych przez brokera na miesiąc) jest stosowana na koniec okresu rozliczeniowego względem sumy proporcjonalnie naliczonych szczytów wyrażonych w godzinach.

Na przykład:

1. Każde z 10 000 urządzeń łączy pojedyncze połączenie AMQP i odbiera polecenia z tematu usługi Service Bus. Urządzenia wysyła zdarzenia telemetryczne do Centrum zdarzeń. Jeśli wszystkie urządzenia łączą się przez 12 godzin dziennie, następujące połączenia opłaty (poza innymi opłatami usługi Service Bus temat): 10 000 połączeń * 12 godzin * 31 dni / 744 = 5000 połączeń obsługiwanych przez brokera. Po wykorzystaniu miesięcznego limitu 1000 połączeń obsługiwanych przez brokera możesz być naliczona opłata za 4000 połączeń obsługiwanych przez brokera, zgodnie ze stawką 0,03 USD za połączenia obsługiwane przez brokera, w sumie $120.
2. 10 000 urządzeń odbiera komunikaty z kolejki usługi Service Bus za pośrednictwem protokołu HTTP z limitem czasu różna od zera. Jeśli wszystkie urządzenia łączą się przez 12 godzin dziennie, zobaczysz następujące opłaty (poza innymi opłatami usługi Service Bus): 10 000 połączeń odebranych HTTP * 12 godzin dziennie * 31 dni / 744 godzin = 5000 połączeń obsługiwanych przez brokera.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Czy opłaty za połączenia obsługiwane przez brokera mają zastosowanie w przypadku kolejek i tematów/subskrypcji?

Tak. Nie ma opłat za połączenia związane z przesyłaniem zdarzeń przez protokół HTTP niezależnie od liczby systemów lub urządzeń wysyłających. Odbieranie zdarzeń za pośrednictwem protokołu HTTP przy użyciu limitu czasu większego niż zero, czasami nazywane "długim sondowaniem", generuje opłaty za połączenia obsługiwane przez brokera. Połączenia AMQP generują opłaty za połączenia obsługiwane przez brokera bez względu na to, czy połączenia są używane do wysyłania czy odbierania. Pierwsze 1000 połączeń obsługiwanych przez brokera w wszystkich standardowych obszarach nazw subskrypcji platformy Azure są uwzględnione bez opłat dodatkowych (poza opłatą podstawową). Ponieważ te dozwolone liczby połączeń są wystarczające w wielu scenariuszach obsługi komunikatów do usługi, opłaty za połączenia obsługiwane przez brokera stają się tylko istotne, jeśli planujesz używać długiego sondowania AMQP lub HTTP z dużą liczbą klientów. na przykład w celu uzyskania bardziej efektywnego przesyłania strumieniowego zdarzeń lub umożliwienia dwukierunkowej komunikacji z wielu urządzeń albo wystąpień aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe informacje o cenach usługi Service Bus, zobacz [stronę cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Zobacz [— często zadawane pytania do usługi Service Bus](service-bus-faq.md#pricing) na niektóre często zadawane pytania dotyczące usługi Service bus, cen i rozliczeń.

[Azure portal]: https://portal.azure.com
