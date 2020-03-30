---
title: Rozwiązywanie problemów z wydajnością maszyny wirtualnej platformy Azure w systemie Linux lub Windows
description: W tym artykule opisano ogólne rozwiązywanie problemów z wydajnością maszyny wirtualnej (VM) za pomocą monitorowania i obserwowania wąskich gardeł oraz zapewnia możliwe korygowanie problemów, które mogą wystąpić.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772622"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Rozwiązywanie problemów z wydajnością maszyny wirtualnej platformy Azure w systemie Linux lub Windows

W tym artykule opisano ogólne rozwiązywanie problemów z wydajnością maszyny wirtualnej (VM) za pomocą monitorowania i obserwowania wąskich gardeł oraz zapewnia możliwe korygowanie problemów, które mogą wystąpić. Oprócz monitorowania, można również użyć Perfinsights, które mogą dostarczyć raport z najlepszych rozwiązań zalecenia i kluczowych wąskich gardeł wokół we/wy/CPU/memory. Perfinsights jest dostępny zarówno dla maszyn wirtualnych [z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) i [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) na platformie Azure.

W tym artykule będzie przejść za pomocą monitorowania do diagnozowania wąskich gardeł wydajności.

## <a name="enabling-monitoring"></a>Włączanie monitorowania

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitorowanie maszyny wirtualnej usługi Azure IAAS

Aby monitorować maszynę wirtualną gościa, należy użyć monitorowania maszyny Wirtualnej platformy Azure, która będzie ostrzegać o pewnych warunkach zasobów wysokiego poziomu. Aby sprawdzić, czy masz włączoną diagnostykę maszyn wirtualnych, zobacz [omówienie dzienników zasobów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs). Jeśli widzisz następujące, to najprawdopodobniej nie masz włączonej diagnostyki:

![Monitorowanie nie jest włączone](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Włączanie diagnostyki maszyn wirtualnych za pośrednictwem witryny Microsoft Azure portal

Aby włączyć diagnostykę maszyn wirtualnych:

1. Przejdź do maszyny Wirtualnej
2. Kliknij **pozycję Ustawienia diagnostyki**
3. Wybierz konto magazynu i kliknij pozycję **Włącz monitorowanie na poziomie gościa**.

   ![Kliknij pozycję Ustawienia, a następnie pozycję Diagnostyka](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Konto magazynu używane do diagnostyki można sprawdzić na karcie **Agent** w obszarze **Ustawienia diagnostyki**.

![Sprawdź konto magazynu](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Włączanie diagnostyki konta magazynu za pośrednictwem witryny Azure Portal

Magazyn jest bardzo ważną warstwą, gdy zamierzamy analizować wydajność we/wy dla maszyny wirtualnej na platformie Azure. W przypadku metryk związanych z magazynowaniem musimy włączyć diagnostykę jako dodatkowy krok. Można to również włączyć, jeśli chcemy tylko analizować liczniki związane z magazynowaniem.

1. Określ, którego konta magazynu (lub kont) maszyna wirtualna używa, wybierając maszynę wirtualną. Kliknij **pozycję Ustawienia**, a następnie kliknij pozycję **Dyski**:

   ![Kliknij pozycję Ustawienia, a następnie pozycję Dyski](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. W portalu przejdź do konta magazynu (lub kont) maszyny wirtualnej i przejmuj się następującymi krokami:

   1. Kliknij omówienie konta magazynu, które znalazłeś w kroku powyżej.
   2. Wyświetlane będą domyślne metryki. 

    ![Domyślne metryki](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Kliknij dowolną z metryk, która pokaże inny blok z więcej opcji, aby skonfigurować i dodać metryki.

   ![Dodawanie danych](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Aby skonfigurować te opcje:

1.  Wybierz pozycję **Metryki**.
2.  Wybierz **zasób** (konto magazynu).
3.  Wybierz **obszar nazw**
4.  Wybierz **metryczny**.
5.  Wybierz typ **agregacji**
6.  Ten widok można przypiąć na pulpicie nawigacyjnym.

## <a name="observing-bottlenecks"></a>Obserwacja wąskich gardeł

Gdy przejdziemy przez proces wstępnej konfiguracji potrzebnych metryk i opublikujemy włączenie diagnostyki maszyny Wirtualnej i powiązanego konta magazynu, możemy przejść do fazy analizy.

### <a name="accessing-the-monitoring"></a>Dostęp do monitoringu

Wybierz maszynę wirtualną platformy Azure, którą chcesz zbadać, i wybierz pozycję **Monitorowanie**.

![Wybierz monitorowanie](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Terminy obserwacji

Aby zidentyfikować, jeśli masz jakieś wąskie gardła zasobów, przejrzyj dane. Jeśli okaże się, że komputer działa poprawnie, ale zostało zgłoszone, że wydajność została niedawno obniżona, przejrzyj zakres czasu danych, który obejmuje dane metryki wydajności przed zgłoszoną zmianą, w trakcie i po problemie.

### <a name="check-for-cpu-bottleneck"></a>Sprawdź wąskie gardło procesora

![Sprawdź wąskie gardło procesora](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Edytuj wykres.
2. Ustaw zakres czasu.
3. Następnie należy dodać w liczniku: CPU Procent gościa OS
4. Zapisz zmiany.

### <a name="cpu-observe-trends"></a>Cpu obserwować trendy

Patrząc na problemy z wydajnością, należy pamiętać o trendach i zrozumieć, czy mają one wpływ na Ciebie. W następnych sekcjach użyjemy wykresów monitorowania z portalu, aby wyświetlić trendy. Mogą być również przydatne do odwoływania się do zachowania zasobów różnicy w tym samym okresie. Aby dostosować wykresy, kliknij pozycję [Platforma danych Usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Wybijanie — wybijanie może być związane z zaplanowanym zadaniem/znanym zdarzeniem. Jeśli można zidentyfikować zadanie, należy określić, czy zadanie jest uruchamiane na wymaganym poziomie wydajności. Jeśli wydajność jest dopuszczalna, może nie być konieczne zwiększenie zasobów.

Spike up i Stała — często wskazuje nowe obciążenie. Jeśli nie jest to rozpoznane obciążenie, włącz monitorowanie na maszynie Wirtualnej, aby dowiedzieć się, jaki proces (lub procesy) powoduje zachowanie. Po rozpoznaniu procesu należy określić, czy zwiększone zużycie jest spowodowane przez nieefektywny kod, czy normalne zużycie. Jeśli zużycie normalne, zdecydować, czy proces działa na wymaganym poziomie wydajności.

Stała — określ, czy maszyna wirtualna zawsze działała na tym poziomie, czy tylko na tym poziomie, ponieważ diagnostyka została włączona. Jeśli tak, zidentyfikuj proces (lub procesy) powodujący problem i rozważ dodanie większej liczby tego zasobu.

Stale rośnie — stały wzrost zużycia jest często nieefektywny kod lub proces przy większym obciążeniu użytkownika.

### <a name="high-cpu-utilization-remediation"></a>Środki korygowania wykorzystania procesora CPU

Jeśli aplikacja lub proces nie działa na odpowiednim poziomie wydajności i widzisz 95% + stała wykorzystania procesora CPU, można wykonać jedną z następujących czynności:

* Dla natychmiastowej ulgi - Zwiększ rozmiar maszyny Wirtualnej do rozmiaru z większą liczą rdzeni
* Zrozumieć problem – zlokalizować aplikację / proces i rozwiązać odpowiednio.

Jeśli została zwiększona maszyna wirtualna, a procesor jest nadal uruchomiony 95%, należy określić, czy to ustawienie oferuje lepszą wydajność lub wyższą przepływność aplikacji do dopuszczalnego poziomu. Jeśli nie, rozwiąż problem z poszczególnymi\procesem aplikacji.

Można użyć perfinsights dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) do analizy, który proces jest motorem zużycia procesora CPU. 

## <a name="check-for-memory-bottleneck"></a>Sprawdzanie wąskiego gardła pamięci

Aby wyświetlić dane:

1. Dodaj sekcję.
2. Dodaj kafelek.
3. Otwórz galerię.
4. Wybierz użycie pamięci i przeciągnij. Gdy kafelek jest zadokowany, kliknij prawym przyciskiem myszy i wybierz **6x4**.

### <a name="memory-observe-trends"></a>Pamięć obserwuje trendy

Użycie pamięci pokazuje, ile pamięci jest zużywane z maszyną wirtualną. Zrozumieć trend i czy mapuje do czasu, w którym widzisz problemy. Zawsze powinno być więcej niż 100 MB dostępnej pamięci.

Spike i Constant/Constant Steady Consumption — wysokie wykorzystanie pamięci może nie być przyczyną złej wydajności, ponieważ niektóre aplikacje, takie jak relacyjna liczba wyszukiwarek baz danych, przydzielają dużą ilość pamięci, a to wykorzystanie może nie być znaczące. Jednak jeśli istnieje wiele aplikacji wymagających pamięci, może wystąpić niska wydajność z rywalizacji pamięci powodując przycinanie i stronicowania/zamiany na dysku. Ta niska wydajność jest często zauważalną przyczyną wpływu na wydajność aplikacji.

Stale rosnące zużycie - możliwe aplikacji "rozgrzewka", zużycie to jest powszechne wśród silników baz danych uruchomienie. Jednak może to również być oznaką przecieku pamięci w aplikacji. Zidentyfikuj aplikację i zrozum, czy oczekiwane jest zachowanie.

Użycie strony lub wymiany pliku — sprawdź, czy używasz pliku\) stronicowania systemu Windows `/dev/sdb`(znajdującego się na D: lub Linux Swap file (znajdującego się na) są intensywnie używane. Jeśli nie masz nic na tych woluminach z wyjątkiem tych plików, sprawdź, czy na tych dyskach nie ma wysokich odczytów/zapisów. Ten problem wskazuje na warunki małej pamięci.

### <a name="high-memory-utilization-remediation"></a>Korygowanie wykorzystania pamięci o wysokim poziomie wykorzystania

Aby rozwiązać problem wysokiego wykorzystania pamięci, wykonaj dowolne z następujących zadań:

* Dla natychmiastowej ulgi lub strony lub wymiany użycia pliku — zwiększyć rozmiar maszyny Wirtualnej do jednego z większą ilość pamięci, a następnie monitor.
* Zrozumieć problem — zlokalizować aplikacje/proces i rozwiązywać problemy w celu zidentyfikowania aplikacji pamięci o wysokiej zużywają.
* Jeśli znasz aplikację, sprawdź, czy alokacja pamięci może być ograniczona.

Jeśli po uaktualnieniu do większej maszyny Wirtualnej, można odkryć, że nadal masz stały wzrost do 100%, zidentyfikować aplikację/proces i rozwiązywania problemów.

Za pomocą programu Perfinsights dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) można analizować, który proces jest przyczyną zużycia pamięci. 

## <a name="check-for-disk-bottleneck"></a>Sprawdź wąskie gardło dysku

Aby sprawdzić podsystem magazynu dla maszyny Wirtualnej, sprawdź diagnostyki na poziomie maszyny Wirtualnej platformy Azure przy użyciu liczników w diagnostyki maszyn wirtualnych, a także diagnostyki konta magazynu.

W przypadku rozwiązywania problemów związanych z maszynami wirtualnymi można użyć perfinsights dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux), co może pomóc w analizie, który proces napędza we/wy. 

Należy pamiętać, że nie mamy liczników dla kont magazynu strefowego nadmiarowego i magazynu w wersji premium. W przypadku problemów związanych z tymi licznikami, podnieś sprawę pomocy technicznej.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Wyświetlanie diagnostyki konta magazynu w monitorowaniu

Aby pracować nad poniższymi elementami, przejdź do konta magazynu maszyny Wirtualnej w portalu:

![Wyświetlanie diagnostyki konta magazynu w monitorowaniu](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Edytuj wykres monitorowania.
2. Ustaw zakres czasu.
3. Dodaj liczniki opisane w poniższych krokach.
4. Zapisz zmiany.

### <a name="disk-observe-trends-standard-storage-only"></a>Dysk obserwuje trendy (tylko standardowa pamięć masowa)

Aby zidentyfikować problemy z magazynem, spójrz na metryki wydajności z diagnostyki konta magazynu i diagnostyki maszyny Wirtualnej.

Dla każdego wyboru poniżej poszukaj kluczowych trendów, gdy występują problemy w zakresie czasowym problemu.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Sprawdź dostępność magazynu platformy Azure — dodaj metrykę konta magazynu: dostępność

Jeśli widzisz spadek dostępności, może to być problem z platformą, sprawdź [stan platformy Azure](https://azure.microsoft.com/status/). Jeśli nie jest tam wyświetlany żaden problem, należy zgłosić nowe żądanie pomocy technicznej.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Sprawdź limit czasu magazynu platformy Azure — dodaj metryki konta magazynu:

* KlientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Wartości w metrykach *TimeOutError wskazują, że operacja we/wy trwała zbyt długo i przesunął limit czasu. Praca nad kolejnymi krokami pomoże zidentyfikować potencjalne przyczyny.

AverageServerLatency zwiększa się w tym samym czasie w TimeOutErrors może być problem platformy. W tej sytuacji zgłosić nowe żądanie pomocy technicznej.

AverageE2ELatency reprezentuje opóźnienie klienta. Sprawdź, jak usługi We/Wy są wykonywane przez aplikację. Poszukaj wzrostu lub stale wysokie metryki TotalRequests. Ta metryka reprezentuje we/wy. Jeśli zaczynasz trafić limity konta magazynu lub pojedynczego dysku VHD, opóźnienie może być związane z ograniczaniem przepustowości.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Sprawdź, czy ograniczanie przepustowości magazynu platformy Azure — dodawanie metryk konta magazynu: ThrottlingError

Wartości ograniczania przepustowości wskazują, że jesteś ograniczany na poziomie konta magazynu, co oznacza, że trafienie w limit we/wy na koncie. Możesz określić, czy przekraczasz próg We/Wy, sprawdzając metrykę **TotalRequests**.

Należy zauważyć, że każdy dysk VHD ma limit 500 IOPS lub 60 MBit, ale jest związany skumulowanym limitem 20000 IOPS na konto magazynu.

Za pomocą tej metryki nie można stwierdzić, który obiekt blob powoduje ograniczanie przepustowości i które są dotknięte przez niego. Jednak jesteś albo trafienie we/wyz.

Aby określić, czy przekraczasz limit we/wy, przejdź do diagnostyki konta magazynu i sprawdź TotalRequests, aby sprawdzić, czy zbliżasz się do 20 tysięcy TotalRequests. Zidentyfikuj zmianę wzorca, czy widzisz limit po raz pierwszy, czy ten limit występuje w określonym czasie.

W przypadku nowych ofert dysków w obszarze Magazyn standardowy limity we/wy i przepływność mogą się różnić, ale łączny limit konta magazynu standardowego wynosi 20000 we/wy (magazyn w wersji Premium ma różne limity na poziomie konta lub dysku). Dowiedz się więcej o różnych standardowych ofertach dysków do magazynowania i na limity dysków:

* [Cele dotyczące skalowalności i wydajności dysków maszyn wirtualnych w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

#### <a name="references"></a>Dokumentacja

* [Cele skalowalności i wydajności dla kont magazynu obiektów blob strony premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)

Przepustowość konta magazynu jest mierzona za pomocą metryk konta magazynu: TotalIngress i TotalEgress. Masz różne progi przepustowości w zależności od typu nadmiarowości i regionów.

* [Cele dotyczące skalowalności i wydajności dla standardowych kont magazynu](../../storage/common/scalability-targets-standard-account.md)

Sprawdź TotalIngress i TotalEgress względem limitów transferu danych przychodzących i wychodzących dla typu nadmiarowości konta magazynu i regionu.

Sprawdź limity przepływności veds dołączone do maszyny Wirtualnej. Dodaj odczyt i zapis dysku metryki maszyny Wirtualnej.

Nowe oferty dysków w magazynie standardowym mają różne limity we/wy i przepustowość (Usługi We/Wy nie są udostępniane na dysk VHD). Spójrz na dane, aby zobaczyć, czy jesteś osiągnięcie limitów połączonej przepływności MB vHD (s) na poziomie maszyny Wirtualnej przy użyciu odczytu i zapisu dysku, a następnie zoptymalizować konfigurację magazynu maszyn wirtualnych do skalowania przeszłości pojedyncze limity VHD. Dowiedz się więcej o różnych standardowych ofertach dysków do magazynowania i na limity dysków:

* [Cele dotyczące skalowalności i wydajności dysków maszyn wirtualnych w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

### <a name="high-disk-utilizationlatency-remediation"></a>Duże wykorzystanie dysku/korygowanie opóźnienia

Zmniejsz opóźnienie klienta i zo zo maszyny Wirtualnej do skalowania w przeszywu poza limity VHD

* [Optymalizacja we/wy dla systemu Windows na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Optymalizacja we/wy dla systemu Linux na platformie Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Ograniczenie ograniczania przepustowości

Jeśli trafisz górne limity kont magazynu, ponownie zrównoważyć veds między kontami magazynu. Zapoznaj się z [obiektami docelowymi skalowalności i wydajności usługi Azure Storage](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Zwiększanie przepustowości i zmniejszanie opóźnień

Jeśli masz aplikację z uwzględnieniem opóźnień i wymagasz dużej przepływności, należy przeprowadzić migrację dysków VHD do magazynu w usłudze Azure w ramach usługi Azure Premium przy użyciu maszyny wirtualnej z serii DS i GS.

W tych artykułach omówiono konkretne scenariusze:

* [Migracja do usługi Azure Premium Storage](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Korzystanie z usługi Azure Premium Storage z programem SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, skontaktuj się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie złóż błąd pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
