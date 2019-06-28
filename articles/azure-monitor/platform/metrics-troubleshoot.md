---
title: Rozwiązywanie problemów z wykresy metryk usługi Azure Monitor
description: Rozwiązywanie problemów z tworzenia, dostosowywania lub interpretowanie wykresy metryk
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 73ef5cc00b5154dbdbc92911d17740c7d13038ec
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341978"
---
# <a name="troubleshooting-metrics-charts"></a>Rozwiązywanie problemów z wykresów metryk

Skorzystaj z tego artykułu, jeśli wystąpią problemy związane z tworzenia, dostosowywania lub interpretacja wykresów w Eksploratorze metryk platformy Azure. Jeśli jesteś nowym użytkownikiem metryki, Dowiedz się więcej o [wprowadzenie do Eksploratora metryk](metrics-getting-started.md) i [zaawansowane funkcje Eksploratora metryk](metrics-charts.md). Można również wyświetlić [przykłady](metric-chart-samples.md) skonfigurowanego wykresów metryki.

## <a name="cant-find-your-resource-to-select-it"></a>Nie można odnaleźć zasobu w taki sposób, aby go zaznaczyć

Kliknięty **wybierz zasób** przycisk, ale nie widzisz zasobu w oknie dialogowym selektor zasobów.

**Rozwiązanie:** Eksplorator metryk wymaga przed listą dostępnych zasobów wybierz subskrypcji i grup zasobów. Jeśli nie widzisz zasobu:

1. Upewnij się, że wybrano poprawną subskrypcję w **subskrypcji** listy rozwijanej. Jeśli Twoja subskrypcja nie ma na liście, kliknij polecenie **katalog i Subskrypcja ustawienia** i Dodaj subskrypcję z zasobem usługi.

1. Upewnij się, że wybrano właściwy zasób grupy.
    > [!WARNING]
    > Aby uzyskać najlepszą wydajność, przy pierwszym otwarciu Eksploratora metryk **grupy zasobów** listy rozwijanej nie ma żadnych grup wstępnie wybranego zasobu. Należy wybrać co najmniej jedną grupę, aby zobaczyć wszystkie zasoby.

## <a name="chart-shows-no-data"></a>Wykres nie pokazuje żadnych danych

Czasami wykresy może wyświetlać żadnych danych po wybraniu poprawne zasobów i metryki. To zachowanie może być spowodowany kilka z następujących powodów:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Dostawca zasobów Microsoft.Insights nie jest zarejestrowany dla Twojej subskrypcji

Eksplorowanie metryk wymaga *Microsoft.Insights* dostawca zasobów zarejestrowany w ramach subskrypcji. W wielu przypadkach jest on zarejestrowany automatycznie (oznacza to, że po Konfiguracja reguły alertu, dostosowywanie ustawień diagnostycznych dla dowolnego zasobu lub skonfigurować reguły skalowania automatycznego). Jeśli dostawca zasobów Microsoft.Insights nie jest zarejestrowany, należy go ręcznie zarejestrować wykonując kroki opisane w [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/resource-manager-supported-services.md).

**Rozwiązanie:** Otwórz **subskrypcje**, **dostawców zasobów** kartę i sprawdź, czy *Microsoft.Insights* jest zarejestrowany dla Twojej subskrypcji.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nie masz wystarczających praw dostępu do zasobu

Na platformie Azure, dostęp do metryk jest kontrolowany przez [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Musisz być członkiem [Czytelnik monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Współautor monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor), lub [Współautor](../../role-based-access-control/built-in-roles.md#contributor) do eksplorowania metryk dla każdego zasobu.

**Rozwiązanie:** Upewnij się, że masz wystarczające uprawnienia do zasobów, z którego są eksplorowania metryk.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Zasób nie Emituj metryki w wybranym zakresie czasu

Niektóre zasoby nie Emituj stale ich metryki. Na przykład Azure nie będzie zbierać metryki dla zatrzymanych maszyn wirtualnych. Inne zasoby mogą emitować ich metryki, tylko wtedy, gdy wystąpi jakiś warunek. Na przykład metryka czas przetwarzania transakcji wymaga co najmniej jedna transakcja. Jeśli nie było żadnych transakcji w wybranym zakresie czasu, wykres naturalnie będzie pusta. Ponadto podczas gdy większość metryk na platformie Azure są gromadzone co minutę, istnieją pewne zebranych rzadziej. Zobacz dokumentację metryki, aby uzyskać więcej informacji na temat metrykę, którą próbujesz zapoznaj się z.

**Rozwiązanie:** Zmiana czasu wykresu do szerszego zakresu. Można rozpocząć od "W ciągu ostatnich 30 dni" przy użyciu większy stopień szczegółowości czasu (lub opierając się na opcję "stopień szczegółowości czasu automatycznego").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Wybrany zakres czasu większą niż 30 dni

[Większość metryk na platformie Azure są przechowywane przez 93 dni](data-platform-metrics.md#retention-of-metrics). Jednak można tworzyć tylko zapytania do nie więcej niż 30 dni danych o dowolnym pojedynczym wykresie. To ograniczenie nie ma zastosowania do [metryk opartych na dzienniku](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Rozwiązanie:** Jeśli zostanie wyświetlony pusty wykres lub wykres pokazuje tylko część danych metryk, sprawdź, że różnica między start - i daty w selektorze czasu nie przekracza rozmiaru interwale 30-dniowego.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Wszystkie wartości metryk był poza zakresem zablokowane osi y

Przez [blokowania granice osi y wykresu](metrics-charts.md#lock-boundaries-of-chart-y-axis), możesz przypadkowo wprowadzić obszaru wyświetlania wykresu pokazuje linii wykresu. Na przykład jeśli osi y jest ograniczona do zakresu od 0 do 50%, a metrykę ma stałą wartość 100%, wiersz jest zawsze renderowane poza obszaru, dzięki czemu na wykresie są wyświetlane jako puste.

**Rozwiązanie:** Upewnij się, że granice osi y wykresu nie są blokowane, poza zakresem wartości metryk. Jeżeli granice osi y są zablokowane, warto tymczasowo zmienione w celu upewnij się, że metryki, które wartości nie wykroczy poza zakres wykresu. Blokowanie zakres osi y nie jest zalecane z dokładnością automatyczne dla wykresów z **suma**, **min**, i **max** agregacji, ponieważ ich wartości zmieniają się z poziom szczegółowości, zmiana rozmiaru okna przeglądarki lub przesyłane z rozdzielczość ekranu co do innego. Poziom szczegółowości przełączania może opuścić obszaru wyświetlania pusty wykres.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Patrzysz metryki systemu operacyjnego gościa, ale nie zostały włączone rozszerzenie diagnostyki platformy Azure

Kolekcja **systemu operacyjnego gościa** metryki wymaga konfigurowania rozszerzenia diagnostyki platformy Azure lub włączanie go przy użyciu **ustawień diagnostycznych** panelu zasobu bazy danych.

**Rozwiązanie:** Jeśli włączono rozszerzenie diagnostyki platformy Azure, ale nadal nie możesz wyświetlić metryki, wykonaj czynności opisane w temacie [przewodnik rozwiązywania problemów rozszerzenia diagnostyki Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Zobacz też procedury rozwiązywania problemów z [nie można pobrać przestrzeni nazw systemu operacyjnego gościa i metryki](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Komunikat "Błąd podczas pobierania danych" na pulpicie nawigacyjnym

Ten problem może się zdarzyć, gdy pulpit nawigacyjny został utworzony za pomocą metryk, który później przestarzały i usunięte z systemu Azure. Aby sprawdzić, czy jest wymagane, otwórz **metryki** kartę zasobów i wyboru dostępne metryki w selektorze metryki. Jeśli Metryka nie jest wyświetlany, metryki zostało usunięte z systemu Azure. Gdy metryka jest przestarzała, są lepiej nową metrykę, udostępniająca podobne perspektywą kondycja zasobu.

**Rozwiązanie:** Zaktualizuj kafelka niepowodzenie pobrania alternatywnych metryk dla wykresu na pulpicie nawigacyjnym. Możesz [Przejrzyj listę rzeczy, dostępne metryki dla usług platformy Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Wykres pokazuje linii kreskowanej

Wykresy metryk platformy Azure umożliwia linię przerywaną oznacza, że brak wartości (znany także jako "wartość null") między ziarno czasu znanych dwa punkty danych. Na przykład, jeśli selektor czasu pobrania stopień szczegółowości czasu "1 minuta", ale Metryka został zgłoszony w 07:26, 07:27 07:29 i 07:30 (Uwaga: minuty przerwę między punktami danych drugą i trzecią), a następnie linię przerywaną, co połączy 07:27 i 07:29 i linię ciągłą będą się łączyć. inne punkty danych. Spadnie linia przerywana do zera, gdy Metryka używa **liczba** i **suma** agregacji. Dla **avg**, **min** lub **max** agregacji, linia przerywana łączy dwa punkty danych znane dokładnością. Ponadto gdy brakuje danych na prawej lub lewej krawędzi wykresu linia przerywana rozwija kierunek Brak punktu danych.
  ![metryki obrazu](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Rozwiązanie:** To zachowanie jest celowe. Jest to przydatne do identyfikowania brakujących punktów danych. Wykres liniowy przełożonego nadaje się do wizualizacji trendy metryki o wysokiej gęstości, ale może być trudne do interpretacji dla metryk, wartościami rozrzedzony, szczególnie w przypadku, gdy corelating wartościami ziarno czasu jest ważna. Linia przerywana ułatwia odczytu tych wykresów, ale jeśli wykres jest nadal niejasne, należy wziąć pod uwagę przeglądania metryk za pomocą innego typu wykresu. Na przykład powstać wykresu metryki tej samej wyraźnie pokazuje każdego ziarno czasu, przez tylko wizualizacja pojedynczego znaku kropki, po wartości i pomijanie dane punktu całkowicie Jeśli wartość to Brak: ![metryki obrazu](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Jeśli chcesz nadal wykres liniowy swoje metryki, przenosząc myszy nad wykresem może pomóc ocenić stopień szczegółowości czasu przez wyróżnianie punktu danych w lokalizacji wskaźnika myszy.

## <a name="chart-shows-unexpected-drop-in-values"></a>Wykres przedstawia nieoczekiwany spadek wartości

W wielu przypadkach postrzegany spadek wartości metryk jest nieporozumienia dane wyświetlane na wykresie. Można wprowadzani przez spadek sum lub liczby, gdy wykres pokazuje najnowszych minut, ponieważ ostatnim punktem danych metryk, które jeszcze nie zostały odebrane lub jeszcze przetworzone przez usługę Azure. W zależności od usługi opóźnienie przetwarzania metryki może być zakresu kilka minut. Wykresy wyświetlane ostatnie zakres czasu z dokładnością 1 lub 5-minutowych, listy wartości w ciągu ostatnich kilku minut staje się bardziej zauważalne: ![metryki obrazu](./media/metrics-troubleshoot/drop-in-values.png)

**Rozwiązanie:** To zachowanie jest celowe. Uważamy, że wyświetlane dane tak szybko, jak firma Microsoft otrzyma jest korzystne nawet wtedy, gdy dane są *częściowe* lub *niekompletne*. Dzięki temu można wcześniej zawarcia ważne i rozpocznij badanie natychmiast. Na przykład metryk, który pokazuje liczbę błędów, widzisz częściowe wartości X informuje, że istnieją co najmniej X błędów w danym minutę. Użytkownik może rozpocząć badanie problemu następnie od razu, zamiast czekać wyświetlić dokładną liczbę błędów, które wystąpiły w ciągu tej minuty, co może nie być ważne. Wykres zostanie zaktualizowany, gdy firma Microsoft otrzyma cały zestaw danych, ale w tym czasie mogą być również wyświetlane nowe punkty niekompletne dane z nowszą minut.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nie można pobrać przestrzeni nazw systemu operacyjnego gościa i metryki

Maszyny wirtualne i zestawy skalowania maszyn wirtualnych mają dwie kategorie metryki: **Host maszyny wirtualnej** metryki, które są zbierane przez środowiska hostingu platformy Azure i **systemu operacyjnego gościa** metryki, które są zbierane przez [agenta monitorowania](agents-overview.md) uruchomionej na maszynach wirtualnych. Zainstaluj agenta monitorowania, należy włączyć [rozszerzenie diagnostyki platformy Azure](diagnostics-extension-overview.md).

Domyślnie metryk systemu operacyjnego gościa są przechowywane na koncie Azure Storage, które można wybierać **ustawień diagnostycznych** kartę zasobu. Jeśli system operacyjny gościa metryki nie są zbierane lub Eksploratora metryk nie można uzyskać do nich dostęp, widoczne są tylko **hosta maszyny wirtualnej** metryki przestrzeni nazw:

![metryki obrazu](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Rozwiązanie:** Jeśli nie widzisz **systemu operacyjnego gościa** przestrzeni nazw i metryki w Eksploratorze metryk:

1. Upewnij się, że [rozszerzenie diagnostyki platformy Azure](diagnostics-extension-overview.md) jest włączone i skonfigurowane do zbierania metryk.
    > [!WARNING]
    > Nie można użyć [agenta usługi Log Analytics](agents-overview.md#log-analytics-agent) (nazywane również program Microsoft Monitoring Agent, lub "MMA") do wysyłania **systemu operacyjnego gościa** do konta magazynu.

1. Sprawdź, czy konto magazynu nie jest chroniony przez zaporę.

1. Użyj [Eksplorator usługi Azure storage](https://azure.microsoft.com/features/storage-explorer/) można zweryfikować, że metryki są przepływać do konta magazynu. Jeśli metryki nie są zbierane, postępuj zgodnie z [przewodnik rozwiązywania problemów rozszerzenia diagnostyki Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat rozpoczynania pracy z Eksploratora metryk](metrics-getting-started.md)
* [Dowiedz się więcej o zaawansowanych funkcji Eksploratora metryk](metrics-charts.md)
* [Wyświetlenie listy dostępnych metryk dla usług platformy Azure](metrics-supported.md)
* [Zobacz przykłady, skonfigurowanego wykresów](metric-chart-samples.md)
