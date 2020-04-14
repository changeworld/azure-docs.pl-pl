---
title: Pulpit nawigacyjny podsumowania dla analiz Centrum partnerów w portalu Commercial Marketplace
description: Dowiedz się, jak uzyskać dostęp do wykresów, trendów i wartości danych zagregowanych, które podsumowują aktywność w portalu marketplace z pulpitu nawigacyjnego Podsumowanie w Centrum partnerów.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262404"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny podsumowania w analizie rynku komercyjnego

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego podsumowanie w Centrum partnerów. Ten pulpit nawigacyjny wyświetla wykresy, trendy i wartości danych zagregowanych, które podsumowują aktywność w rynku dla Twoich ofert.

Aby uzyskać dostęp do pulpitu nawigacyjnego Podsumowanie, otwórz **[pulpit nawigacyjny Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze Komercyjnego portalu Marketplace.

>[!NOTE]
> Szczegółowe definicje terminologii analitycznej można znaleźć w części [Często zadawane pytania i terminologia dotyczące analizy rynku komercyjnego.](./faq-terminology.md)

## <a name="summary-dashboard"></a>Pulpit nawigacyjny Podsumowanie

Pulpit nawigacyjny **Podsumowanie** przedstawia przegląd na podstawie każdego typu oferty. **Statystyki** pokazują krytyczne informacje w skrócie i zapewniają szeroki obraz aktywności sprzedażowej twoich ofert. Te raporty można wizualizować za pomocą pulpitu nawigacyjnego **Podsumowanie.** W tym artykule przedstawiono więcej szczegółów na temat każdego z następujących elementów:

- [Zakres dat](#date-range)
- [Sekcja Podsumowanie](#summary-section)
- [Klienci według geografii](#customers-by-geography)
- [Wykresy trendów wzrostu](#growth-trend-charts)
- [Ranking klientów](#customer-leaderboard)
- [Trend liczenia miejsc](#seat-count-trend)
- [Darmowe wersje próbne Trend zamówień SaaS](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementy pulpitu nawigacyjnego Podsumowanie

W poniższych sekcjach opisano sposób korzystania z pulpitu nawigacyjnego podsumowania i sposobu odczytywania danych.

### <a name="date-range"></a>Zakres dat

Wybór zakresu dat można znaleźć w prawym górnym rogu każdej strony. Dostosuj dane wyjściowe wykresów stron **Podsumowanie,** wybierając zakres dat na podstawie ostatnich 3, 6 lub 12 miesięcy lub wybierając niestandardowy zakres dat o maksymalnym czasie trwania 12 miesięcy. Domyślny zakres dat to sześć miesięcy.

![Pulpit nawigacyjny analizy centrum partnerów](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sekcja Podsumowanie

W sekcji Podsumowanie jest wyświetlana liczba wszystkich utworzonych zamówień, nabytych klientów i użycia zgłoszonych w wybranym zakresie dat. Częściowy bieżący miesiąc zostanie wykluczony z obliczeń tych metryk. Na przykład: Jeśli wybrano przedział czasu 6M, godziny użycia są obliczane dla sześciu miesięcy poprzedzających bieżący miesiąc. Jeśli wybrano niestandardowy zakres dat, część z bieżącego miesiąca zostanie wykluczona z obliczeń.

![Trendy wzrostu na pulpicie nawigacyjnym podsumowania](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Czytanie sekcji podsumowania

- **Zamówienia**: Liczba wszystkich zakupionych zamówień (z wyłączeniem anulowanych zamówień) dla ofert, które do tej pory opublikowałeś.
- **Klienci**: Liczba wszystkich klientów, którzy zakupili Twoje oferty i mają co najmniej jedno nieza anulowane zamówienie.
- **Znormalizowane godziny użytkowania:** Zdefiniowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godziny użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" używają 1/6 (lub 0,1666) jako mnożnika [liczba rdzeni maszyn wirtualnych].
- **Nieprzetworzone godziny użytkowania:** czas działania maszyn wirtualnych pod względem godzin. Wartość procentowa obok **łącznej liczby zamówień**, całkowita liczba **klientów,** **znormalizowane godziny użytkowania,** **nieprzetworzone godziny użytkowania,** **wizyty na stronie**i wezwania do **akcji** reprezentują ilość wzrostu użycia dla wybranego zakresu dat ([użycie w ostatnim miesiącu — użycie pierwszego miesiąca])/ użycie pierwszego miesiąca). Jak opisano powyżej, częściowa kwota bieżącego miesiąca zostanie wykluczona z tej metryki.
- **Trendy wzrostu:** Jeśli najedziesz kursorem na kolumny wykresu, wykresy słupkowe wyświetlają wartość dla każdego miesiąca.
- **Zielony trójkąt skierowany w górę**: Wskazuje na pozytywny trend wzrostu.
- **Czerwony trójkąt skierowany w dół**: Wskazuje ujemny trend wzrostu w stosunku do poprzedniego miesiąca.

### <a name="customers-by-geography"></a>Klienci według geografii

Mapa cieplna **Klienci według lokalizacji** geograficznie wyświetla liczbę klientów na mapie świata.

![Klienci według lokalizacji geograficznej w pulpicie nawigacyjnym podsumowania](./media/summary-customers-by-geography.png)

- Możesz przenieść mapę, aby wyświetlić dokładną lokalizację.
- Można powiększyć określoną lokalizację.
- Mapa cieplna ma dodatkową siatkę do wyświetlania szczegółów liczby klientów, liczby zamówień, znormalizowanych godzin użytkowania w określonej lokalizacji.
- Możesz wyszukiwać i wybierać kraj w siatce, aby powiększyć lokalizację na mapie. Powróć oryginalny widok, naciskając przycisk **Początek** na mapie.
- **Nowy** klient zakupił jedną z twoich ofert po raz pierwszy w ciągu miesiąca w wybranym zakresie dat.

### <a name="growth-trend-charts"></a>Wykresy trendów wzrostu

Możesz wyświetlać trendy na podstawie wzrostu **zakupionych zamówień** (w tym anulowanych zamówień), **nabytych klientów** (w tym utraconych klientów) i **zgłoszonych użycia,** które są wyświetlane miesiąc po miesiącu zgodnie z wybranym zakresem dat. Możesz dalej analizować te trendy, wybierając łącza poniżej wykresu, które przechodzą do odpowiednich **stron zamówienia,** **użycia,** **klienta**lub **aplikacji Marketplace Insights.**

Wizyty na stronie oferty w portalu Marketplace i wykresy trendów **wezwania do działania** są wyświetlane dla witryny Azure marketplace i usługi AppSource na dwóch kartach.

![Wizyty na stronie i wykresy trendów połączeń z działaniami na pulpicie nawigacyjnym podsumowania](./media/summary-page-visits-and-cta.png)

Wykres **zamówień według ofert** organizuje zamówienia zgodnie z nazwą oferty.

Wykres kołowy **zamówień według kanału sprzedaży** organizuje zamówienia (w tym zamówienia anulowane przez klientów) w wybranym zakresie dat według kanału sprzedaży. Kanał sprzedaży to rodzaj umowy licencyjnej używanej przez klientów do zakupu platformy Azure, która jest dostawcą rozwiązań w chmurze (CSP), przedsiębiorstwem, przedsiębiorstwem za pośrednictwem sprzedawcy, GTM i płatności zgodnie z tobą.

**Wykorzystanie przez oferty** i **wykorzystanie przez wykresy** kołowe kanału sprzedaży przedstawiają podział użycia według najlepszych ofert i kanałów sprzedaży, odpowiednio. Wewnętrzny wykres kołowy reprezentuje użycie nieprzetworzone, a zewnętrzny wykres kołowy reprezentuje znormalizowane użycie.

**Zamówienia według typu licencji w portalu Marketplace** i użycie według wykresów kołowych typu licencji w portalu **Marketplace** wyświetlają podział zamówień i użycia według ich odpowiedniego typu licencji. Typy licencji obejmują:

- **Rozliczane za pośrednictwem platformy Azure:** Microsoft rozlicza klientów w Twoim imieniu, gdy zdecydujesz się sprzedać ofertę za pośrednictwem firmy Microsoft z tego typu licencji. Typy płatności obejmują płatność zgodnie z rzeczywistym wezwaniem za pomocą karty kredytowej lub fakturowania enterprise.
- **Przynieś własną licencję:** Firma Microsoft nie rozlicza klientów za ich użycie w ramach tego typu oferty marketplace. To użycie jest wymienione jako **Pobierz teraz (za darmo)** na rynku.
- **Bezpłatne**: Firma Microsoft nie rozlicza klientów za ich użycie w ramach tego typu oferty marketplace. To użycie jest wymienione jako **bezpłatna wersja próbna** na rynku.
- **Microsoft jako sprzedawca**: Reprezentuje oferty sprzedawane przez sprzedawców firmy Microsoft w ramach **programu Cloud Solution Provider (CSP).**

### <a name="customer-leaderboard"></a>Ranking klientów

50 najlepszych klientów z największą liczbą zamówień jest wyświetlanych na *tablicy liderów*, posortowanych według najwyższej liczby zamówień i procentu zamówienia.

- Wybierz klienta, aby wyświetlić szczegóły profilu, zamówienia uporządkowane według oferty lub zamówienia uporządkowane według typu licencji platformy Azure i kanału cenowego.
- Wykres pączków **Oferty według zamówień** przedstawia cztery najlepsze oferty (według liczby zamówień), a pozostałe oferty pogrupowane jako "Rest All".
- **Znormalizowane wykorzystanie według oferty** wykres pierścieniowy przedstawia pięć najlepszych ofert według użycia.

> [!NOTE]
> Dane osobowe Klienta będą przedstawiane tylko wtedy, gdy klient wyraził na to zgodę. Można wyświetlić te informacje, jeśli zalogowano się na poziomie uprawnień roli **właściciela.** Użytkownicy z rolą **Współautor** nie będą mogli wyświetlać tych informacji. [Dowiedz się więcej o rolach i uprawnieniach użytkowników](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend liczby miejsc

**Zamówienia według wykresu na miejsce/ na miejscu** przedstawiają podział wszystkich zamówień zakupionych zgodnie z modelem cenowym. Wykres **trendu liczby miejsc** przedstawia miejsca w porównaniu z zamówieniami zakupionymi dla wszystkich ofert oprogramowania na miejsce Software as a Service (SaaS).

### <a name="free-trials-saas-orders-trend"></a>Darmowe wersje próbne Trend zamówień SaaS

**Bezpłatny okres próbny Wykres trendu zamówień SaaS** przedstawia trend zamówień na bezpłatne wersje próbne SaaS oferuje 30-dniowy okres próbny.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przegląd raportów analitycznych dostępnych w komercyjnym rynku Centrum partnerów, zobacz [Analytics dla rynku komercyjnego w Centrum partnerów.](./analytics.md)
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz [Pulpit nawigacyjny zamówień w analizie rynku komercyjnego](./orders-dashboard.md).
- W przypadku maszyny wirtualnej (VM) oferuje użycie i metryki rozliczeniowe taryfowe, zobacz [Pulpit nawigacyjny użycia w analizie komercyjnego rynku.](./usage-dashboard.md)
- Aby uzyskać szczegółowe informacje o klientach, w tym trendy wzrostu, zobacz [Pulpit nawigacyjny klientów w analizie rynku komercyjnego.](./customer-dashboard.md)
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [Pulpit nawigacyjny pobierania w analizie rynku komercyjnego](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów dotyczących ofert w witrynie Azure Marketplace i AppSource, zobacz [Pulpit nawigacyjny ocen i opinii w analizie komercyjnego portalu marketplace.](./ratings-reviews.md)
- W przypadku często zadawanych pytań dotyczących analityki rynku komercyjnego i kompleksowego słownika terminów danych zobacz [Często zadawane pytania i terminologia w celu analizy rynku komercyjnego.](./faq-terminology.md)
