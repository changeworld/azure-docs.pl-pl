---
title: Usługi Azure Site Recovery wdrożenia Planistę dla funkcji Hyper-V-do Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano analizy wygenerowanego raportu za pomocą usługi Azure lokacji odzyskiwania wdrożenia Planistę dla funkcji Hyper-V do platformy Azure scenariusza.
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: 060d51406f67ad8a55cdf61506cd66f5390ebe4c
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29467212"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analizowanie raportu planowania usługi Azure Site Recovery wdrożenia
W tym artykule omówiono arkusze zawarty w raporcie programu Excel, generowane przez Azure lokacji odzyskiwania wdrożenia Planistę dla funkcji Hyper-V do platformy Azure scenariusza.

## <a name="on-premises-summary"></a>Podsumowanie środowiska lokalnego
Arkusz podsumowania lokalnymi omówienie PROFILOWANEGO środowisku funkcji Hyper-V.

![Podsumowanie środowiska lokalnego](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Data rozpoczęcia** i **Data zakończenia**: daty rozpoczęcia i zakończenia danych profilowania brany pod uwagę podczas generowania raportu. Domyślnie data rozpoczęcia to data rozpoczęcia profilowania, a data zakończenia to data zatrzymania profilowania. Te informacje mogą być wartości "Data_rozpoczęcia" i "EndDate", jeśli generowany jest raport z tych parametrów.

**Łączna liczba dni profilowania**: całkowita liczba dni profilowania od daty rozpoczęcia do daty zakończenia, dla których jest generowany raport.

**Liczba maszyn wirtualnych zgodne**: Całkowita liczba zgodne maszyn wirtualnych, dla których wymagane przepustowości, wymaganą liczbę kont magazynu i rdzeni Azure mają być obliczane.

**Łączna liczba dysków na wszystkich zgodnych maszynach wirtualnych**: całkowita liczba dysków wszystkich zgodnych maszyn wirtualnych.

**Średnia liczba dysków na zgodną maszynę wirtualną**: średnia liczba dysków obliczana dla wszystkich zgodnych maszyn wirtualnych.

**Średni rozmiar dysku (GB)**: średni rozmiar dysku obliczany dla wszystkich zgodnych maszyn wirtualnych.

**Żądany cel punktu odzyskiwania (w minutach)**: Przywracanie domyślnego punktu cel lub przekazana dla parametru "DesiredRPO" w czasie generowania raportu, aby oszacować przepustowość wymagana wartość.

**Wymaganą przepustowość (MB/s)**: Wartość przekazana dla parametru "Przepustowości" w czasie generowania raportu, aby oszacować cel punktu osiągalne odzyskiwania (RPO).

**Zaobserwowany dzienny typowy współczynnik zmian danych (GB)**: średni współczynnik zmian danych zaobserwowany we wszystkie dni profilowania.

## <a name="recommendations"></a>Zalecenia 
Arkusz Zalecenia raportu dotyczącego replikacji z funkcji Hyper-V do platformy Azure zawiera następujące szczegółowe informacje zgodnie z wybranym żądanym celem punktu odzyskiwania:

![Zalecenia z raportu replikacji z funkcji Hyper-V do platformy Azure](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profilowanie danych
![Profilowanie danych](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Okres profilowanych danych**: okres, podczas którego działało profilowanie. Domyślnie w obliczeniach narzędzie uwzględnia wszystkie profilowane dane. Jeśli używana jest opcja datą rozpoczęcia i datą zakończenia podczas generowania raportu, generuje raport w określonym okresie. 

**Liczba serwerów funkcji Hyper-V profilowane**: liczba serwerów funkcji Hyper-V jest generowany raport którego maszyn wirtualnych. Wybierz numer, aby wyświetlić nazwy serwerów funkcji Hyper-V. Aby wyświetlić wszystkie serwery wraz z ich wymagania dotyczące magazynu zostanie otwarty arkusz wymagania magazynu lokalnego. 

**Żądany cel punktu odzyskiwania**: cel punktu odzyskiwania dla danego wdrożenia. Domyślnie wymagana przepustowość sieci jest obliczana dla wartości celu punktu odzyskiwania równych 15, 30 i 60 minut. Odpowiednie wartości są aktualizowane w arkuszu zgodnie z wybraną wartością. Jeśli parametr DesiredRPOinMin jest używany podczas generowania raportu, ta wartość jest wyświetlana w wyniku żądany cel punktu odzyskiwania.

### <a name="profiling-overview"></a>Omówienie profilowania
![Omówienie profilowania](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Łączna liczba profilowanych maszyn wirtualnych**: całkowita liczba maszyn wirtualnych, których profilowane dane są dostępne. Jeśli VMListFile ma nazw żadnych maszyn wirtualnych, które nie zostały profilowaniu, te maszyny wirtualne nie są wliczane generowania raportu i są wykluczone z profilowanego łączna liczba maszyn wirtualnych.

**Zgodne maszyny wirtualne**: liczba maszyn wirtualnych, które mogą być chronione na platformie Azure przy użyciu usługi Azure Site Recovery. Jest całkowita liczba zgodne maszyn wirtualnych, dla których wymagane przepustowości, liczba kont magazynu i liczby rdzeni Azure mają być obliczane. Szczegóły wszystkich maszyn wirtualnych są dostępne w sekcji „Zgodne maszyny wirtualne”.

**Niezgodne maszyny wirtualne**: liczba profilowanych maszyn wirtualnych, które nie są zgodne na potrzeby ochrony za pomocą usługi Site Recovery. Przyczyny niezgodności wymieniono w sekcji „Niezgodne maszyny wirtualne”. Jeśli VMListFile ma nazw żadnych maszyn wirtualnych, które nie zostały profilowane, te maszyny wirtualne są wykluczone z niezgodną liczbę maszyn wirtualnych. Dla tych maszyn wirtualnych jest wyświetlany tekst „Nie znaleziono danych” na końcu sekcji „Niezgodne maszyny wirtualne”.

**Żądany cel punktu odzyskiwania**: żądany cel punktu odzyskiwania w minutach. Raport jest generowany dla trzech wartości celu punktu odzyskiwania: 15 (ustawienie domyślne), 30 i 60 minut. Zalecenie przepustowości w raporcie zostanie zmieniona w zależności od opcji wybranej w **żądany cel punktu odzyskiwania** listy rozwijanej w prawym górnym rogu arkusza. Jeśli raport jest generowany przy użyciu parametru - DesiredRPO przy użyciu niestandardowej wartości, to Niestandardowa wartość zawiera jako domyślnego w **żądany cel punktu odzyskiwania** listy rozwijanej.

### <a name="required-network-bandwidth-mbps"></a>Wymagana przepustowość sieci (Mb/s)
![Wymagana przepustowość sieci](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Aby spełnić RPO 100% czasu**: zalecane przepustowości w MB/s do przydzielenia spełnia Twoje żądany cel punktu odzyskiwania 100 procent czasu. Taka przepustowość musi zostać przeznaczona na stałą replikację przyrostową wszystkich zgodnych maszyn wirtualnych, aby uniknąć naruszeń celu punktu odzyskiwania.

**Aby spełnić RPO 90% czasu**: prawdopodobnie z powodu cenową połączenia szerokopasmowego lub innego powodu nie można ustawić przepustowości potrzebnych do spełnienia 100 procent czasu Twojej żądany cel punktu odzyskiwania. Jeśli jest to możliwe, można użyć na niższy przepustowości, który jest zgodny z żądany cel punktu odzyskiwania 90 procent czasu. Aby umożliwić poznanie skutków ustawienia mniejszej przepustowości, w raporcie przedstawiono analizę warunkową liczby i czasu trwania naruszeń celu punktu odzyskiwania.

**Uzyskuje przepływności**: przepływności na serwerze, na którym uruchomiono polecenie GetThroughput do regionu Azure, w którym znajduje się na koncie magazynu. Liczba przepływności wskazuje Szacowany poziom, który można uzyskać w przypadku ochrony zgodne maszyn wirtualnych przy użyciu usługi Site Recovery. Właściwości magazynu i sieci serwera funkcji Hyper-V musi być taki sam jak serwer, z którym jest uruchomione narzędzie.

W przypadku wszystkich wdrożeń usługi Site Recovery w przedsiębiorstwach zalecamy użycie usługi [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Wymagane konta magazynu
Ten wykres przedstawia łączną liczbę kont magazynu (w warstwach Standardowa i Premium) wymaganych do ochrony wszystkich zgodnych maszyn wirtualnych. Aby dowiedzieć się, którego konta magazynu używać dla poszczególnych maszyn wirtualnych, zobacz sekcję „Rozmieszczenie maszyny wirtualnej względem magazynu”.

![Wymagane konta magazynu platformy Azure](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Wymagana liczba rdzeni platformy Azure
Wynik to łączna liczba rdzeni do skonfigurowania przed rozpoczęciem pracy w trybie failover lub testem pracy w trybie failover dla wszystkich zgodnych maszyn wirtualnych. Jeśli liczba rdzeni w ramach subskrypcji jest zbyt mała, usługa Site Recovery nie może utworzyć maszyn wirtualnych w czasie pracy w trybie failover lub testu pracy w trybie failover.

![Wymagana liczba rdzeni platformy Azure](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Wymagania dotyczące dodatkowego magazynu lokalnego

Całkowita ilość wolnego miejsca wymagane przez serwery funkcji Hyper-V zakończona pomyślnie Replikacja początkowa i replikacja różnicowa, aby upewnić się, że replikacja maszyny Wirtualnej nie powoduje żadnych niepożądanych przestojów w przypadku aplikacji produkcyjnych. Więcej informacji na temat poszczególnych wymagań wolumin jest dostępny w [wymagania magazynu lokalnego](#on-premises-storage-requirement). 

Aby dowiedzieć się, dlaczego ilość wolnego miejsca jest wymagane dla replikacji, zobacz [wymagania magazynu lokalnego](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) sekcji.

![Częstotliwość wymaganie i skopiuj magazynu lokalnego](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maksymalna częstotliwość kopiowania
Maksymalną zalecaną częstotliwość kopiowania należy ustawić, aby osiągać żądany cel punktu odzyskiwania. Domyślna to pięć minut. Częstotliwość kopiowania do 30 sekund w celu osiągnięcia lepszy cel punktu odzyskiwania.

### <a name="what-if-analysis"></a>Analiza warunkowa
![Co analizy Jeśli](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) analiza przedstawia liczbę naruszeń mogą wystąpić podczas profilowania okres, po ustawieniu mniejszej przepustowości sieci, aby uzyskać żądany cel punktu odzyskiwania należy spełnić tylko 90 procent czasu. W danym dniu może wystąpić co najmniej jedno naruszenie. Wykres przedstawia szczyt celu punktu odzyskiwania w danym dniu. Na podstawie tej analizy można zdecydować, czy liczba naruszeń celu punktu odzyskiwania we wszystkich dniach i szczytowa wartość celu punktu odzyskiwania na dzień jest dopuszczalna przy określonej mniejszej przepustowości. W przypadku dopuszczalne można przydzielić mniejszej przepustowości sieci do replikacji. W przypadku nadmiernego przydzielić większą przepustowość zgodnie z sugestią podaną w celu spełnienia 100 procent czasu żądany cel punktu odzyskiwania. 

### <a name="recommendation-for-successful-initial-replication"></a>Zalecenie dotyczące pomyślnej replikacji początkowej
W tej sekcji omówiono liczba partii, w których mają być chronione maszyny wirtualne i minimalnej przepustowości, wymaganych do replikacji początkowej (IR) zakończyła się pomyślnie. 

![Dzielenie replikacji początkowej na partie](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

Maszyny wirtualne muszą być chronione w kolejności danej partii. Każdej z partii ma listę maszyn wirtualnych. Maszyny wirtualne partii 1 muszą być chronione przed maszyn wirtualnych 2 partii. Wsadowe 2 maszyny wirtualne muszą być chronione przed maszynami wirtualnymi 3 partii i tak dalej. Po zakończeniu replikacji początkowej maszyn wirtualnych 1 partii, można włączyć replikację dla maszyn wirtualnych 2 partii. Podobnie po zakończeniu replikacji początkowej maszyn wirtualnych 2 partii, można włączyć replikację dla maszyn wirtualnych 3 partii i tak dalej. 

Jeśli kolejność partii nie występuje, wystarczającą przepustowość dla replikacji początkowej nie może być dostępna dla maszyn wirtualnych, które są chronione później. Wynik jest, że albo maszyn wirtualnych nigdy nie zakończenie początkowej replikacji lub kilka chronionych maszyn wirtualnych może Przejdź w tryb ponownej synchronizacji. Arkusz dzielenia replikacji początkowej na partie dla wybranego celu punktu odzyskiwania zawiera szczegółowe informacje na temat maszyn wirtualnych, które powinny zostać uwzględnione w każdej partii.

Na poniższym wykresie przedstawiono rozkład przepustowości dla replikacji początkowej i replikacji różnicowej w partiach w danej kolejności. W przypadku ochrony maszyn wirtualnych pierwszej partii, pełnej przepustowości dostępnej dla replikacji początkowej. Po zakończeniu replikacji początkowej dla pierwszego partii część przepustowości jest wymagane dla replikacji różnicowej. Pozostałe przepustowości dostępnej dla replikacji początkowej drugi partii maszyn wirtualnych. 

Na pasku partii 2 przedstawiono wymaganą przepustowości replikacji różnicowej dla maszyn wirtualnych partii 1 i przepustowość dostępną dla replikacji początkowej maszyn wirtualnych partii 2. Podobnie na pasku partii 3 przedstawiono przepustowości wymagane dla replikacji różnicowej dla poprzedniego instancje (1 partii i partii 2 VMs) oraz przepustowość dostępną dla replikacji początkowej dla partii 3 i tak dalej. Po zakończeniu replikacji początkowej wszystkich partii ostatniego pasek pokazuje, ile przepustowości wymagane dla replikacji różnicowej dla wszystkich chronionych maszyn wirtualnych.

**Dlaczego należy podzielić replikację początkową na partie?**
Czas ukończenia replikacji początkowej opiera się na rozmiarze dysku maszyny wirtualnej, używanym miejscu na dysku i dostępnej przepływności sieci. Szczegóły są dostępne w arkuszu dzielenia replikacji początkowej na partie na dla wybranego celu punktu odzyskiwania.

### <a name="cost-estimation"></a>Szacowanie kosztów
Wykres przedstawia widok podsumowania koszt odzyskiwania (DR) szacowany po całkowitej awarii na platformie Azure w Twoim regionie wybranego celu i walut, określoną w celu wygenerowania raportu.

![Podsumowanie szacowania kosztów](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Podsumowanie pomaga zrozumieć koszt, który trzeba płacić za magazyn, obliczeniowych, sieci i licencjonowania w przypadku ochrony wszystkie zgodne maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. Koszt jest obliczany zgodne maszyn wirtualnych, a nie na wszystkich PROFILOWANEGO maszyn wirtualnych. 
 
Koszt można wyświetlić w rozliczeniu miesięcznym lub rocznym. Dowiedz się więcej o [obsługiwanych regionach docelowych](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) i [obsługiwanych walutach](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Koszt przez składniki**: całkowity koszt odzyskiwania po awarii jest podzielony na cztery składniki: obliczeń, magazynu, sieci i usługi Site Recovery licencji kosztów. Koszt jest obliczany na podstawie zużycia, która powstaje podczas replikacji i w czasie wyszczególniania odzyskiwania po awarii. Obliczeń, magazynu (premium i standardowa) ExpressRoute/VPN skonfigurowano między lokacją lokalną i platformy Azure i licencji usługi Site Recovery są używane do obliczeń.

**Koszt przez Państwa**: koszt odzyskiwania po całkowitej awarii jest skategoryzowany oparte na dwa różne stany: replikacji i awarii. 

**Koszt replikacji**: koszt powstaje podczas replikacji. Obejmuje ona kosztów licencji usługi Site Recovery, sieci i magazynu. 

**Przechodzenie do szczegółów DR koszt**: koszt powstaje podczas testu pracy w trybie Failover. Usługa Site Recovery obraca się maszyn wirtualnych podczas testowania trybu failover. Przechodzenie do szczegółów kosztów odzyskiwania po awarii obejmuje uruchomionych maszyn wirtualnych koszt zasobów obliczeniowych i magazynu. 

**Azure koszt magazynowania na miesiąc i rok**: wykres słupkowy wyświetlany poniesienia — wersja premium i standardowa magazynu dla replikacji i awarii koszt całkowita ilość miejsca. W arkuszu [Szacowanie kosztów](hyper-v-deployment-planner-cost-estimation.md) możesz wyświetlić szczegółową analizę kosztów dla poszczególnych maszyn wirtualnych.

### <a name="growth-factor-and-percentile-values-used"></a>Używane wartości współczynnika wzrostu i percentyla
W tej sekcji w dolnej części arkusz zawiera wartość używana dla wszystkich liczników wydajności PROFILOWANEGO maszyn wirtualnych (wartość domyślna to 95. percentyl). Współczynnik wzrostu zawiera także (wartość domyślna to 30 procent) używany do obliczeń.

![Używane wartości współczynnika wzrostu i percentyla](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Zalecenia dotyczące danych wejściowych w postaci dostępnej przepustowości
![Omówienie profilowania przy użyciu danych wejściowych przepustowości](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Konieczne może być sytuacja, w którym wiadomo, że nie można ustawić przepustowości większej niż x Mbps replikacji usługi Site Recovery. Można użyć narzędzia jako danych wejściowych dostępną przepustowość (za pomocą parametru przepustowości podczas generowania raportu) i uzyskać osiągalne cel punktu odzyskiwania w minutach. Z tym osiągalne wartość RPO można zdecydować, czy musisz udostępnić dodatkowe ograniczenie użycia przepustowości lub zadowalające rozwiązanie odzyskiwania po awarii, ten cel punktu odzyskiwania.

![Osiągalna wartość celu punktu odzyskiwania](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Zalecenie umieszczania magazynu maszyny Wirtualnej 
![Rozmieszczenie maszyny wirtualnej względem magazynu](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Typ magazynu dysków**: konto magazynu w warstwie Standardowa lub Premium używane do replikacji wszystkich odpowiednich maszyn wirtualnych wymienionych w kolumnie **Maszyny wirtualne do rozmieszczenia**.

**Sugerowany prefiks**: proponowany 3-znakowy prefiks, którego można użyć w nazwie konta magazynu. Możesz użyć własnego prefiksu, ale propozycja narzędzia będzie zgodna z [konwencją nazewnictwa partycji dla kont magazynu](https://aka.ms/storage-performance-checklist).

**Sugerowana nazwa konta**: nazwa konta magazynu po uwzględnieniu proponowanego prefiksu. Zastąp nazwę w nawiasach kątowych (< i >) niestandardowymi danymi wejściowymi.

**Konto magazynu dzienników**: wszystkie dzienniki replikacji są przechowywane na standardowym koncie magazynu. W przypadku maszyn wirtualnych replikowanych do konta magazynu w warstwie Premium skonfiguruj dodatkowe konto magazynu w warstwie Standardowa na potrzeby magazynu dzienników. Jedno konto magazynu dzienników w warstwie Standardowa może być używane przez wiele kont magazynu replikacji w warstwie Premium. Maszyny wirtualne replikowane do kont magazynu w warstwie Standardowa używają tego samego konta magazynu dla dzienników.

**Sugerowana nazwa konta dzienników**: nazwa konta dzienników magazynu po uwzględnieniu proponowanego prefiksu. Zastąp nazwę w nawiasach kątowych (< i >) niestandardowymi danymi wejściowymi.

**Podsumowanie rozmieszczania**: podsumowanie łącznego obciążenia maszyn wirtualnych na koncie magazynu podczas replikacji i pracy w trybie failover lub testu pracy w trybie failover. Podsumowanie zawiera:

* Całkowita liczba maszyn wirtualnych jest mapowany na konto magazynu. 
* Całkowita liczba odczytu/zapisu IOPS na wszystkich maszynach wirtualnych, które znajdują się na tym koncie magazynu.
* Całkowita liczba zapisu (replikacja) IOPS.
* Rozmiar całkowitą instalacji na wszystkich dyskach.
* Całkowita liczba dysków.

**Maszyny wirtualne do miejsca**: lista wszystkich maszyn wirtualnych, które powinny być umieszczane na konta magazynu podanego dla uzyskania optymalnej wydajności i użycia.

## <a name="compatible-vms"></a>Zgodne maszyny wirtualne
Raport programu Excel, generowane przez lokacji odzyskiwania wdrożenia Planistę zawiera szczegóły wszystkie zgodne maszyn wirtualnych w arkuszu "Zgodny maszyn wirtualnych".

![Zgodne maszyny wirtualne](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Nazwa maszyny wirtualnej**: nazwa maszyny wirtualnej używana w pliku VMListFile podczas generowania raportu. Ta kolumna obejmuje też dyski (VHD) dołączone do maszyn wirtualnych. Nazwy obejmują nazwy hostów funkcji Hyper-V, na których zostały rozmieszczone maszyny wirtualne po tym, jak narzędzie odnalazło je w trakcie okresu profilowania.

**Zgodność maszyny wirtualnej**: wartości to **Tak** i **Tak**\*. **Tak** \* dla wystąpień, w których maszyna wirtualna jest rozwiązaniem dla [magazynu Azure premium](https://aka.ms/premium-storage-workload). Tutaj profilowany dysk o wysokim współczynniku zmian lub dużej liczbie operacji we/wy na sekundę pasuje do rozmiaru dysku w warstwie Premium większego niż rozmiar mapowany do dysku. Konto magazynu decyduje o który typ dysku magazynu premium, aby mapować dysk, na podstawie jego rozmiaru: 
* Mniej niż 128 GB — P10.
* 128 GB do 256 GB jest P15 z replikacją.
* 256 GB do 512 GB — P20.
* 512 GB do 1024 GB jest P30.
* 1,025 GB do 2048 GB jest P40.
* 2,049 GB 4,095 GB to P50.

Na przykład jeśli charakterystyki obciążenia dysku powodują umieszczenie go w kategorii P20 lub P30, ale z powodu rozmiaru jest mapowany w dół do niższego typu magazynu Premium, narzędzie oznacza daną maszynę wirtualną jako **Tak**\*. Narzędzie zaleca również zmianę rozmiaru dysku źródłowego tak, aby mieścił się w zalecanym typie dysku Premium Storage lub zmianę docelowego typu dysku po zakończeniu pracy w trybie failover.

**Typ magazynu**: dostępne typy magazynu to Standardowa i Premium.

**Sugerowany prefiks**: 3-znakowy prefiks konta magazynu.

**Konto magazynu**: nazwa uwzględniająca proponowany prefiks konta magazynu.

**IOPS R/W piku (o wskaźnik wzrostu)**: szczytowego obciążenia odczytu/zapisu IOPS na dysku (wartość domyślna to 95. percentyl) oraz przyszłych współczynnik wzrostu (wartość domyślna to 30 procent). Całkowita liczba odczytu/zapisu IOPS maszyny wirtualnej nie zawsze sumę maszyny Wirtualnej poszczególnych dysków odczytu/zapisu IOPS. Szczytowa odczytu/zapisu IOPS maszyny wirtualnej są piku sumę jego poszczególnych dysków odczytu/zapisu IOPS podczas profilowania okresu co minutę.

**Szczytowa danych churn — w MB/s (współczynnik wzrostu)**: piku churn — szybkość na dysku (wartość domyślna to 95. percentyl) oraz przyszłych współczynnik wzrostu (wartość domyślna to 30 procent). Przenoszenie wszystkich danych maszyny wirtualnej nie zawsze sumę tworzeniem danych maszyny Wirtualnej poszczególnych dysków. Przenoszenie danych szczytu maszyny wirtualnej jest piku sumę przenoszenie jego poszczególnych dysków podczas co minutę okresu profilowania.

**Rozmiar maszyny Wirtualnej Azure**: idealnym zmapowane rozmiar maszyny Wirtualnej usługi w chmurze Azure dla to lokalnej maszyny Wirtualnej. Mapowanie jest oparty na pamięci lokalnej maszyny Wirtualnej, liczbę dysków rdzeni/kart sieciowych i odczytu/zapisu IOPS. Zawsze zalecany jest najmniejszy rozmiar maszyny wirtualnej platformy Azure zgodny ze wszystkimi charakterystykami lokalnej maszyny wirtualnej.

**Liczba dysków**: łączna liczba dysków (VHD) na maszynie wirtualnej.

**Rozmiar (GB) na dysku**: całkowity rozmiar wszystkich dysków maszyny wirtualnej. W narzędziu jest też wyświetlany rozmiar poszczególnych dysków maszyny wirtualnej.

**Rdzenie**: liczba rdzeni procesora CPU maszyny wirtualnej.

**Pamięć (MB)**: pamięć RAM maszyny wirtualnej.

**Karty sieciowe**: liczba kart sieciowych maszyny wirtualnej.

**Rozruch typu**: typ rozruchu maszyny wirtualnej. Dozwolone wartości to BIOS i EFI.

## <a name="incompatible-vms"></a>Niezgodne maszyny wirtualne
Raport programu Excel wygenerowanych przez wdrożenia Planistę odzyskiwania lokacji zawiera szczegóły wszystkie niezgodne maszyn wirtualnych w arkuszu "Niezgodny maszyn wirtualnych".

![Niezgodne maszyny wirtualne](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Nazwa maszyny wirtualnej**: nazwa maszyny wirtualnej używana w pliku VMListFile podczas generowania raportu. Ta kolumna obejmuje też dyski (VHD) dołączone do maszyn wirtualnych. Nazwy obejmują nazwy hostów funkcji Hyper-V, na których zostały rozmieszczone maszyny wirtualne po tym, jak narzędzie odnalazło je w trakcie okresu profilowania.

**Zgodność maszyny wirtualnej**: wskazuje, dlaczego dana maszyna wirtualna nie jest zgodna na potrzeby użycia z usługą Site Recovery. Niezgodność każdego dysku na podstawie opublikowanych [limitów magazynów](https://aka.ms/azure-storage-scalbility-performance) może wynikać z dowolnej spośród następujących przyczyn:

* Rozmiar dysku jest większy niż 4,095 GB. Magazyn Azure nie obsługuje obecnie rozmiary dysków danych jest większa niż 4,095 GB.

* Dysk systemu operacyjnego jest większa niż 2,047 GB generacji 1 (typ rozruchu systemu BIOS) maszyny Wirtualnej. Usługa Site Recovery nie obsługuje rozmiar dysku systemu operacyjnego jest większa niż 2,047 GB dla maszyny wirtualne generacji 1.

* Dysk systemu operacyjnego jest większa niż 300 GB generacji 2 (typ rozruchu interfejsu EFI) maszyny Wirtualnej. Usługa Site Recovery nie obsługuje ponad 300 GB rozmiar dysku systemu operacyjnego dla maszyn wirtualnych generacji 2.

* Nazwa maszyny Wirtualnej nie jest obsługiwana za pomocą dowolnego z następujących znaków: "" [] ". Narzędzia nie można pobrać danych profilowanych maszyn wirtualnych, które nie ma żadnej z tych znaków w nazwach. 

* Wirtualny dysk twardy jest współużytkowany przez co najmniej dwie maszyny wirtualne. Azure nie obsługuje maszyn wirtualnych z udostępnionego wirtualnego dysku twardego.

* Maszyny Wirtualnej z wirtualny protokół Fiber Channel nie jest obsługiwana. Usługa Site Recovery nie obsługuje maszyn wirtualnych o wirtualny protokół Fiber Channel.

* Klaster funkcji Hyper-V nie zawiera brokera replikacji. Usługa Site Recovery nie obsługuje maszyny Wirtualnej w klastrze funkcji Hyper-V, jeśli brokera funkcji Hyper-V Replica nie jest skonfigurowana dla klastra.

* Maszyna wirtualna bez wysokiej dostępności. Usługi Site Recovery nie obsługuje maszyny Wirtualnej funkcji Hyper-V węzła klastra, których wirtualne dyski twarde są przechowywane na dysku lokalnym, a nie na dysku klastra. 

* Całkowity rozmiar maszyny Wirtualnej (replikacji + testowy tryb failover) przekracza limit rozmiaru konta magazynu premium obsługiwane (35 TB). Ta niezgodność występuje przeważnie, jeśli wartość charakterystyki wydajności pojedynczego dysku maszyny wirtualnej przekracza maksymalny obsługiwany limit standardowego magazynu platformy Azure lub usługi Site Recovery. Takie wystąpienie powoduje przeniesienie do strefy magazynów Premium Storage. Jednak maksymalny rozmiar obsługiwany konta premium magazynu to 35 TB. Pojedynczy chronionej maszyny Wirtualnej nie może być chroniony przez wiele kont magazynu. 

    Podczas testowania trybu failover na chronionej maszynie Wirtualnej i niezarządzane dysk jest skonfigurowany do testowania trybu failover, jest ono wykonywane na tym samym koncie magazynu gdzie postępuje replikacji. W tym wystąpieniu samego dodatkowej ilości miejsca do magazynowania jest wymagany co replikacji. Dzięki temu replikacja będzie kontynuowana i równocześnie test pracy w trybie failover zakończy się sukcesem. Po skonfigurowaniu dysków zarządzanych do testowania trybu failover nie dodatkowego miejsca musi wyjaśnić z testowy tryb failover maszyny Wirtualnej.

* Źródło IOPS przekracza limit IOPS obsługiwanego magazynu 7 500 na dysku.

* Źródło IOPS przekracza limit IOPS obsługiwanego magazynu 80,000 dla maszyny Wirtualnej.

* Postęp dokonany w źródłowej maszyny Wirtualnej uśrednianie danych przekracza obsługiwany limit przenoszenia danych usługi Site Recovery 10 MB/s dla średni rozmiar operacji We/Wy.

* IOPS przekracza obsługiwany limit IOPS odzyskiwania lokacji 840 zapisu obowiązującej średni źródłowej maszyny Wirtualnej.

* Obliczony magazyn migawek przekracza obsługiwany limit magazynu migawek wynoszący 10 TB.

**IOPS R/W piku (o wskaźnik wzrostu)**: obciążenia szczytowego IOPS na dysku (wartość domyślna to 95. percentyl) oraz przyszłych współczynnik wzrostu (wartość domyślna to 30 procent). Całkowita liczba odczytu/zapisu IOPS maszyny wirtualnej nie zawsze sumę maszyny Wirtualnej poszczególnych dysków odczytu/zapisu IOPS. Szczytowa odczytu/zapisu IOPS maszyny wirtualnej jest piku sumę jego poszczególnych dysków odczytu/zapisu IOPS podczas co minutę okresu profilowania.

**Szczytowa danych churn — (MB/s) (o wskaźnik wzrostu)**: piku churn — szybkość na dysku (wartość domyślna to 95. percentyl) oraz przyszłych współczynnik wzrostu (wartość domyślna to 30 procent). Należy pamiętać, że całkowita danych churn — maszyny wirtualnej nie zawsze jest sumą tworzeniem danych maszyny Wirtualnej poszczególnych dysków. Przenoszenie danych szczytu maszyny wirtualnej jest piku sumę przenoszenie jego poszczególnych dysków podczas co minutę okresu profilowania.

**Liczba dysków**: łączna liczba dysków VHD maszyny wirtualnej.

**Rozmiar (GB) na dysku**: rozmiar całkowitą Instalatora wszystkich dysków maszyny wirtualnej. W narzędziu jest też wyświetlany rozmiar poszczególnych dysków maszyny wirtualnej.

**Rdzenie**: liczba rdzeni procesora CPU maszyny wirtualnej.

**Pamięć (MB)**: wielkość pamięci RAM maszyny wirtualnej.

**Karty sieciowe**: liczba kart sieciowych maszyny wirtualnej.

**Rozruch typu**: typ rozruchu maszyny wirtualnej. Dozwolone wartości to BIOS i EFI.

## <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Site Recovery. Ograniczenia te są oparte na testach, ale nie dotyczą wszystkich aplikacji możliwych kombinacji we/wy. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. Aby uzyskać najlepsze wyniki nawet po zakończeniu planowania wdrożenia, wykonaj szeroką gamę do testowania aplikacji przez wystawienie test trybu failover można pobrać obrazu true wydajność aplikacji.
 
**Cel magazynu replikacji** | **Rozmiar źródła we/wy średni maszyny Wirtualnej** |**Przenoszenie uśrednianie danych maszyny Wirtualnej źródłowego** | **Łączny współczynnik zmian danych źródłowej maszyny wirtualnej dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s na maszynę wirtualną | 168 GB na maszynę wirtualną
Premium Storage | 8 KB  | 5 MB/s na maszynę wirtualną | 421 GB na maszynę wirtualną
Premium Storage | 16 KB lub więcej| 10 MB/s na maszynę wirtualną | 842 GB na maszynę wirtualną

Limity te są średnimi wartościami przy założeniu 30-procentowego nakładania się operacji we/wy. Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy. Poprzednie liczby zakładają typowe zaległości wynoszące około pięć minut. To, że po przekazaniu danych jest przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="on-premises-storage-requirement"></a>Wymagania dotyczące magazynu lokalnego

Arkusz zawiera informacje o łącznym wymaganym wolnym miejscu w magazynie dla każdego woluminu serwerów funkcji Hyper-V (gdzie znajdują się dyski VHD) na potrzeby pomyślnego ukończenia replikacji początkowej i replikacji różnicowej. Przed włączeniem replikacji, Dodaj miejsce do magazynowania wymaganego w woluminach, aby upewnić się, że replikacja nie powoduje żadnych niepożądanych przestojów aplikacji produkcyjnych. 

  Planowanie wdrożenia odzyskiwania lokacji identyfikuje magazynu optymalne wymagania dotyczące wolnego miejsca, na podstawie rozmiaru dysku VHD i przepustowość sieci wykorzystywaną podczas replikacji.

![Wymagania dotyczące magazynu lokalnego](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Dlaczego trzeba mieć wolne miejsca na serwerze funkcji Hyper-V podczas replikacji?
* Po włączeniu replikacji maszyny wirtualnej odzyskiwania lokacji tworzy migawkę każdego wirtualnego dysku twardego maszyny wirtualnej dla replikacji początkowej. W czasie replikacji początkowej nowe zmiany są zapisywane na dyskach przez aplikację. Usługa Site Recovery śledzi te zmiany różnicowe w plikach dziennika, które wymagają dodatkowego miejsca. Dopiero po zakończeniu replikacji początkowej, pliki dziennika są przechowywane lokalnie. 

    Jeśli wystarczającą ilość miejsca jest niedostępna dla plików dziennika i migawka (AVHDX), replikacja przechodzi w tryb ponownej synchronizacji i replikacji nigdy nie zostało zakończone. W najgorszym przypadku potrzebne dodatkowe wolne miejsce 100 procent rozmiaru wirtualnego dysku twardego dla replikacji początkowej.
* Po zakończeniu replikacji początkowej, rozpoczyna się replikacja różnicowa. Usługa Site Recovery śledzi zmiany tych zmian w plikach dziennika, które są przechowywane w woluminie, w którym znajdują się wirtualne dyski twarde maszyny wirtualnej. Te pliki dziennika replikowane do platformy Azure z częstotliwością skonfigurowanych kopiowania. W zależności od dostępnej przepustowości sieci replikowanie plików dziennika na platformie platformy Azure może potrwać pewien czas. 

    Jeśli dostępne do przechowywania plików dziennika nie jest wystarczająco dużo wolnego miejsca, replikacja została wstrzymana. Następnie stan replikacji maszyny wirtualnej przechodzi w stan "wymaganej ponownej synchronizacji."
* Jeśli przepustowość sieci nie wystarcza do dystrybuowania plików dziennika na platformie Azure, plików dziennika uzyskać stercie na woluminie. W najgorszym przypadku gdy zwiększeniu rozmiaru plików dziennika do 50% rozmiaru wirtualnego dysku twardego replikacji maszyny Wirtualnej przechodzi w stan "wymaganej ponownej synchronizacji." W najgorszym przypadku potrzebne dodatkowe wolne miejsce 50 procent rozmiaru wirtualnego dysku twardego dla replikacji różnicowej.

**Host funkcji Hyper-V**: lista profilowanych serwerów funkcji Hyper-V. Jeśli serwer jest częścią klastra funkcji Hyper-V, wszystkie węzły klastra są grupowane razem.

**Wolumin (ścieżka dysku VHD)**: każdy wolumin hosta funkcji Hyper-V, na którym znajdują się dyski VHD/VHDX. 

**Dostępne wolne miejsce (GB)**: wolne miejsce dostępne na woluminie.

**Łączna liczba miejsca do magazynowania wymaganego w woluminie (GB)**: Całkowita ilość wolnego miejsca wymagane miejsce na wolumin zakończona pomyślnie Replikacja początkowa i replikacji różnicowej. 

**Łączna liczba dodatkowego magazynu na potrzeby aprowizacji w wolumin zakończona pomyślnie replikacja (GB)**: sugeruje całkowita dodatkowe miejsce, które należy udostępnić na woluminie zakończona pomyślnie Replikacja początkowa i replikacja różnicowa.

## <a name="initial-replication-batching"></a>Dzielenie replikacji początkowej na partie 

### <a name="why-do-i-need-initial-replication-batching"></a>Dlaczego warto przeprowadzić I konieczne wstępne przetwarzanie wsadowe replikacji?
Jeśli wszystkie maszyny wirtualne chronione na tym samym czasie, wymaganie wolnego miejsca jest znacznie wyższa. Jeśli w magazynie nie jest dostępny, replikacja maszyn wirtualnych przechodzi do trybu ponownej synchronizacji. Ponadto wymagana przepustowość sieci jest znacznie wyższa na zakończenie replikacji początkowej dla wszystkich maszyn wirtualnych ze sobą pomyślnie. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Dzielenie replikacji początkowej na partie dla wybranego celu punktu odzyskiwania replikacji początkowej
Ten arkusz zawiera widok szczegółowy każdej partii IR. Dla każdego celu puntu odzyskiwania jest tworzony oddzielny arkusz dzielenia replikacji początkowej na partie. 

Po wykonaniu zalecenie wymagania magazynu lokalnego dla każdego woluminu głównego informacje wymagane do replikowania jest lista maszyn wirtualnych, które mogą być chronione równolegle. Te maszyny wirtualne są pogrupowane w partii, a może istnieć wiele instancji. Ochrona maszyn wirtualnych w danej partii kolejności. Najpierw ochrona maszyn wirtualnych 1 partii. Po zakończeniu replikacji początkowej, ochrona maszyn wirtualnych 2 partii i tak dalej. Ten arkusz zawiera listę partii i odpowiadających im maszyn wirtualnych. 

![Przetwarzanie wsadowe szczegóły IR.](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Dodatkowe IR, przetwarzanie wsadowe szczegóły](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Każda partia udostępnia następujące informacje: 
**Host funkcji Hyper-V**: host funkcji Hyper-V maszyny wirtualnej do objęcia ochroną.

**Maszyna wirtualna**: maszyny Wirtualnej mają być chronione. 

**Komentarze**: jeśli w przypadku określonego woluminu maszyny wirtualnej wymagane jest wykonanie akcji, w tym miejscu znajduje się komentarz. Jeśli wystarczające wolne miejsce nie jest dostępna na woluminie, na przykład komentarz jest wyświetlany komunikat "Dodaj dodatkowy magazyn, ochronę tej maszyny Wirtualnej."

**Wolumin (ścieżka wirtualnego dysku twardego)**: Nazwa woluminu, w którym znajdują się wirtualne dyski twarde maszyny Wirtualnej. 

**Wolnego miejsca na woluminie (GB)**: wolnego miejsca na woluminie dla maszyny Wirtualnej. Podczas obliczania wolnego miejsca dostępnego na woluminach jest uwzględniane miejsce na dysku używane do replikacji różnicowej przez maszyny wirtualne z poprzednich partii, których dyski VHD znajdują się na tym samym woluminie. 

Na przykład VM1, maszyny VM2 i VM3 znajdują się na woluminie, E:\VHDpath. Przed replikacją wolne miejsce na woluminie wynosi 500 GB. VM1 jest częścią 1 partii maszyny VM2 jest częścią 2 partii i VM3 jest częścią Batch3. W przypadku maszyny VM1 dostępne wolne miejsce wynosi 500 GB. Dla maszyny VM2, dostępnego wolnego miejsca jest 500 — wymagane dla replikacji różnicowej dla VM1 miejsce na dysku. Jeśli replikacja przyrostowa VM1 wymaga 300 GB miejsca, wolnego miejsca dostępnego dla maszyny VM2 jest 500 GB – 300 GB = 200 GB. Podobnie maszyna VM2 wymaga 300 GB dla replikacji różnicowej. Wolne miejsce dostępne dla VM3 jest 200 GB 300 GB =-100 GB.

**Magazyn na woluminie wymagany dla replikacji początkowej (GB)**: wolne miejsce do magazynowania wymagane na woluminie dla maszyny wirtualnej do pomyślnego zakończenia replikacji początkowej i replikacji różnicowej.

**Wymagane na woluminie dla replikacja różnicowa (GB) magazynu**: wolnego miejsca na wolumin wymagane dla maszyny Wirtualnej, różnicowe replikacji.

**Wymagany dodatkowy magazyn oparty na niedoborze umożliwiający uniknięcie niepowodzenia replikacji (GB)**: dodatkowe wymagane miejsce na woluminie dla maszyny wirtualnej. Jest maksymalnie replikacji początkowej i wymagań dotyczących miejsca przechowywania replikacji różnicowej minus wolnego miejsca na woluminie.

**Minimalna przepustowość wymagana dla replikacji początkowej (Mb/s)**: minimalna przepustowość wymagana na potrzeby replikacji początkowej dla maszyny wirtualnej.

**Przepustowość minimalna wymagane dla replikacji różnicowej (MB/s)**: minimalnej przepustowości, które są wymagane dla replikacji różnicowej dla maszyny Wirtualnej.

### <a name="network-utilization-details-for-each-batch"></a>Szczegóły wykorzystania sieci dla każdej partii 
Każda tabela partii zawiera podsumowanie wykorzystania sieci przez partię.

**Przepustowość dostępną dla partii**: przepustowość dostępną dla partii, po uwzględnieniu przepustowości replikacji różnicowej poprzedniej wsadowym.

**Przybliżona przepustowość dostępna dla replikacji początkowej partii**: przepustowość dostępna dla replikacji początkowej maszyn wirtualnych w partii. 

**Przybliżona przepustowość wykorzystana dla replikacji różnicowej partii**: przepustowość potrzebna do replikacji różnicowej maszyn wirtualnych w partii. 

**Szacowany czas replikacji początkowej dla partii (gg: mm)**: czas szacowany replikacji początkowej w godziny: minuty.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [szacowania kosztów](hyper-v-deployment-planner-cost-estimation.md).
