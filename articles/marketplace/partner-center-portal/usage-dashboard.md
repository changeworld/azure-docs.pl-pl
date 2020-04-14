---
title: Pulpit nawigacyjny użycia w analizie komercyjnej platformy reklamowej w Centrum partnerów
description: Dowiedz się, jak uzyskać dostęp do wszystkich ofert maszyn wirtualnych i metryk rozliczeniowych taryfowych.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 33762540d14ea51e8325abe9a466007cd7cca748
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262183"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny użycia w analizie rynku komercyjnego

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego użycia w Centrum partnerów. Ten pulpit nawigacyjny wyświetla wszystkie oferty użycia i metryki rozliczeniowe taryfowe w dwóch oddzielnych kartach: użycie maszyny Wirtualnej i użycie rozliczeń taryfowych.

Aby uzyskać dostęp do pulpitu nawigacyjnego Usage, otwórz pulpit nawigacyjny **[Analizy](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze Komercyjna marketplace.

>[!NOTE]
> Szczegółowe definicje terminologii analitycznej można znaleźć w części [Często zadawane pytania i terminologia dotyczące analizy rynku komercyjnego.](./faq-terminology.md)

## <a name="usage-dashboard"></a>Pulpit nawigacyjny Użycie

Pulpit nawigacyjny użycia reprezentuje metryki dla wszystkich maszyn wirtualnych (VM) oferuje użycie i pomiaru rozliczeń użycia. Znajdują się one w dwóch oddzielnych kartach: użycie maszyny Wirtualnej i użycie rozliczeń taryfowych.

Na karcie Użycie maszyny Wirtualnej znajdują się graficzne reprezentacje następujących elementów:

- [Podsumowanie użycia](#usage-summary)
- [Użycie według lokalizacji geograficznej](#usage-by-geography)
- [Wykorzystanie przez oferty](#usage-by-offers)
- [Trend użycia według ofert i jednostek SKU](#usage-trend-by-offers-and-skus)
- [Użycie według typu oferty](#usage-by-offer-type)
- [Użycie według rozmiaru maszyny Wirtualnej](#usage-by-vm-size)
- [Wykorzystanie według kanału sprzedaży](#usage-by-sales-channel)
- [Szczegółowe dane dotyczące użytkowania](#detailed-usage-data)

> [!NOTE]
> Raporty analytics są wyświetlane inaczej w portalu Cloud Partner Portal (CPP) i Centrum partnerów. **Statystyki sprzedawcy** w CPP mają kartę zamówień i użycia, która wyświetla dane zarówno dla ofert opartych na użyciu, jak i ofert nieopartych na użyciu. W Centrum partnerów metryki użycia są wyświetlane na osobnej stronie.

### <a name="usage-summary"></a>Podsumowanie użycia

Tabela podsumowania użycia wyświetla godziny użycia klienta dla wszystkich zakupionych ofert.

- Znormalizowane godziny użytkowania są definiowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godziny użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" używają 1/6 (lub 0,1666) jako mnożnika [liczba rdzeni maszyn wirtualnych].
- Nieprzetworzone godziny użytkowania są definiowane jako czas, przez który maszyny wirtualne były uruchomione pod względem godzin.
- Wartość procentowa reprezentuje zmianę wzrostu użycia dla wybranego zakresu dat ([użycie ostatniego miesiąca — użycie pierwszego miesiąca])/ użycie pierwszego miesiąca).
- Zielone trójkąty skierowane w górę wskazują na zmianę wzrostu.
- Czerwony trójkąt skierowany w dół wskazuje na ujemną zmianę wzrostu w stosunku do poprzedniego miesiąca.
- Wykresy mikrobarów reprezentują wartości miesięczne. Wartość każdego miesiąca można wyświetlić, najeżdżając kursorem na kolumny na wykresie.

### <a name="usage-by-geography"></a>Użycie według lokalizacji geograficznej

**Znormalizowane użycie według mapy cieplnej geografii** wyświetla godziny użycia mapowane według kraju klienta. Zmiana koloru kraju reprezentuje znormalizowane stężenie użycia. Powróć oryginalny widok, naciskając przycisk **home** na mapie.

### <a name="usage-by-offers"></a>Wykorzystanie przez oferty

- **Znormalizowane użycie według wykresu** kołowego oferty wyświetla podział znormalizowanych godzin użytkowania według ofert zgodnie z wybranym zakresem dat. 5 najlepszych ofert jest wyświetlanych na wykresie, a pozostałe są pogrupowane w kategorii "reszta wszystkich".
- Wykres słupkowy przedstawia trend wzrostu miesiąc po miesiącu dla wybranego zakresu dat. Kolumny miesiąca reprezentują godziny użycia z ofert z najwyższymi godzinami użytkowania dla danego miesiąca. Wykres liniowy przedstawia trend procentowy wzrostu wykreślony na drugiej osi Y.
- Użyj suwaka u góry wykresu, aby przewinąć od prawej do lewej wzdłuż osi x i/lub skupić się na określonych punktach danych.

### <a name="usage-trend-by-offers-and-skus"></a>Trend użycia według ofert i jednostek SKU

Na tym wykresie jest wyświetlany trend znormalizowanego użycia dla wybranych jednostek SKU oferty. Ranking oferty wyświetla 50 najlepszych ofert o najwyższym zużyciu i są sortowane według godzin użytkowania. Tabela liderów jednostki SKU wyświetla 50 najlepszych jednostek SKU o najwyższym użyciu dla wybranej oferty.

### <a name="usage-by-offer-type"></a>Użycie według typu oferty

- Wykres **kołowy użycia według typu oferty** organizuje użycie zgodnie z typem oferty.
- Najlepsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "Rest All".
- Wykres **trendu** wyświetla trendy wzrostu miesiąc po miesiącu. Kolumna miesiąc reprezentuje użycie według typów oferty w tym miesiącu.

### <a name="usage-by-vm-size"></a>Użycie według rozmiaru maszyny Wirtualnej

Ten wykres reprezentuje trend użycia dla wybranych rozmiarów maszyn wirtualnych (maksymalnie 5) wszystkich ofert/jednostek SKU. Wykres kolumnowy jest skumulowany z godzinami użycia wybranych rozmiarów maszyn wirtualnych.

Ranking wyświetla 50 najlepszych rozmiarów maszyn wirtualnych o najwyższym użyciu i jest sortowany według godzin użycia.

### <a name="usage-by-sales-channel"></a>Wykorzystanie według kanału sprzedaży

- Użycie według wykresu kołowego kanału sprzedaży organizuje użycie zgodnie z kanałem sprzedaży
- Górny kanał sprzedaży o najwyższym użyciu jest wyświetlany na wykresie, a reszta kanału sprzedaży jest pogrupowana jako "Rest All".
- Kolumna miesiąc reprezentuje użycie przez górny kanał sprzedaży w tym miesiącu.
- Funkcje tego wykresu są takie same jak wykres "Użycie według ofert"

### <a name="detailed-usage-data"></a>Szczegółowe dane dotyczące użytkowania

W **tabeli szczegółów użycia** wyświetlana jest ponumerowana lista 1000 rekordów użycia posortowanych według użycia.

- Każdą kolumnę w siatce można sortować.
- Dane można wyodrębnić do pliku CSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów wynosi ponad 1000, dane eksportu zostaną asynchronicznie umieszczone na stronie pobierania, która będzie dostępna przez następne 30 dni.
- Filtry można zastosować do **szczegółowych danych użycia,** aby wyświetlić tylko dane, które Cię interesują. Dane mogą być filtrowane według kraju, kanału sprzedaży, typu licencji w portalu Marketplace, typu użycia, nazwy oferty, typu oferty, bezpłatnych wersji próbnych, identyfikatora subskrypcji w marketplace, identyfikatora klienta i nazwy firmy.

> [!NOTE]
> Wybierz **typ Użycia** w filtrze strony, aby wyświetlić wykresy na stronie w widoku "Widok znormalizowany" lub "Nieprzetworzony". Domyślnym widokiem tych wykresów jest "Widok znormalizowany".

**Filtry strony Użycie** są stosowane na poziomie strony. Można wybrać wiele filtrów, aby renderować wykres dla kryteriów wybranych do wyświetlania, a dane, które mają być wyświetlane w siatce/eksporcie "szczegółowe dane użycia". Filtry są stosowane do danych wyodrębnionych dla zakresu danych wybranego w prawym górnym rogu strony zamówień.

- **Typy ofert** i **nazwy ofert** są wyświetlane tylko dla ofert nabytych w wybranym zakresie dat. Nazwy ofert na liście są wyświetlane dla typów ofert wybranych z listy.
- Domyślnym wyborem jest "Wszystkie" dla każdej z opcji filtru, z wyjątkiem **typu Użycie**. Domyślnym wyborem **dla typu użycia** jest użycie znormalizowane. Aby wyświetlić użycie surowca na wykresach, wybierz opcję "użycie pierwotne".
- Zastosowane filtry pokazują wybrane(-e) liczbę dla dokonanych wyborów filtrów. Zastosowane filtry nie są wyświetlane dla wyborów domyślnych.

> [!NOTE]
> Szczegółowa definicja każdego z pól w siatce "szczegółowe dane zamówienia", filtry stron i wszystkie możliwe wybory są zdefiniowane w sekcji słownika danych [często zadawanych pytań i terminologii.](link needed)

Karta **Użycie rozliczeń taryfowych** przedstawia informacje o użyciu dla typów ofert, w których użycie jest mierzone według wymiaru na licznik. Obecnie prezentowany jest overage typu oferty SaaS. Zakładka przedstawia graficzne reprezentacje trendów nadpłaty dla użycia rozliczeń taryfowych SaaS:

- **Trend przełęczna według wymiarów liczników**: Wyświetla miesięczny trend nadliczmowania dla wybranego wymiaru licznika oferty. Oś X reprezentuje miesiąc, a oś Y reprezentuje ilość użycia. Jednostka miary miernika niestandardowego jest również wyświetlana na osi Y.
- **Tendencja przekroju wartości SKU**: Reprezentuje tendencję wykorzystania wybranego wymiaru licznika według jednostek SKU. Wyświetlane jednostki SKU będą reprezentować 5 najlepszych jednostek SKU o najwyższej ilości użycia dla wybranej oferty.
- **Trend przewyższenia według 50 najlepszych klientów**: 50 najlepszych ofert z najwyższymi godzinami użytkowania jest wyświetlanych na ***tablicy liderów*** i jest klasyfikowanych według najwyższego wykorzystania licznika niestandardowego. Wybierz klienta w tabeli liderów, aby wyświetlić trend użycia wybranego wymiaru licznika.
- **Trend przewyższenia przez najlepszych klientów**: Przedstawia najlepsze percentyle klientów, które przyczyniają się do % całkowitego użycia. Górny percentyl odbiorcy jest wyświetlany wzdłuż osi X i jest określany przez ilość użycia odbiorcy. Oś Y wyświetla ilość użycia. Szczegóły można wyświetlić, najeżdżając kursorem na punkty wzdłuż wykresu liniowego.

> [!NOTE]
> Szczegóły użycia i wszystkie wykresy na tej stronie są wyświetlane dla dowolnego wymiaru licznika wybranego dla filtru strony.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przegląd raportów analitycznych dostępnych w komercyjnym rynku Centrum partnerów, zobacz [Analytics dla rynku komercyjnego w Centrum partnerów.](./analytics.md)
- Aby uzyskać wykresy, trendy i wartości danych agregujących, które podsumowują aktywność w portalu marketplace dla Twojej oferty, zobacz [Podsumowanie pulpitu nawigacyjnego w analizie rynku komercyjnego.](./summary-dashboard.md)
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz [Pulpit nawigacyjny zamówień w analizie rynku komercyjnego](./orders-dashboard.md).
- Aby uzyskać szczegółowe informacje o klientach, w tym trendy wzrostu, zobacz [Pulpit nawigacyjny klientów w analizie rynku komercyjnego.](./customer-dashboard.md)
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [Pulpit nawigacyjny pobierania w analizie rynku komercyjnego](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów dotyczących ofert w witrynie Azure Marketplace i AppSource, zobacz [Pulpit nawigacyjny ocen i opinii w analizie komercyjnego portalu marketplace.](./ratings-reviews.md)
- W przypadku często zadawanych pytań dotyczących analityki rynku komercyjnego i kompleksowego słownika terminów danych zobacz [Często zadawane pytania i terminologia w celu analizy rynku komercyjnego.](./faq-terminology.md)
