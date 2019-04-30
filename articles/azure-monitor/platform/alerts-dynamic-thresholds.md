---
title: Tworzenie alertów z dynamicznymi progami w usłudze Azure Monitor
description: Tworzenie alertów za pomocą usługi machine learning na podstawie dynamicznymi progami
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 772401c286a50774d201703cefcbbc12f0fcf88f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775812"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Alerty metryk z dynamicznymi progami w usłudze Azure Monitor (publiczna wersja zapoznawcza)

Alert dotyczący metryki z dynamicznymi progami wykrywania korzysta z zaawansowanych (ML), aby dowiedzieć się zachowanie historyczne metryk, zidentyfikować wzorce i anomalie, które wskazują na problemy możliwe usługi uczenia maszynowego. Zapewnia obsługę zarówno prostego interfejsu użytkownika, jak i operacji na dużą skalę, pozwalając użytkownikom na konfigurowanie reguł alertów za pośrednictwem interfejsu API usługi Azure Resource Manager, w pełni zautomatyzowany sposób.

Po utworzeniu reguły alertu jego uruchomienie nastąpi tylko, gdy monitorowane metryki nie działa zgodnie z oczekiwaniami, na podstawie jego progów dostosowanych do potrzeb.

Chętnie poznamy Twoją opinię i zapewnić jej mieszczących się na <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Kiedy i dlaczego jest przy użyciu typu warunek dynamiczny, zaleca się?

1. **Skalowalna alerty** — dynamicznymi progami reguł alertów można tworzyć dostosowane progi setek serii metryki naraz. Zapewnianie jeszcze tak samo łatwe definiowania reguły alertu na jedną metrykę. Przy użyciu interfejsu użytkownika lub wyników interfejsu API usługi Azure Resource Manager w mniejszej liczby reguł alertów do zarządzania. Skalowalne podejście jest szczególnie przydatne podczas rozwiązywania problemów związanych z wymiarami metryki lub mające zastosowanie do wielu zasobów, takich jak wszystkie zasoby w subskrypcji. Co przekłada się na znaczną ilość czasu, zapisywania na zarządzanie i tworzenia reguł alertów. [Dowiedz się więcej o sposobie konfigurowania alertów metryk z dynamicznymi progami przy użyciu szablonów](alerts-metric-create-templates.md).

1. **Blokada Smart rozpoznawanie wzorców metryki** — przy użyciu unikatowej technologii uczenia Maszynowego, jesteśmy w stanie automatyczne wykrywanie wzorców metryki i reagowanie na zmiany metryk wraz z upływem czasu, co często może obejmować sezonowości (co godzinę / codziennie / co tydzień). Dostosowanie metryk zachowanie przestrzeni czasu i alertów w oparciu o odchyleniach od sposobu ich zwalnia obciążenia wiedzy "prawo" próg wszystkie metryki. Algorytm uczenia Maszynowego, używane w dynamicznymi progami podsłuchem hałas (precyzja niski) lub progów całej (wycofaniu niski), które nie ma oczekiwanego wzorca.

1. **Intuicyjne konfiguracji** — dynamicznymi progami umożliwia konfigurowanie alertów dotyczących metryk za pomocą szczegółowo pojęcia, pozwalające uniknąć konieczności mieć domeny obszerną wiedzę na temat metrykę.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Jak skonfigurować reguły alerty z dynamicznymi progami?

Alerty z dynamicznymi progami można skonfigurować za pomocą alertów metryk w usłudze Azure Monitor. [Dowiedz się więcej o sposobie konfigurowania alertów metryk](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Jak są obliczane progi

Dynamicznymi progami ciągle uczy się dane serii metryki i próbuje go za pomocą zestawu algorytmów i metod modelu. Wykrywa wzorce w danych, takich jak sezonowość (co godzinę / codziennie / co tydzień) i będzie mogło obsłużyć generujące dużo alertów metryk (np. maszyn procesora CPU lub pamięci) oraz metryki o niskim rozproszenia (np. wskaźnik dostępności i błędów).

Progi są zaznaczone w taki sposób, że odchylenie od tych progów wskazuje anomalii zachowania metryki.

> [!NOTE]
> Wykrywanie wzorcu sezonowym ustawiono interwał godziny, dnia lub tygodnia. Oznacza to, że inne wzorce like wzór bihourly lub semiweekly mogą nie zostać wykryte.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Co to jest ustawienie "Ważność" oznacza dynamicznymi progami?

Czułość próg alertu jest koncepcja wysokiego poziomu, który kontroluje ilość odchylenie od zachowania metryki wymaganym do wyzwolenia alertu.
Ta opcja nie wymaga domeny wiedzę na temat metryk, takich jak statyczny próg. Dostępne opcje:

- Wysoki — progów będzie obraz blisko wzorzec serii metryki. Reguła alertu zostanie wyzwolone na najmniejszą odchylenie, co większej liczby alertów.
- Średni — mniej progi ścisłej i zrównoważonego alerty mniej niż Wysoka czułość (ustawienie domyślne).
- Niska — progów będzie luźne za pomocą więcej odległości od wzorca serii metryki. Reguła alertu wyzwoli się tylko na duże odchylenia, co mniej alertów.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Jakie są opcje ustawienia 'Operator' w dynamicznymi progami?

Dynamicznymi progami, który może utworzyć regułę alertów dostosować progi na podstawie zachowania metryki wielkich i dolne granice przy użyciu tego samego reguły alertu.
Można wybrać alertu dla jednej z trzech następujących warunków:

- Większy niż próg górny lub mniejsza niż niższa wartość progową (ustawienie domyślne)
- Większa niż górny próg
- Mniejsza niż niższa wartość progową.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Zaawansowane ustawienia w oznaczają dynamicznymi progami co zrobić?

**Niepowodzenie okresy** -dynamicznymi progami umożliwia również skonfigurowanie "Liczba naruszeń wyzwalających alert", minimalna liczba odchyleń wymagane w niektórych przedziale czasu dla systemu, aby zgłosić alert (domyślny przedział czasu to cztery odchylenia 20 minut). Użytkownik może skonfigurować w przypadku braku kropki i wybierz, co chcesz otrzymywać alerty o, zmieniając okresy niepowodzeniem i przedziału czasu. Ta możliwość powoduje zmniejszenie liczby niepotrzebnych alertów generowanych przez przejściowe skoki. Na przykład:

Aby wyzwolić alert, gdy problem polega na ciągłym na 20 minut, 4 razy pod rząd w danym grupowaniu okresu, wynoszącą 5 minut, użyj następujących ustawień:

![Kończy się niepowodzeniem ustawienia okresy ciągłe problemu na 20 minut, 4 razy pod rząd w danym grupowaniu okresu, 5 minut](media/alerts-dynamic-thresholds/0008.png)

Aby wyzwolić alert podczas naruszenie z dynamicznymi progami w ciągu 20 minut z ostatnich 30 minut od okresu 5 minut, użyj następujących ustawień:

![Niepowodzenie okresy ustawienia problemu przez 20 minut z ostatnich 30 minut przy użyciu grupowania okresu 5 minut](media/alerts-dynamic-thresholds/0009.png)

**Ignoruj dane przed** — użytkownicy mogą również opcjonalnie określić datę rozpoczęcia, z której system powinien się zacząć obliczanie wartości progowe z. Typowym przypadkiem użycia może wystąpić, gdy zasób został uruchomiony w trybie tworzenia testowania i teraz jest promowany do obsługi obciążeń produkcyjnych i w związku z tym zachowaniem dowolnej metryce podczas fazy testowania powinien zostać pominięty.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Jak możesz dowiedzieć się dlaczego został wyzwolony alert dotyczący dynamicznymi progami?

Możesz eksplorować wyzwolonych alertów wystąpień w widoku alertów, klikając łącze w wiadomości e-mail lub wiadomości SMS lub przeglądarki, aby wyświetlić alerty, wyświetlić w witrynie Azure portal. [Dowiedz się więcej o widoku alertów](alerts-overview.md#alerts-experience).

Wyświetla widok alertów:

- Szczegóły metryki w tej chwili alertu dynamicznymi progami uruchamiane.
- Wykres czasu, w którym alert został wyzwalacz, który zawiera dynamicznymi progami używane w danym momencie.
- Możliwość się opinią na temat alertów dynamicznymi progami i alerty doświadczenia widoku, co może poprawić wykrywanie przyszłych.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Powolne działanie zmieni się w wyzwalaczu metryki alert?

Prawdopodobnie nie. Dynamicznymi progami dla zastosowań dobre są wykrywanie znaczące odchylenia, a nie wolno zmieniających się problemy.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Jak dużo danych jest używane do przeglądania i następnie obliczyć progi?

Progi pojawiające się na wykresie, przed utworzeniem reguły alertu na metryce, są obliczane na podstawie danych historycznych wystarczająco w celu obliczania godzinę lub codziennie wzorców sezonowych (10 dni). Po utworzeniu reguły alertu dynamicznymi progami zużyjesz wszystkie niezbędne dane historyczne, które są dostępne, a dowiesz się stale i doświadczenie, na podstawie nowych danych umożliwiają bardziej precyzyjne progów. Oznacza to, że po tym wykresie obliczenia będą również wyświetlane tygodniowe wzorców.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Jak dużo danych jest wymagana do wyzwolenia alertu?

Jeśli masz nowy zasób lub brakujące dane metryk dynamicznymi progami nie spowoduje wyzwolenia alertów przed trzy dni dla danych są dostępne dla zapewnienia dokładnych wartości progowe.

## <a name="dynamic-thresholds-best-practices"></a>Dynamiczne progi najlepszych rozwiązań.

Dynamicznymi progami mogą być stosowane do dowolnej platformy lub metryk niestandardowych w usłudze Azure Monitor, a także została dostosowana dla typowych metryk aplikacji i infrastruktury.
Następujące elementy są najlepsze rozwiązania dotyczące sposobu konfigurowania alertów dla niektórych z tych wskaźników przy użyciu dynamicznymi progami.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamicznymi progami dotyczące metryk procent procesorów maszyny wirtualnej

1. W [witryny Azure portal](https://portal.azure.com), kliknij pozycję **Monitor**. Widok monitora konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

2. Kliknij przycisk **alerty** kliknięcie **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów również ma **alerty** w ich menu zasobów w ramach **monitorowanie**, alerty można utworzyć, także z niego.

3. Kliknij przycisk **Wybieranie lokalizacji docelowej**, w okienku kontekstowym, który ładuje, zaznacz zasób docelowy, który ma zostać wyświetlony alert. Użyj **subskrypcji** i **typu zasobu "Maszyn wirtualnych"** list rozwijanych można znaleźć zasobu, który chcesz monitorować. Umożliwia także na pasku wyszukiwania można znaleźć zasobu.

4. Po wybraniu zasobu docelowego kliknij **Dodaj warunek**.

5. Wybierz **"Procent użycia procesora CPU"**.

6. Opcjonalnie można dostosować metryki, dostosowując **okres** i **agregacji**. Zaleca się do użycia typ agregacji "Maximum" dla tego typu metryki, ponieważ jest mniej reprezentatywne zachowanie. "Maksymalna" progu statycznych typu agregacji może być więcej właściwe.

7. Zostanie wyświetlony wykres metryki przez ostatnie 6 godzin. Zdefiniuj parametry alertu:
    1. **Typ warunku** — wybierz opcję "Dynamic".
    1. **Czułość** — wybierz umiarkowanym czułości w celu ograniczenia liczby niepotrzebnych alertów.
    1. **Operator** — wybierz pozycję "Większe niż", chyba że zachowanie reprezentuje użycia aplikacji.
    1. **Częstotliwość** — należy wziąć pod uwagę obniżenia oparte na działalność alertu.
    1. **Niepowodzenie okresy** (opcja zaawansowana) — okno wstecz wygląd powinien być co najmniej 15 minut. Na przykład jeśli w okresie jest równa pięć minut, następnie kończy się niepowodzeniem okresów powinna być co najmniej trzech lub więcej.

8. Wykresu metryki będą wyświetlane progi obliczony na podstawie ostatnich danych.

9. Kliknij przycisk **Gotowe**.

10. Wypełnij **szczegóły alertu** takich jak **Nazwa reguły alertu**, **opis**, i **ważność**.

11. Dodawanie grupy akcji alertu, przez wybranie istniejącej grupy akcji lub tworzenia nowej grupy akcji.

12. Kliknij przycisk **gotowe** można zapisać regułę alertu metryki.

> [!NOTE]
> Alert dotyczący metryki, zasady utworzone za pośrednictwem portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamicznymi progami na czas wykonywania żądania HTTP szczegółowych informacji w aplikacji

1. W [witryny Azure portal](https://portal.azure.com), kliknij pozycję **Monitor**. Widok monitora konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

2. Kliknij przycisk **alerty** kliknięcie **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów również ma **alerty** w ich menu zasobów w ramach **monitorowanie**, alerty można utworzyć, także z niego.

3. Kliknij przycisk **Wybieranie lokalizacji docelowej**, w okienku kontekstowym, który ładuje, zaznacz zasób docelowy, który ma zostać wyświetlony alert. Użyj **subskrypcji** i **typu zasobu "Application Insights"** list rozwijanych można znaleźć zasobu, który chcesz monitorować. Umożliwia także na pasku wyszukiwania można znaleźć zasobu.

4. Po wybraniu zasobu docelowego kliknij **Dodaj warunek**.

5. Wybierz **"Czas wykonywania żądania HTTP"**.

6. Opcjonalnie można dostosować metryki, dostosowując **okres** i **agregacji**. Zaleca się do użycia typ agregacji "Maximum" dla tego typu metryki, ponieważ jest mniej reprezentatywne zachowanie. "Maksymalna" progu statycznych typu agregacji może być więcej właściwe.

7. Zostanie wyświetlony wykres metryki przez ostatnie 6 godzin. Zdefiniuj parametry alertu:
    1. **Typ warunku** — wybierz opcję "Dynamic".
    1. **Operator** — wybierz pozycję "Większe niż", aby ograniczyć alerty wyzwalane na poprawę czas trwania.
    1. **Częstotliwość** — należy wziąć pod uwagę obniżenia oparte na działalność alertu.

8. Wykresu metryki będą wyświetlane progi obliczony na podstawie ostatnich danych.

9. Kliknij przycisk **Gotowe**.

10. Wypełnij **szczegóły alertu** takich jak **Nazwa reguły alertu**, **opis**, i **ważność**.

11. Dodawanie grupy akcji alertu, przez wybranie istniejącej grupy akcji lub tworzenia nowej grupy akcji.

12. Kliknij przycisk **gotowe** można zapisać regułę alertu metryki.

> [!NOTE]
> Alert dotyczący metryki, zasady utworzone za pośrednictwem portalu są tworzone w tej samej grupie zasobów co zasób docelowy.
