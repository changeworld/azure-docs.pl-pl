---
title: Rozpoczynanie pracy z usługą Azure Monitor
description: Rozpocznij korzystanie z usługi Azure Monitor, aby uzyskać wgląd w operacje dotyczące zasobów i podejmować działania oparte na danych.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 70807db256f72b77bb29db3f6f59474a892f2939
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263365"
---
# <a name="get-started-with-azure-monitor"></a>Rozpoczynanie pracy z usługą Azure Monitor
Usługa Azure Monitor to usługa platformy, która zapewnia jedno źródło monitorowania zasobów systemu Azure. Z monitorem Azure można zwizualizować, zapytania, trasy, archiwum a w przeciwnym razie podjęcia działania w metryki i dzienników pochodzących z zasobami na platformie Azure. Możesz pracować z tego danych przy użyciu portalu Azure [poleceń cmdlet programu PowerShell Monitor](insights-powershell-samples.md), [interfejsu wiersza polecenia i Platform](insights-cli-samples.md), lub [interfejsów API REST Monitor Azure](https://msdn.microsoft.com/library/dn931943.aspx). Ten artykuł zawiera przewodnik po kilku głównych elementach usługi Azure Monitor obejmujący użycie portalu w celach demonstracyjnych.

## <a name="walkthrough"></a>Przewodnik
1. W portalu, przejdź do **wszystkie usługi** i Znajdź **Monitor** opcji. Kliknij ikonę gwiazdki, aby dodać tę opcję do listy ulubionych i zapewnić jej dostępność bezpośrednio z lewego paska nawigacyjnego.

    ![Opcja Monitor na liście usług](./media/monitoring-get-started/monitor-more-services.png)
2. Kliknij przycisk **Monitor** opcję, aby otworzyć **Monitor** strony. Ta strona zgromadzono wszystkich monitorowania ustawień i danych do jednego, skonsolidowanego widoku. Pierwszym otwarciu do **omówienie** sekcji. Przegląd zawiera pakiet zbiorczy alertów monitorowania, błędy i klasyfikatory kondycji usługi, które odnoszą się do zasobów w ramach subskrypcji.  

    ![Monitor nawigacji](./media/monitoring-get-started/monitor-blade-nav.png)

    Usługa Azure Monitor obejmuje trzy podstawowe kategorie danych monitorowania: **dziennik aktywności**, **metryki** i **dzienniki diagnostyczne**.
3. Kliknij opcję **Dziennik aktywności**, aby wyświetlić sekcję dziennika aktywności.

    [**Dziennik aktywności**](monitoring-overview-activity-logs.md) zawiera opis wszystkich operacji wykonywanych na zasobach objętych subskrypcją. Przy użyciu dziennika aktywności można określić zakres działań, autorów i czas dowolnych operacji tworzenia, aktualizowania lub usuwania dotyczących zasobów objętych subskrypcją. Dziennik aktywności zawiera na przykład informacje o tym, kiedy i przez kogo została zatrzymana aplikacja internetowej. Zdarzenia dziennika aktywności są przechowywane na platformie i dostępne do badania przez 90 dni.

    ![Dziennik aktywności](./media/monitoring-get-started/monitor-act-log-blade.png)

    Można utworzyć i zapisać zapytania dla wspólnych filtrów, a następnie przypiąć najważniejsze zapytania do pulpitu nawigacyjnego portalu, dzięki czemu zawsze będzie wiadomo, czy wystąpiły zdarzenia spełniające kryteria użytkownika.
4. Zastosuj filtr ograniczający widok do określonej grupy zasobów w ostatnim tygodniu, po czym kliknij pozycję **Zapisz**. Nadaj nazwę zapytania.

    ![Zapisywanie zapytania dotyczącego dziennika aktywności](./media/monitoring-get-started/monitor-act-log-save.png)
5. Następnie kliknij przycisk **Przypnij**.

    ![Kliknięcie opcji Przypnij dla dziennika aktywności](./media/monitoring-get-started/monitor-act-log-pin.png)

    Większość widoków w tym przewodniku można przypiąć do pulpitu nawigacyjnego. Pozwala to utworzyć pojedyncze źródło informacji dotyczących danych operacyjnych Twoich usług.
6. Wróć do pulpitu nawigacyjnego. Zapytanie (wraz z liczbą wyników) jest teraz wyświetlane na pulpicie nawigacyjnym. Jest to przydatne, jeśli chcesz szybko sprawdzić wszystkie akcje wysokiej jakości, które wystąpiły ostatnio w ramach subskrypcji, na przykład nowa rola została przypisana lub maszyna wirtualna została usunięta.

    ![Dzienniki aktywności przypięty do pulpitu nawigacyjnego](./media/monitoring-get-started/monitor-act-log-db.png)
7. Wróć do kafelka **Monitor** i kliknij sekcję **Metryki**. Należy najpierw wybrać zasób filtrowania i wybierając, korzystając z opcji listy rozwijanej w górnej części strony.

    ![Filtrowanie zasobu pod kątem metryk](./media/monitoring-get-started/monitor-met-filter.png)

    Wszystkie zasoby platformy Azure tworzą [**metryki**](monitoring-overview-metrics.md). Ten widok gromadzi wszystkie metryki na jednym ekranie, dzięki czemu można łatwo prześledzić działanie zasobów. Ponadto zapoznaj się z naszym marki [Nowa metryka wykresów środowisko](https://aka.ms/azuremonitor/new-metrics-charts) , klikając **metryki (wersja zapoznawcza)** kartę.
8. Po wybraniu zasobu w lewej części strony są wyświetlane wszystkie dostępne metryki. Można jednocześnie prezentować wiele metryk na wykresie, wybierając metryki i modyfikując zakres czasu i typ wykresu. Możesz również wyświetlić wszystkie alerty metryki ustawione dla danego zasobu.

    ![Blok metryki](./media/monitoring-get-started/monitor-metric-blade.png)

   > [!NOTE]
   > Niektóre metryki są dostępne tylko po włączeniu [usługi Application Insights](../application-insights/app-insights-overview.md) i/lub rozszerzenie systemu Windows lub Linux Azure Diagnostics na zasobie.
   >
   >

9. Jeśli wykres Ci odpowiada, możesz przypiąć go do pulpitu nawigacyjnego za pomocą przycisku **Przypnij**.
10. Wróć do **Monitor** i kliknij przycisk **dzienniki diagnostyczne**.

    ![Blok dzienników diagnostycznych](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Dzienniki diagnostyczne**](monitoring-overview-of-diagnostic-logs.md) to dzienniki tworzone *przez* zasób, które dostarczają dane o działaniu tego zasobu. Na przykład typami dzienników diagnostycznych są liczniki reguł grup zabezpieczeń sieci oraz dzienniki przepływu pracy aplikacji logiki. Te dzienniki mogą być przechowywane na koncie magazynu, przesyłane strumieniowo do usługi Event Hub i/lub wysyłane do usługi [Log Analytics](../log-analytics/log-analytics-overview.md). Usługa Log Analytics jest produktem firmy Microsoft służącym do analizy operacyjnej, który umożliwia zaawansowane wyszukiwanie oraz generowanie alertów.

    W portalu można wyświetlić i odfiltrować listę wszystkich zasobów objętych subskrypcją, aby ustalić, czy włączono dla nich tworzenie dzienników diagnostycznych.
    > [!NOTE]
    > Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
    >
    > *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
    >
    >

11. Kliknij zasób na stronie dzienników diagnostycznych. Jeśli dzienniki diagnostyczne są przechowywane na koncie magazynu, zostanie wyświetlona lista tworzonych co godzinę dzienników, które można bezpośrednio pobrać.

    ![Dzienniki diagnostyczne dla jednego zasobu](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Możesz również kliknąć opcję **Ustawienia diagnostyczne**, która pozwala skonfigurować lub zmodyfikować ustawienia archiwizacji na koncie magazynu, przesyłania strumieniowego do usługi Event Hubs lub wysyłania do obszaru roboczego usługi Log Analytics.

    ![Włączanie dzienników diagnostycznych](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Jeśli skonfigurowano wysyłanie dzienników diagnostycznych do usługi Log Analytics, można je przeszukiwać w sekcji **Wyszukiwanie w dziennikach** portalu.
12. Przejdź do **alertów (klasyczne)** sekcji strony monitora.

    ![publiczny blok alertów](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Tutaj można zarządzać wszystkimi [ **klasycznego alerty** ](monitoring-overview-alerts.md) na zasobów platformy Azure. Dotyczy to również alerty dotyczące metryk, zdarzenia dziennika aktywności testów sieci web usługi Application Insights (lokalizacja) i proaktywna Diagnostyka usługi Application Insights. Alerty nawiązać grupy akcji. [Grupy akcji](monitoring-action-groups.md) umożliwiają powiadamiania osób lub wykonywać określone czynności po zgłoszeniu alertu.

13. Kliknij pozycję **Dodaj alert dotyczący metryki**, aby utworzyć alert.

    ![dodawanie alertu dotyczącego metryki](./media/monitoring-get-started/monitor-alerts-add.png)

    Alert można następnie przypiąć do pulpitu nawigacyjnego, aby łatwiej śledzić jego stan w dowolnym momencie.

    Azure Monitor teraz ma również [ **nowszej alerty** ](https://aka.ms/azuremonitor/near-real-time-alerts) które może przyjąć częstotliwością wynoszącymi nawet co minutę.

14. Sekcja Monitor zawiera także linki do aplikacji usługi [Application Insights](../application-insights/app-insights-overview.md) oraz rozwiązań zarządzania usługi [Log Analytics](../log-analytics/log-analytics-overview.md). Te produkty firmy Microsoft są silnie zintegrowane z usługą Azure Monitor.
15. Jeśli nie korzystasz z usług Application Insights ani Log Analytics, możliwe jest, że usługa Azure Monitor współpracuje z aktualnie używanymi przez Ciebie produktami do monitorowania, rejestrowania i tworzenia alertów. Zobacz pełną listę produktów oraz instrukcje dotyczące sposobów integracji na naszej [stronie partnerów](monitoring-partners.md).

Wykonując poniższe kroki i przypinając odpowiednie kafelki do pulpitu nawigacyjnego, możesz tworzyć kompleksowe widoki aplikacji i infrastruktury, podobne do widoku poniżej:

![Pulpit nawigacyjny usługi Azure Monitor](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Kolejne kroki
* Odczyt [przegląd wszystkich Azure narzędzi monitorowania](monitoring-overview.md) zrozumieć, jak działa Azure Monitor z nimi.
