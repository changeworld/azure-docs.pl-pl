---
title: Pulpit nawigacyjny zamówień centrum partnerów w analizie w portalu Marketplace komercyjnym
description: Dowiedz się, jak uzyskać dostęp do raportów analitycznych dotyczących zamówień oferowanych w portalu marketplace w formacie graficznym i do pobrania.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 148b80a6843168b11e4feb3558f6bf08eaeb3ace
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261265"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny zamówień w analizie rynku komercyjnego

Ten artykuł zawiera informacje na **pulpicie nawigacyjnym zamówień** w Centrum partnerów. Ten pulpit nawigacyjny wyświetla informacje o zamówieniach w formacie graficznym i do pobrania.

Aby uzyskać dostęp do **pulpitu nawigacyjnego Zamówienia** w narzędziach analitycznych Centrum **[partnerów,](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** otwórz pulpit nawigacyjny Analizy w obszarze Rynek komercyjny.

>[!NOTE]
> Szczegółowe definicje terminologii analitycznej można znaleźć w części [Często zadawane pytania i terminologia dotyczące analizy rynku komercyjnego.](./faq-terminology.md)

## <a name="orders-dashboard"></a>Pulpit nawigacyjny Zamówienia

**Pulpit nawigacyjny Zamówień** w menu **Analizuj** wyświetla bieżące zamówienia dla wszystkich ofert SaaS. Można wyświetlić graficzne reprezentacje następujących elementów:

- [Podsumowanie zamówienia](#order-summary)
- [Zamówienia według geografii](#orders-by-geography)
- [Zamówienia według ofert](#orders-by-offers)
- [Tendencja zamówień na witrynę w porównaniu z zakładem](#orders-trend-per-site-versus-per-seat)
- [Zamówienia przez jednostki SKU](#orders-by-skus)
- [Tendencja zamówień i miejsc](#orders-and-seats-trend)
- [Tabela szczegółów zamówienia](#order-details-table)

> [!NOTE]
> Istnieją różnice między wyświetlaniem raportów analitycznych w portalu Cloud Partner Portal (CPP) a nowym programem dla rynków komercyjnych w Centrum partnerów. Jednym z konkretnych sposobów jest to, że **Insights sprzedawcy** w CPP ma **zamówienie & użycie** karty, która wyświetla dane dla ofert opartych na użyciu i ofert nieopartych na użyciu. W Centrum **partnerów** strona Zamówienia ma osobną kartę dla ofert SaaS.

## <a name="order-dashboard-details"></a>Szczegóły pulpitu nawigacyjnego zamówienia

W tej sekcji opisano raporty analityczne bardziej szczegółowo.

### <a name="order-summary"></a>Podsumowanie zamówienia

W sekcji Podsumowanie zamówienia wyświetlana jest liczba wszystkich zakupionych zamówień (z wyłączeniem anulowanych zamówień), anulowanych zamówień i miejsc.

Wartość procentowa obok pozycji Łączna liczba zamówień reprezentuje kwotę wzrostu wybranego zakresu dat.

![Podsumowanie zamówienia analizy centrum partnerów](./media/order-summary.png)

- Zielony trójkąt skierowany w górę wskazuje na pozytywny trend wzrostu.
- Czerwony trójkąt skierowany w dół wskazuje na ujemny trend wzrostu w stosunku do poprzedniego miesiąca.
- Trendy wzrostu są reprezentowane przez mikrobargrafy. Wartość każdego miesiąca można wyświetlić, najeżdżając kursorem na kolumny na wykresie.
- Anulowane zamówienia to liczba zamówień, które zostały wcześniej zakupione, a następnie anulowane w wybranym zakresie dat.
- Liczba miejsc jest liczbą miejsc utworzonych w wybranym zakresie dat.

### <a name="orders-by-geography"></a>Zamówienia według geografii

Mapa cieplna **Zamówienia według lokalizacji** pokazuje liczbę zamówień na mapie świata i pokazuje miejsca mapowane na podstawie kraju klienta. Ta mapa cieplna działa tak samo jak **[klient według mapy cieplnej geografii](./customer-dashboard.md#customer-by-geography)**.

![Centrum partnerów Analizuj zamówienia według lokalizacji geograficznej](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Zamówienia według ofert

**Wykres Zamówień według ofert** organizuje zamówienia (w tym anulowane zamówienia) zgodnie z ich nazwami ofert.

- Najlepsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "Rest All".
- Możesz wybrać konkretne oferty w legendzie, aby wyświetlić tylko te oferty na wykresie.
- Najechanie kursorem na wycinkę na wykresie spowoduje wyświetlenie liczby zamówień i procentu tej oferty w porównaniu z całkowitą liczbą zamówień we wszystkich ofertach.
- Trend **zamówień według ofert** wyświetla trendy wzrostu z miesiąca na miesiąc. Kolumna miesiąc reprezentuje liczbę zamówień według nazwy oferty. Na wykresie liniowym jest wyświetlany trend procentowy wzrostu wykreślony na osi z.
- Suwaka u góry wykresu można użyć, aby przewinąć w prawo i w lewo wzdłuż osi x i skupić się na określonych punktach danych.
- Wykres trendu można wyświetlić, wybierając określony element w legendzie.
- Można również wybrać wyświetlanie trendów i danych dla **anulowanych zamówień**. Wykres będzie funkcjonował w taki sam sposób, jak zamówienia według wykresu **ofert.**

### <a name="orders-trend-per-site-versus-per-seat"></a>Zamówienia Trend na witrynę w porównaniu z miejscem

Wykres pączka **na miejsce w porównaniu do pączka na miejsce** reprezentuje podział na witrynę SaaS i na miejsce zamówienia SaaS zakupione przez klientów (ten wykres obejmuje anulowane zamówienia). Wykres kolumnowy reprezentuje trend dla zamówień SaaS dla witryn i na miejsce SaaS zakupionych przez klientów (ten wykres obejmuje anulowane zamówienia).

### <a name="orders-by-skus"></a>Zamówienia przez jednostki SKU

Wykres **Zamówienia według jednostek SKU** reprezentuje trend zamówień na poziomie Jednostki SKU dla wszystkich ofert (dotyczy to anulowanych zamówień). Wykres pierścieniowy reprezentuje podział pięciu pierwszych zamówień jednostek SKU, a wykres kolumnowy reprezentuje trend zamówień dla pięciu największych jednostek SKU.

### <a name="orders-and-seats-trend"></a>Trend zamówień i miejsc

Wykres **trendu zamówień i miejsc** przedstawia 50 najlepszych ofert z największą liczbą zamówień. Są one wyświetlane na tablicy liderów i są sortowane według najwyższej liczby zamówień i procentu zamówienia.

- **Zamówienia według jednostek SKU**: Wybierz ofertę, aby wyświetlić podział liczby zamówień dla pięciu największych jednostek SKU na wykresie.
- **Miejsca według jednostek SKU**: Miesięczny trend siedzeń dla pięciu największych jednostek SKU. Jeśli wybrana oferta nie jest ofertą na miejsce, nie zobaczysz żadnych danych na tym wykresie warstwowym.

### <a name="canceled-orders-by-offers"></a>Anulowane zamówienia według ofert

Wykres kołowy **Anulowane zamówienia według ofert** organizuje wszystkie anulowane zamówienia zgodnie z ich nazwami ofert. Najlepsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "Rest All". W legendzie można wybrać określone oferty do wyświetlenia na wykresie.

- Najechanie kursorem na wycinkę na wykresie spowoduje wyświetlenie liczby zamówień i procentu wybranej oferty w porównaniu z całkowitą liczbą zamówień we wszystkich ofertach.
- Na wykresie kolumnowym są wyświetlane trendy miesiąc po miesiącu. Kolumny reprezentują liczbę anulowanych zamówień według nazwy oferty. Suwaka u góry wykresu można użyć, aby przewinąć w prawo i w lewo wzdłuż osi x i skupić się na określonych punktach danych. Wykres trendu można wyświetlić, wybierając określony element w legendzie.

### <a name="order-details-table"></a>Tabela szczegółów zamówienia

W tabeli Szczegóły zamówienia jest wyświetlana ponumerowana lista 1000 najwyższych zamówień posortowanych według daty nabycia.

- Każdą kolumnę w siatce można sortować.
- Dane można wyodrębnić do pliku TSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów ponad 1000, eksportowane dane zostaną asynchronicznie umieszczone na stronie pobierania przez następne 30 dni.
- Filtry można zastosować do **tabeli Szczegóły zamówienia,** aby wyświetlić tylko dane, które Cię interesują. Dane mogą być filtrowane według kraju, typu licencji platformy Azure, typu licencji w portalu Marketplace, typu oferty, stanu zamówienia, bezpłatnych śladów, identyfikatora subskrypcji portalu Marketplace, identyfikatora klienta i nazwy firmy.

#### <a name="orders-page-filters"></a>Filtry strony zamówień

Filtry te są stosowane na poziomie strony.

Można wybrać wiele filtrów, aby renderować wykres dla kryteriów, które chcesz wyświetlić, oraz danych, które mają być wyświetlane w siatce/eksporcie **szczegółowe dane zamówienia.** Filtry są stosowane do danych wyodrębnionych dla zakresu danych wybranego w prawym górnym rogu strony zamówień.

- Typy ofert i nazwy ofert są wyświetlane tylko dla ofert, dla których masz zamówienia w wybranym zakresie dat. Nazwy ofert na liście są wyświetlane dla typów ofert wybranych na liście.
- Zastosowane filtry pokazują całkowite metryki w ramach każdego wyboru dla każdego wybranego filtru. Zastosowane filtry nie są wyświetlane po wybraniu zaznaczenia domyślnego.
- Jeśli dla jednej z list rozwijanych wybrano opcję **Wszystko,** wszystkie dane na wybranej stronie zostaną zagregowane. Na przykład: "Wszystkie" w opcji filtru typów oferty oznacza, że wszystkie typy ofert zostały wybrane. Jest to domyślny wybór list rozwijanych. Zastosowane filtry nie będą widoczne po wybraniu opcji **Wszystko.**
- **Wybór wielu wartości:** wszystkie dane na stronie zostaną zagregowane dla wszystkich wyborów dokonanych pod listą rozwijaną. Jeśli dokonano wielu selekcji, zastosowany filtr wyświetli liczbę wszystkich dokonanych wyborów. Zobacz obrazek poniżej w celach informacyjnych.

    ![Centrum partnerów Analizowanie zamówienia z wieloma wartościami zastosowanymi do filtru](./media/filters-applied.png)

- **Wybór pojedynczej wartości**: Jeśli wybrana jest jedna wartość, zastosowany filtr wyświetli liczbę jednego filtru, który został wybrany. Zobacz poniższą ilustrację, aby uzyskać odniesienie.

     ![Centrum partnerów Analizowanie zamówienia z pojedynczą wartością zastosowaną do filtru](./media/filters-applied-single.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przegląd raportów analitycznych dostępnych w komercyjnym rynku Centrum partnerów, zobacz [Analytics dla rynku komercyjnego w Centrum partnerów.](./analytics.md)
- Aby uzyskać wykresy, trendy i wartości danych agregujących, które podsumowują aktywność w portalu marketplace dla Twojej oferty, zobacz [Podsumowanie pulpitu nawigacyjnego w analizie rynku komercyjnego.](./summary-dashboard.md)
- W przypadku maszyny wirtualnej (VM) oferuje użycie i metryki rozliczeniowe taryfowe, zobacz [Pulpit nawigacyjny użycia w analizie komercyjnego portalu marketplace.](./usage-dashboard.md)
- Aby uzyskać szczegółowe informacje o klientach, w tym trendy wzrostu, zobacz [pulpit nawigacyjny klienta w analizie rynku komercyjnego.](./customer-dashboard.md)
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [Pulpit nawigacyjny pobierania w analizie komercyjnego rynku.](./downloads-dashboard.md)
- Aby wyświetlić skonsolidowany widok opinii klientów dotyczących ofert w witrynie Azure Marketplace i AppSource, zobacz [Pulpit nawigacyjny ocen i opinii w analizie komercyjnego portalu marketplace.](./ratings-reviews.md)
- W przypadku często zadawanych pytań dotyczących analityki rynku komercyjnego i kompleksowego słownika terminów danych zobacz [Często zadawane pytania i terminologia w celu analizy rynku komercyjnego.](./faq-terminology.md)
