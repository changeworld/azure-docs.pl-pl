---
title: Tworzenie alertów z dynamicznymi progami w usłudze Azure Monitor
description: Tworzenie alertów za pomocą progów dynamicznych opartych na uczeniu maszynowym
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 1d21c7ed93ac2ce2ab61282707d57fbf43e0b71a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261078"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Alerty metryki z dynamicznymi progami w usłudze Azure Monitor

Alert metryki z wykrywaniem progów dynamicznych wykorzystuje zaawansowane uczenie maszynowe (ML), aby nauczyć się historycznych zachowań metryk, zidentyfikować wzorce i anomalie, które wskazują na możliwe problemy z usługą. Zapewnia obsługę zarówno prostego interfejsu użytkownika, jak i operacji na dużą skalę, umożliwiając użytkownikom konfigurowanie reguł alertów za pośrednictwem interfejsu API usługi Azure Resource Manager w sposób w pełni zautomatyzowany.

Po utworzeniu reguły alertu zostanie ona uruchamiana tylko wtedy, gdy monitorowana metryka nie będzie działać zgodnie z oczekiwaniami, na podstawie dostosowanych progów.

Chcielibyśmy usłyszeć twoją opinię, zachować go najbliższych . <azurealertsfeedback@microsoft.com>

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Dlaczego i kiedy zaleca się stosowanie typu warunku dynamicznego?

1. **Skalowalne alerty** — reguły alertów progu dynamicznego mogą tworzyć dostosowane progi dla setek serii metryk naraz, ale zapewniając taką samą łatwość definiowania reguły alertu na pojedynczej metryki. Dają one mniej alertów do tworzenia i zarządzania. Do ich utworzenia można użyć witryny Azure portal lub interfejsu API usługi Azure Resource Manager. Skalowalne podejście jest szczególnie przydatne w przypadku obliczania wymiarów metryki lub podczas stosowania do wielu zasobów, takich jak wszystkie zasoby subskrypcji.  [Dowiedz się więcej o konfigurowaniu alertów metryk z progami dynamicznymi przy użyciu szablonów](alerts-metric-create-templates.md).

1. **Inteligentne rozpoznawanie wzorców metrycznych** — dzięki naszej technologii ML jesteśmy w stanie automatycznie wykrywać wzorce metryczne i dostosowywać się do zmian metrycznych w czasie, które często mogą obejmować sezonowość (co godzinę / codziennie / co tydzień). Dostosowanie do zachowania metryki w czasie i alerty na podstawie odchyleń od jego wzorca zmniejsza ciężar poznania progu "prawo" dla każdej metryki. Algorytm ml używany w dynamicznych progów ma na celu zapobieganie hałaśliwe (niska precyzja) lub szeroki (niskie wycofanie) progi, które nie mają oczekiwanego wzorca.

1. **Intuicyjna konfiguracja** — dynamiczne progi umożliwia konfigurowanie alertów metryk przy użyciu koncepcji wysokiego poziomu, co łagodzi potrzebę posiadania rozległej wiedzy na temat domeny na temat metryki.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Jak skonfigurować reguły alertów za pomocą progów dynamicznych?

Alerty z progami dynamicznymi można skonfigurować za pomocą alertów metryk w usłudze Azure Monitor. [Dowiedz się więcej o konfigurowaniu alertów metryk .](alerts-metric.md)

## <a name="how-are-the-thresholds-calculated"></a>Jak obliczane są progi?

Progi dynamiczne stale uczy się danych serii metryk i próbuje modelować go przy użyciu zestawu algorytmów i metod. Wykrywa wzorce w danych, takie jak sezonowość (hourly / daily / weekly) i jest w stanie obsługiwać hałaśliwe metryki (takie jak procesor maszynowy lub pamięć), a także metryki o niskim rozproszeniu (takie jak dostępność i poziom błędu).

Progi są wybierane w taki sposób, że odchylenie od tych progów wskazuje anomalię w zachowaniu metryki.

> [!NOTE]
> Wykrywanie wzorców sezonowych jest ustawione na interwał godzin, dzień lub tydzień. Oznacza to, że inne wzorce, takie jak wzór dwugodzinny lub semiweekly, mogą nie zostać wykryte.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Co oznacza ustawienie "Czułość" w progach dynamicznych?

Czułość progu alertu jest pojęciem wysokiego poziomu, który kontroluje ilość odchylenia od zachowania metryki wymagane do wyzwolenia alertu.
Ta opcja nie wymaga wiedzy domeny na temat metryki, takiej jak próg statyczny. Dostępne opcje:

- Wysoki — progi będą szczelne i zbliżone do wzorca serii metryki. Reguła alertu zostanie wyzwolona w najmniejszym odchyleniu, co spowoduje więcej alertów.
- Średni — mniej ciasne i bardziej zrównoważone progi, mniej alertów niż przy wysokiej czułości (domyślnie).
- Niski — progi będą luźne z większą odległością od wzoru serii metrycznej. Reguła alertu wyzwoli tylko duże odchylenia, co spowoduje zmniejszenie liczby alertów.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Jakie są opcje ustawień "Operator" w progach dynamicznych?

Reguła alertów progów dynamicznych może tworzyć dostosowane progi na podstawie zachowania metryki dla górnej i dolnej granicy przy użyciu tej samej reguły alertu.
Możesz wybrać alert, który ma zostać wyzwolony w jednym z następujących trzech warunków:

- Większy niż górny próg lub niższy niż dolny próg (domyślny)
- Większa niż górny próg
- Niższy niż dolny próg.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Co oznaczają ustawienia zaawansowane w dynamic progach?

**Okresy niepowodzenia** — progi dynamiczne umożliwiają również skonfigurowanie "Naruszenia numeru w celu wyzwolenia alertu", minimalnej liczby odchyleń wymaganych w określonym przedziale czasowym, aby system podniósł alert (domyślny przedział czasu to cztery odchylenia w ciągu 20 minut). Użytkownik może skonfigurować okresy awarii i wybrać, co ma być alerty na zmieniając okresy awarii i przedział czasu. Ta zdolność zmniejsza hałas alertu generowany przez przemijające skoki. Przykład:

Aby wyzwolić alert, gdy problem jest ciągły przez 20 minut, 4 razy z rzędu w danym okresie grupowania 5 minut, należy użyć następujących ustawień:

![Nie można wywiązyć się z ustawień okresów ciągłego przez 20 minut, 4 razy z rzędu w danym okresie grupowania 5 minut](media/alerts-dynamic-thresholds/0008.png)

Aby wyzwolić alert, gdy wystąpiło naruszenie progów dynamicznych w ciągu 20 minut z ostatnich 30 minut z okresem 5 minut, użyj następujących ustawień:

![Nie można wywiązywanie się z ustawień okresów dla wydania przez 20 minut z ostatnich 30 minut z grupowaniem okresów 5 minut](media/alerts-dynamic-thresholds/0009.png)

**Ignoruj dane przed** — użytkownicy mogą również opcjonalnie zdefiniować datę rozpoczęcia, od której system powinien rozpocząć obliczanie progów od. Typowy przypadek użycia może wystąpić, gdy zasób był uruchomiony w trybie testowania i jest teraz promowany do obsługi obciążenia produkcyjnego, a zatem zachowanie dowolnej metryki podczas fazy testowania powinny być pominięte.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Jak dowiedzieć się, dlaczego został uruchomiony alert Progi dynamiczne?

Możesz eksplorować wyzwalane wystąpienia alertów w widoku alertów, klikając łącze w wiadomości e-mail lub wiadomości tekstowej lub przeglądarkę, aby wyświetlić widok alertów w witrynie Azure portal. [Dowiedz się więcej o widoku alertów](alerts-overview.md#alerts-experience).

W widoku alertu są wyświetlane:

- Wszystkie szczegóły metryki w momencie alertu dynamiczne progi zwolniony.
- Wykres okresu, w którym alert został wyzwolony, który zawiera progi dynamiczne używane w tym momencie.
- Możliwość przekazywania informacji zwrotnych na temat alertu Progi dynamiczne i środowiska widoku alertów, co może poprawić wykrywanie w przyszłości.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Czy powolne zachowanie zmieni się w metryki wyzwalania alertu?

Prawdopodobnie nie. Progi dynamiczne są dobre do wykrywania znaczących odchyleń, a nie powoli zmieniających się problemów.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Ile danych jest używanych do podglądu, a następnie obliczania progów?

Po utworzeniu reguły alertu progi pojawiające się na wykresie są obliczane na podstawie wystarczającej ilości danych historycznych do obliczania godzin lub dziennych wzorców sezonowych (10 dni). Po utworzeniu reguły alertu dynamiczne progi używają wszystkich potrzebnych danych historycznych, które są dostępne i będą stale uczyć się i dostosowywać na podstawie nowych danych, aby progi były dokładniejsze. Oznacza to, że po tym obliczeniu wykres będzie również wyświetlał wzorce tygodniowe.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Ile danych jest potrzebne do wyzwolenia alertu?

Jeśli masz nowy zasób lub brakujące dane metryki, dynamiczne progi nie będą wyzwalać alertów przed trzema dniami i co najmniej 30 przykładami danych metryk są dostępne, aby zapewnić dokładne progi.
W przypadku istniejących zasobów z wystarczającą ilością danych metryk dynamiczne progi mogą natychmiast wyzwalać alerty.

## <a name="dynamic-thresholds-best-practices"></a>Najważniejsze wskazówki dotyczące progów dynamicznych

Progi dynamiczne mogą być stosowane do dowolnej platformy lub metryki niestandardowej w usłudze Azure Monitor i został również dostrojony do typowych metryk aplikacji i infrastruktury.
Poniższe elementy są najlepsze rozwiązania dotyczące konfigurowania alertów na niektóre z tych metryk przy użyciu progów dynamicznych.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Progi dynamiczne w metrykach procentowych procesora CPU maszyny wirtualnej

1. W [witrynie Azure portal](https://portal.azure.com)kliknij **monitor**. Widok Monitor konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij **pozycję Alerty,** a następnie kliknij przycisk **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów ma również **alerty** w menu zasobów w obszarze **Monitorowanie**, można utworzyć alerty stamtąd, jak również.

3. Kliknij **pozycję Wybierz miejsce docelowe**, w okienku kontekstu, które się ładuje, wybierz zasób docelowy, na który chcesz powiadomić. Użyj **subskrypcji** i **"Maszyny wirtualne" Zasób typu** rozwijane, aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć swój zasób.

4. Po wybraniu zasobu docelowego kliknij przycisk **Dodaj warunek**.

5. Wybierz **"Procent procesora"**.

6. Opcjonalnie uściślaj metrykę, dostosowując **okres** i **agregację**. Jest odradzane do używania "Maksymalny" typ agregacji dla tego typu metryki, ponieważ jest mniej reprezentatywne dla zachowania. Dla "Maksymalny" próg statyczny typu agregacji może być bardziej odpowiednie.

7. Zostanie wyświetlony wykres dla danych z ostatnich 6 godzin. Zdefiniuj parametry alertu:
    1. **Typ warunku** — wybierz opcję "Dynamiczny".
    1. **Czułość** - Wybierz średnią/niską czułość, aby zmniejszyć hałas alertu.
    1. **Operator** — wybierz "Większe niż", chyba że zachowanie reprezentuje użycie aplikacji.
    1. **Częstotliwość** — należy rozważyć obniżenie na podstawie wpływu biznesowego alertu.
    1. **Okresy niepowodzenia** (opcja zaawansowana) — okno spojrzenia wstecz powinno wynosić co najmniej 15 minut. Na przykład jeśli okres jest ustawiony na pięć minut, a następnie okresy niepowodzenia powinny być co najmniej trzy lub więcej.

8. Wykres metryczny wyświetli obliczone progi na podstawie ostatnich danych.

9. Kliknij przycisk **Gotowe**.

10. Wypełnij **szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis**i **Ważność**.

11. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

12. Kliknij **przycisk Gotowe,** aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pośrednictwem portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Progi dynamiczne w czasie wykonywania żądania HTTP usługi Application Insights

1. W [witrynie Azure portal](https://portal.azure.com)kliknij **monitor**. Widok Monitor konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij **pozycję Alerty,** a następnie kliknij przycisk **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów ma również **alerty** w menu zasobów w obszarze **Monitorowanie**, można utworzyć alerty stamtąd, jak również.

3. Kliknij **pozycję Wybierz miejsce docelowe**, w okienku kontekstu, które się ładuje, wybierz zasób docelowy, na który chcesz powiadomić. Użyj **subskrypcji** i **"Application Insights" Zasób typu** rozwijane, aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć swój zasób.

4. Po wybraniu zasobu docelowego kliknij przycisk **Dodaj warunek**.

5. Wybierz **"Czas wykonywania żądania HTTP"**.

6. Opcjonalnie uściślaj metrykę, dostosowując **okres** i **agregację**. Jest odradzane do używania "Maksymalny" typ agregacji dla tego typu metryki, ponieważ jest mniej reprezentatywne dla zachowania. Dla "Maksymalny" próg statyczny typu agregacji może być bardziej odpowiednie.

7. Zostanie wyświetlony wykres dla danych z ostatnich 6 godzin. Zdefiniuj parametry alertu:
    1. **Typ warunku** — wybierz opcję "Dynamiczny".
    1. **Operator** — wybierz opcję "Większa niż", aby zmniejszyć liczbę alertów uruchamianych po poprawie czasu trwania.
    1. **Częstotliwość** — należy rozważyć obniżenie na podstawie wpływu biznesowego alertu.

8. Wykres metryczny wyświetli obliczone progi na podstawie ostatnich danych.

9. Kliknij przycisk **Gotowe**.

10. Wypełnij **szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis**i **Ważność**.

11. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

12. Kliknij **przycisk Gotowe,** aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pośrednictwem portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretowanie dynamicznych wykresów progowych

Poniżej znajduje się wykres przedstawiający metrykę, jej dynamiczne limity progowe i niektóre alerty uruchamiane, gdy wartość znajdowała się poza dozwolonymi progami.

![Dowiedz się więcej o konfigurowaniu alertów metrycznych](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Aby zinterpretować poprzedni wykres, użyj następujących informacji.

- **Niebieska linia** — rzeczywista zmierzona metryka w czasie.
- **Niebieski obszar cieniowany** — pokazuje dozwolony zakres dla metryki. Tak długo, jak wartości metryki pozostają w tym zakresie, nie wystąpi alert.
- **Niebieskie kropki** — jeśli klikniesz lewym przyciskiem myszy część wykresu, a następnie najedziesz kursorem na niebieską linię, pod kursorem pojawi się niebieska kropka z indywidualną zagregowanym wskaźnikiem.
- **Wyskakujące okienko z niebieską kropką** — pokazuje zmierzoną wartość metryczną (niebieską kropkę) oraz górną i dolną wartość dozwolonego zakresu.  
- **Czerwona kropka z czarnym kółkiem** — pokazuje pierwszą wartość metryczną poza dozwolonym zakresem. Jest to wartość, która uruchamia alert metryki i umieszcza go w stanie aktywnym.
- **Czerwone kropki**— wskazuje dodatkowe zmierzone wartości poza dozwolonym zakresem. Nie będą uruchamiać dodatkowych alertów metryk, ale alert pozostaje w aktywnym.
- **Czerwony obszar** — pokazuje czas, kiedy wartość metryki znajdowała się poza dozwolonym zakresem. Alert pozostaje w stanie aktywnym, tak długo, jak kolejne zmierzone wartości są poza dozwolonym zakresem, ale nie są uruchamiane żadne nowe alerty.
- **Koniec czerwonego obszaru** — gdy niebieska linia powraca do dozwolonych wartości, czerwony obszar zatrzymuje się, a zmierzona linia wartości zmienia kolor na niebieski. Stan alertu metryki uruchamianego w czasie czerwonej kropki z czarnym konturem jest ustawiony na rozwiązany. 
