---
title: Rozwiązywanie problemów z wykresami metrycznymi usługi Azure Monitor
description: Rozwiązywanie problemów z tworzeniem, dostosowywaniem lub interpretowaniem wykresów metrycznych
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659666"
---
# <a name="troubleshooting-metrics-charts"></a>Rozwiązywanie problemów z wykresami metryk

Ten artykuł należy używać, jeśli występują problemy z tworzeniem, dostosowywania lub interpretowania wykresów w Eksploratorze metryk platformy Azure. Jeśli dopiero zaczynasz metryki, dowiedz się więcej o [rozpoczęciu pracy z Eksploratorem metryk](metrics-getting-started.md) i [zaawansowanymi funkcjami Eksploratora metryk.](metrics-charts.md) Można również zobaczyć [przykłady](metric-chart-samples.md) skonfigurowanych wykresów metrycznych.

## <a name="cant-find-your-resource-to-select-it"></a>Nie można znaleźć zasobu, aby go zaznaczyć

Po kliknięciu przycisku **Wybierz zasób** nie widzisz żądanego zasobu w oknie dialogowym selektora zasobów.

**Rozwiązanie:** Eksplorator metryk wymaga wybrania subskrypcji i grup zasobów przed wystawieniem dostępnych zasobów. Jeśli zasób nie jest widoczny:

1. Upewnij się, że wybrano poprawną subskrypcję na liście rozwijanej **Subskrypcja**. Jeśli subskrypcji nie ma na liście, kliknij pozycję **Katalog i ustawienia subskrypcji** i dodaj subskrypcję z odpowiednim zasobem.

1. Upewnij się, że wybrano właściwą grupę zasobów.
    > [!WARNING]
    > Aby zapewnić najlepszą wydajność, przy pierwszym otwarciu Eksploratora metryk na liście rozwijanej **Grupa zasobów** nie ma żadnych wstępnie wybranych grup zasobów. Aby wyświetlić jakiekolwiek zasoby, musisz wybrać co najmniej jedną grupę.

## <a name="chart-shows-no-data"></a>Wykres nie zawiera żadnych danych

Czasami wykresy mogą nie wyświetlać żadnych danych po wybraniu poprawnych zasobów i metryk. To zachowanie może być spowodowane przez kilka z następujących przyczyn:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Dostawca zasobów Microsoft.Insights nie jest zarejestrowany dla Twojej subskrypcji

Eksplorowanie metryk wymaga dostawcy zasobów *Microsoft.Insights* zarejestrowanego w Twojej subskrypcji. W wielu przypadkach jest on rejestrowany automatycznie (tj. po skonfigurowaniu reguły alertu, dostosowaniu ustawień diagnostyki dla dowolnego zasobu lub skonfigurowaniu reguły skalowania automatycznego). Jeśli dostawca zasobów microsoft.insights nie jest zarejestrowany, należy ręcznie zarejestrować go, wykonując kroki opisane w [dostawców zasobów platformy Azure i typów](../../azure-resource-manager/management/resource-providers-and-types.md).

**Rozwiązanie:** Otwórz **subskrypcje**, **kartę Dostawcy zasobów** i sprawdź, czy usługa *Microsoft.Insights* jest zarejestrowana dla subskrypcji.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nie masz wystarczających praw dostępu do zasobu

Na platformie Azure dostęp do metryk jest kontrolowany za pomocą [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Aby eksplorować metryki dla dowolnego zasobu, musisz być członkiem roli [Czytelnik monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Współautor monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) lub [Współautor](../../role-based-access-control/built-in-roles.md#contributor).

**Rozwiązanie:** Upewnij się, że masz wystarczające uprawnienia do zasobu, z którego eksplorujesz metryki.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Zasób nie emitował metryk w wybranym zakresie czasu

Niektóre zasoby nie emitują stale swoich metryk. Na przykład platforma Azure nie będzie zbierać metryk dla zatrzymanych maszyn wirtualnych. Inne zasoby mogą emitować swoje metryki tylko po wystąpieniu określonego warunku. Na przykład metryka pokazująca czas przetwarzania transakcji wymaga co najmniej jednej transakcji. Jeśli w wybranym zakresie czasu nie było żadnych transakcji, wykres naturalnie będzie pusty. Ponadto, chociaż większość metryk na platformie Azure jest zbierana co minutę, niektóre są zbierane rzadziej. Zobacz dokumentację metryki, aby uzyskać szczegółowe informacje na temat metryki, którą próbujesz zbadać.

**Rozwiązanie:** Zmień czas wykresu na szerszy zakres. Możesz rozpocząć od "Ostatnie 30 dni" przy użyciu większej szczegółowości czasu (lub polegania na opcji "Automatyczna szczegółowość czasu").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Wybrano zakres czasu większy niż 30 dni

[Większość metryk na platformie Azure jest przechowywana przez 93 dni](data-platform-metrics.md#retention-of-metrics). Jednak na dowolnym pojedynczym wykresie można wykonywać zapytanie o dane z nie więcej niż 30 dni. To ograniczenie nie ma zastosowania do [metryk opartych na dzienniku](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Rozwiązanie:** Jeśli widzisz pusty wykres lub wykres wyświetla tylko część danych metrycznych, sprawdź, czy różnica między datami początkowymi i końcowymi w selektorze czasu nie przekracza interwału 30-dniowego.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Wszystkie wartości metryk były poza zablokowanym zakresem osi y

[Blokując granice osi y wykresu](metrics-charts.md#lock-boundaries-of-chart-y-axis), możesz przypadkowo sprawić, że w obszarze wyświetlania wykresu nie będzie pokazywana linia wykresu. Na przykład jeśli oś y jest ograniczona do zakresu od 0 do 50%, a metryka ma stałą wartość 100%, linia wykresu jest zawsze renderowana poza widocznym obszarem, przez co wykres jest wyświetlany jako pusty.

**Rozwiązanie:** Sprawdź, czy granice osi y wykresu nie są zablokowane poza zakresem wartości metryk. Jeżeli granice osi y są zablokowane, warto tymczasowo je zresetować, aby upewnić się, że wartości metryki nie znajdują się poza zakresem wykresu. Blokowanie zakresu osi y nie jest zalecane w przypadku używania automatycznego poziomu szczegółowości dla wykresów z agregacją **suma**, **wartość minimalna** i **wartość maksymalna**, ponieważ ich wartości będą zmieniały się wraz z poziomem szczegółowości poprzez zmianę rozmiaru okna przeglądarki lub zmianę rozdzielczości ekranu. Przełączenie poziomu szczegółowości może spowodować pozostawienie pustego obszaru wyświetlania wykresu.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Patrzysz na metrykę systemu operacyjnego gościa, ale nie włączysz rozszerzenia diagnostyki platformy Azure

Kolekcja metryk **systemu operacyjnego gościa** wymaga skonfigurowania rozszerzenia Diagnostyki Azure lub włączenia go przy użyciu panelu **Ustawienia diagnostyczne** zasobu.

**Rozwiązanie:** Jeśli rozszerzenie diagnostyki platformy Azure jest włączone, ale nadal nie możesz zobaczyć swoich metryk, wykonaj kroki opisane w [przewodniku rozwiązywania problemów z rozszerzeniem diagnostyki azure.](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal) Zobacz też kroki rozwiązywania problemów [z nie można wybrać obszaru nazw systemu operacyjnego gościa i metryki](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Komunikat "Błąd podczas pobierania danych" na pulpicie nawigacyjnym

Ten problem może wystąpić, gdy pulpit nawigacyjny został utworzony z metryką, która później stała się przestarzała i została usunięta z platformy Azure. Aby sprawdzić, czy to ten przypadek, otwórz kartę **Metryki** zasobu i w selektorze metryk sprawdź dostępne metryki. Jeśli metryka nie jest wyświetlana, oznacza to, że została usunięta z platformy Azure. Gdy metryka jest przestarzała, zazwyczaj istnieje nowa, lepsza metryka, która zapewnia podobną perspektywę na kondycję zasobu.

**Rozwiązanie:** Zaktualizuj kafelek, który ustępuje, wybierając alternatywną metrykę wykresu na pulpicie nawigacyjnym. Możesz [przejrzeć listę dostępnych metryk dla usług platformy Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Wykres przedstawia linię przerywaną

Wykresy metryk platformy Azure używają stylu linii przerywanego, aby wskazać, że istnieje brakuna wartość (znana również jako "wartość null") między dwoma znanymi punktami danych ziarna czasu. Na przykład, jeśli w selektorze czasu wybrany "1 minuta" ziarnistość czasu, ale metryka została zgłoszona w 07:26, 07:27, 07:29 i 07:30 (uwaga minuta przerwy między drugim i trzecim punktem danych), a następnie linia przerywana połączy 07:27 i 07:29 i linia ciągła połączy wszystkich innych punktów danych. Linia przerywana spada do zera, gdy metryka używa **agregacji liczby** i **sum.** W przypadku **agregacji avg**, **min** lub **max** linia przerywana łączy dwa najbliższe znane punkty danych. Ponadto gdy brakuje danych z prawej lub lewej krawędzi wykresu, linia przerywana jest rozwijana w kierunku brakującego punktu danych.
  ![obraz metryki](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Rozwiązanie:** To zachowanie jest zgodnie z projektem. Jest to przydatne do identyfikowania brakujących punktów danych. Wykres liniowy jest doskonałym wyborem do wizualizacji trendów metryk o wysokiej gęstości, ale może być trudny do interpretacji dla metryk z rzadkimi wartościami, szczególnie gdy wartości corelating z ziarnem czasu są ważne. Linia przerywana ułatwia czytanie tych wykresów, ale jeśli wykres jest nadal niejasny, rozważ wyświetlanie metryk za pomocą innego typu wykresu. Na przykład wykres wykresu rozproszonego dla tej samej metryki wyraźnie pokazuje za każdym razem ziarno, wizualizując kropkę tylko wtedy, gdy istnieje wartość i całkowicie pomijając punkt danych, gdy brakuje wartości: ![obraz metryki](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Jeśli wciąż preferujesz wykres liniowy dla metryk, przesunięcie kursora myszy nad wykresem może pomóc ocenić stopień szczegółowości czasu przez wyróżnienie punktu danych w położeniu wskaźnika myszy.

## <a name="chart-shows-unexpected-drop-in-values"></a>Wykres pokazuje nieoczekiwany spadek wartości

W wielu przypadkach widoczny spadek wartości metryk jest spowodowany niezrozumieniem danych wyświetlanych na wykresie. Spadek sum lub liczb może wprowadzać w błąd, gdy na wykresie są wyświetlane ostatnie minuty, ponieważ ostatnie punkty danych metryk nie zostały jeszcze odebrane lub przetworzone przez platformę Azure. W zależności od usługi opóźnienie przetwarzania metryk może obejmować zakres kilku minut. W przypadku wykresów przedstawiających ostatni zakres czasu z 1- lub 5-minutową szczegółowością spadek wartości ![w ciągu ostatnich kilku minut staje się bardziej zauważalny: obraz metryczny](./media/metrics-troubleshoot/drop-in-values.png)

**Rozwiązanie:** To zachowanie jest zgodnie z projektem. Uważamy, że wyświetlanie danych natychmiast po ich otrzymaniu jest korzystne nawet wtedy, gdy dane są *częściowe* lub *niekompletne*. Pozwala to szybciej wyciągnąć ważne wnioski i od razu przyjrzeć się sprawie. Na przykład w przypadku metryki, która pokazuje liczbę błędów, wyświetlenie częściowej wartości X informuje o tym, że w danej minucie wystąpiło co najmniej X błędów. Dzięki temu można od razu przystąpić do badania problemu, zamiast czekać na wyświetlenie dokładnej liczby błędów, które wystąpiły w ciągu tej minuty, co może nie być istotne. Wykres zostanie zaktualizowany po otrzymaniu całego zestawu danych, ale w tym czasie mogą być również wyświetlane nowe, niekompletne punkty danych z kolejnych minut.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nie można wybrać obszaru nazw systemu operacyjnego gościa i metryk

Maszyny wirtualne i zestawy skalowania maszyny wirtualnej mają dwie kategorie metryk: metryki **hosta maszyn wirtualnych,** które są zbierane przez środowisko hostingu platformy Azure, oraz metryki **systemu operacyjnego gościa (klasyczne),** które są zbierane przez [agenta monitorowania](agents-overview.md) uruchomionego na maszynach wirtualnych. Aby zainstalować agenta monitorowania, należy włączyć [rozszerzenie Diagnostyki Azure](diagnostics-extension-overview.md).

Domyślnie metryki systemu operacyjnego gościa są przechowywane na koncie usługi Azure Storage, które wybrano na karcie **Ustawienia diagnostyczne** zasobu. Jeśli metryki systemu operacyjnego gościa nie są zbierane lub Eksplorator metryk nie można uzyskać do nich dostępu, widoczna jest tylko przestrzeń nazw metryki **hosta maszyny wirtualnej**:

![obraz metryki](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Rozwiązanie:** Jeśli nie widzisz obszaru nazw **systemu operacyjnego gościa (klasycznego)** i metryk w Eksploratorze metryk:

1. Upewnij się, że [rozszerzenie Diagnostyki Azure](diagnostics-extension-overview.md) jest włączone i skonfigurowane do zbierania metryk.
    > [!WARNING]
    > Nie można używać [agenta usługi Log Analytics](agents-overview.md#log-analytics-agent) (nazywanego również programem Microsoft Monitoring Agent lub programem MMA) do wysyłania **systemu operacyjnego gościa** na konto magazynu.

1. Upewnij się, że dostawca zasobów **microsoft.insights** jest [zarejestrowany dla subskrypcji](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Sprawdź, czy konto magazynu nie jest chronione przez zaporę. Witryna Azure Portal potrzebuje dostępu do konta magazynu na potrzeby pobierania danych metryk i rysowania wykresów.

1. Użyj [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), aby zweryfikować, że metryki przepływają do konta usługi Storage. Jeśli metryki nie są zbierane, postępuj zgodnie z [przewodnikiem rozwiązywania problemów z rozszerzeniem Diagnostyki Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o rozpoczynaniu pracy z Eksploratorem metryk](metrics-getting-started.md)
* [Dowiedz się więcej o zaawansowanych funkcjach Eksploratora metryk](metrics-charts.md)
* [Zobacz listę dostępnych metryk dla usług platformy Azure](metrics-supported.md)
* [Zobacz przykłady skonfigurowanych wykresów](metric-chart-samples.md)
