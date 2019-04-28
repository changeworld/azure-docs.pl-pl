---
title: Analizowanie raportu Planisty wdrożenia usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak analizować raportu generowany przez Planistę wdrażania usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V do platformy Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: 7bfe382ac1a175aafb4944dffa8d12a372f4fb70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772895"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analizowanie raportu Planisty wdrożenia usługi Azure Site Recovery
W tym artykule omówiono arkusze zawarte w raporcie programu Excel wygenerowanym przez Planistę wdrażania usługi Azure Site Recovery w przypadku scenariusza dotyczącego funkcji Hyper-V na platformie Azure.

## <a name="on-premises-summary"></a>Podsumowanie środowiska lokalnego
Arkusz podsumowania środowiska lokalnego zawiera omówienie profilowanego środowiska Hyper-V.

![Podsumowanie środowiska lokalnego](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Data rozpoczęcia** i **Data zakończenia**: Daty rozpoczęcia i zakończenia profilowania danych na potrzeby generowania raportu. Domyślnie data rozpoczęcia to data rozpoczęcia profilowania, a data zakończenia to data zatrzymania profilowania. Tymi informacjami mogą być wartości „StartDate” i „EndDate”, jeśli raport jest generowany przy użyciu tych parametrów.

**Całkowita liczba dni profilowania**: Całkowita liczba dni profilowania od daty rozpoczęcia i zakończenia, dla których jest generowany raport.

**Liczba zgodnych maszyn wirtualnych**: Całkowita liczba zgodnych maszyn wirtualnych, dla których są obliczane wymagana przepustowość sieci oraz wymagana liczba kont magazynu i liczba rdzeni platformy Azure.

**Łączna liczba dysków wszystkich zgodnych maszyn wirtualnych**: Łączna liczba dysków wszystkich zgodnych maszyn wirtualnych.

**Średnia liczba dysków na zgodną maszynę wirtualną**: Średnia liczba dysków obliczana dla wszystkich zgodnych maszyn wirtualnych.

**Średni rozmiar dysku (GB)**: Średni rozmiar dysku obliczany dla wszystkich zgodnych maszyn wirtualnych.

**Żądany cel punktu odzyskiwania (w minutach)**: Albo domyślny cel punktu odzyskiwania lub przekazana dla parametru "DesiredRPO" podczas generowania raportu umożliwiająca oszacowanie wymaganej przepustowości wartość.

**Żądana przepustowość (MB/s)**: Wartość przekazana dla parametru "Bandwidth" podczas generowania raportu umożliwiająca oszacowanie cel punktu odzyskiwania osiągalna (RPO).

**Zaobserwowany Typowy współczynnik zmian danych dziennie (GB)**: Średni współczynnik zmian danych zaobserwowany we wszystkie dni profilowania.

## <a name="recommendations"></a>Zalecenia 
Arkusz Zalecenia raportu dotyczącego replikacji z funkcji Hyper-V do platformy Azure zawiera następujące szczegółowe informacje zgodnie z wybranym żądanym celem punktu odzyskiwania:

![Zalecenia z raportu replikacji z funkcji Hyper-V do platformy Azure](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profilowanie danych
![Profilowanie danych](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Okres profilowanych danych**: Okres, w którym zostało uruchomione profilowanie. Domyślnie w obliczeniach narzędzie uwzględnia wszystkie profilowane dane. Jeśli podczas generowania raportu użyto opcji StartDate i EndDate, będzie on obejmować wybrany okres. 

**Liczba serwerów funkcji Hyper-V profilowane**: Liczba serwerów funkcji Hyper-V jest generowany raport maszyn wirtualnych. Wybierz liczbę, aby wyświetlić nazwy serwerów funkcji Hyper-V. Zostanie otwarty arkusz wymagań dotyczących magazynu lokalnego, w którym zostaną wyświetlone wszystkie serwery z wymaganiami dotyczącymi magazynu. 

**Żądany cel punktu odzyskiwania**: Cel punktu odzyskiwania dla danego wdrożenia. Domyślnie wymagana przepustowość sieci jest obliczana dla wartości celu punktu odzyskiwania równych 15, 30 i 60 minut. Odpowiednie wartości są aktualizowane w arkuszu zgodnie z wybraną wartością. W przypadku użycia parametru DesiredRPOinMin podczas generowania raportu ta wartość jest wyświetlana w obszarze wyniku żądanego celu punktu odzyskiwania.

### <a name="profiling-overview"></a>Omówienie profilowania
![Omówienie profilowania](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Łączna liczba profilowanych maszyn wirtualnych**: Całkowita liczba maszyn wirtualnych, których profilowane dane są dostępne. Jeśli parametr VMListFile zawiera nazwy nieprofilowanych maszyn wirtualnych, te maszyny wirtualne nie zostaną uwzględnione podczas generowania raportów i zostaną wykluczone z łącznej liczby profilowanych maszyn wirtualnych.

**Zgodne maszyny wirtualne**: Liczba maszyn wirtualnych, które mogą być chronione na platformie Azure przy użyciu usługi Azure Site Recovery. Jest to łączna liczba zgodnych maszyn wirtualnych, dla których są obliczane: wymagana przepustowość sieci, liczba kont magazynu i liczba rdzeni platformy Azure. Szczegóły wszystkich maszyn wirtualnych są dostępne w sekcji „Zgodne maszyny wirtualne”.

**Niezgodne maszyny wirtualne**: Liczba profilowanych maszyn wirtualnych, które są zgodne na potrzeby ochrony za pomocą usługi Site Recovery. Przyczyny niezgodności wymieniono w sekcji „Niezgodne maszyny wirtualne”. Jeśli plik VMListFile zawiera nazwy maszyn wirtualnych, które nie były profilowane, te maszyny wirtualne są wykluczane z liczby niezgodnych maszyn wirtualnych. Dla tych maszyn wirtualnych jest wyświetlany tekst „Nie znaleziono danych” na końcu sekcji „Niezgodne maszyny wirtualne”.

**Żądany cel punktu odzyskiwania**: Żądany odzyskiwania punktu cel, w ciągu kilku minut. Raport jest generowany dla trzech wartości celu punktu odzyskiwania: 15 (ustawienie domyślne), 30 i 60 minut. Zalecenie dotyczące przepustowości w raporcie zmienia się zgodnie z pozycją wybraną z listy rozwijanej **Żądany cel punktu odzyskiwania** w prawym górnym rogu arkusza. Jeśli raport został wygenerowany przy użyciu parametru -DesiredRPO z wartością niestandardową, ta wartość jest wyświetlana jako domyślna na liście rozwijanej **Żądany cel punktu odzyskiwania**.

### <a name="required-network-bandwidth-mbps"></a>Wymagana przepustowość sieci (Mb/s)
![Wymagana przepustowość sieci](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Aby osiągnąć cel punktu odzyskiwania przez 100% czasu**: Zalecana przepustowość w MB/s do przydzielenia, aby osiągnąć żądany cel punktu odzyskiwania 100 procent czasu. Taka przepustowość musi zostać przeznaczona na stałą replikację przyrostową wszystkich zgodnych maszyn wirtualnych, aby uniknąć naruszeń celu punktu odzyskiwania.

**Aby osiągnąć cel punktu odzyskiwania przez 90% czasu**: Być może z powodu cenami połączeń szerokopasmowych lub z innego powodu nie można ustawić przepustowości wymaganej do osiągnięcia żądanego punktu odzyskiwania 100 procent czasu. W takim przypadku można skorzystać z ustawienia mniejszej przepustowości, które pozwoli osiągnąć żądany cel punktu odzyskiwania przez 90 procent czasu. Aby umożliwić poznanie skutków ustawienia mniejszej przepustowości, w raporcie przedstawiono analizę warunkową liczby i czasu trwania naruszeń celu punktu odzyskiwania.

**Osiągnięta przepływność**: Przepływność z serwera, na którym uruchomiono polecenie GetThroughput do regionu platformy Azure, w którym znajduje się na koncie magazynu. Wartość przepływności wskazuje szacowany poziom, który można osiągnąć w przypadku ochrony zgodnych maszyn wirtualnych przy użyciu usługi Site Recovery. Właściwości sieci i magazynu serwera funkcji Hyper-V muszą być takie same jak serwera, na którym jest uruchamiane narzędzie.

W przypadku wszystkich wdrożeń usługi Site Recovery w przedsiębiorstwach zalecamy użycie usługi [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Wymagane konta magazynu
Ten wykres przedstawia łączną liczbę kont magazynu (w warstwach Standardowa i Premium) wymaganych do ochrony wszystkich zgodnych maszyn wirtualnych. Aby dowiedzieć się, którego konta magazynu używać dla poszczególnych maszyn wirtualnych, zobacz sekcję „Rozmieszczenie maszyny wirtualnej względem magazynu”.

![Wymagane konta usługi Azure Storage](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Wymagana liczba rdzeni platformy Azure
Wynik to łączna liczba rdzeni do skonfigurowania przed rozpoczęciem pracy w trybie failover lub testem pracy w trybie failover dla wszystkich zgodnych maszyn wirtualnych. Jeśli liczba rdzeni w ramach subskrypcji jest zbyt mała, usługa Site Recovery nie może utworzyć maszyn wirtualnych w czasie pracy w trybie failover lub testu pracy w trybie failover.

![Wymagana liczba rdzeni platformy Azure](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Wymagania dotyczące dodatkowego magazynu lokalnego

Całkowita ilość wolnego miejsca wymagana na serwerach funkcji Hyper-V do pomyślnej replikacji początkowej i replikacji różnicowej, dzięki której masz pewność, że replikacja maszyny wirtualnej nie powoduje niepożądanych przestojów w działaniu aplikacji produkcyjnych. Więcej informacji na temat poszczególnych wymagań ilości jest dostępnych w sekcji [Wymagania dotyczące magazynu lokalnego](#on-premises-storage-requirement). 

Aby zrozumieć, dlaczego do replikacji jest wymagane wolne miejsce, zapoznaj się z sekcją [Wymagania dotyczące magazynu lokalnego](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication).

![Wymagania dotyczące magazynu lokalnego i częstotliwość kopiowania](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maksymalna częstotliwość kopiowania
Maksymalną zalecaną częstotliwość kopiowania należy ustawić, aby osiągać żądany cel punktu odzyskiwania. Wartość domyślna wynosi pięć minut. Częstotliwość kopiowania możesz ustawić na 30 sekund, aby osiągać lepszy cel punktu odzyskiwania.

### <a name="what-if-analysis"></a>Analiza warunkowa
![Analiza warunkowa](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Ta analiza przedstawia liczbę naruszeń, które mogą wystąpić podczas profilowania w przypadku ustawienia mniejszej przepustowości umożliwiającej osiąganie żądanego celu punktu odzyskiwania tylko przez 90 procent czasu. W danym dniu może wystąpić co najmniej jedno naruszenie. Wykres przedstawia szczyt celu punktu odzyskiwania w danym dniu. Na podstawie tej analizy można zdecydować, czy liczba naruszeń celu punktu odzyskiwania we wszystkich dniach i szczytowa wartość celu punktu odzyskiwania na dzień jest dopuszczalna przy określonej mniejszej przepustowości. Jeśli jest to akceptowalne, możesz przydzielić mniejszą przepustowość na potrzeby replikacji. W przeciwnym razie przydziel zgodnie z sugestią większą przepustowość, aby osiągnąć żądany cel punktu odzyskiwania przez 100 procent czasu. 

### <a name="recommendation-for-successful-initial-replication"></a>Zalecenie dotyczące pomyślnej replikacji początkowej
W tej sekcji omówiono liczbę partii, w których maszyny wirtualne mają być chronione, oraz minimalną przepustowość wymaganą do pomyślnego ukończenia replikacji początkowej. 

![Dzielenie replikacji początkowej na partie](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

Maszyny wirtualne muszą być chronione z zachowaniem podanej kolejności partii. Każda partia ma określoną listę maszyn wirtualnych. Maszyny wirtualne partii 1 muszą być chronione przed maszynami wirtualnymi partii 2. Maszyny wirtualne partii 2 muszą być chronione przed partią 3 itd. Po zakończeniu replikacji początkowej maszyn wirtualnych partii 1 można włączyć replikację dla maszyn wirtualnych partii 2. Podobnie po zakończeniu replikacji początkowej maszyn wirtualnych partii 2 można włączyć replikację dla maszyn wirtualnych partii 3 itd. 

Jeśli kolejność partii nie będzie przestrzegana, wystarczająca przepustowość dla replikacji początkowej może być niedostępna dla maszyn wirtualnych chronionych później. W rezultacie maszyny wirtualne nigdy nie zakończą replikacji początkowej lub kilka chronionych maszyn wirtualnych może przejść w tryb ponownej synchronizacji. Arkusz dzielenia replikacji początkowej na partie dla wybranego celu punktu odzyskiwania zawiera szczegółowe informacje na temat maszyn wirtualnych, które powinny zostać uwzględnione w każdej partii.

Na poniższym wykresie przedstawiono rozkład przepustowości dla replikacji początkowej i replikacji różnicowej w partiach w danej kolejności. W przypadku ochrony pierwszej partii maszyn wirtualnych w replikacji początkowej jest dostępna pełna przepustowość. Po zakończeniu replikacji początkowej pierwszej partii część przepustowości jest wymagana na potrzeby replikacji różnicowej. Pozostała przepustowość jest dostępna podczas replikacji początkowej drugiej partii maszyn wirtualnych. 

Na pasku partii 2 przedstawiono wymaganą przepustowości replikacji różnicowej dla maszyn wirtualnych partii 1 i przepustowość dostępną dla replikacji początkowej maszyn wirtualnych partii 2. Podobnie na pasku partii 3 przedstawiono przepustowość wymaganą dla replikacji różnicowej poprzednich partii (maszyn wirtualnych partii 1 i partii 2) oraz przepustowość dostępną na potrzeby replikacji początkowej dla partii 3 itd. Po zakończeniu replikacji początkowej wszystkich partii ostatni pasek pokazuje przepustowość wymaganą na potrzeby replikacji różnicowej dla wszystkich chronionych maszyn wirtualnych.

**Dlaczego należy podzielić replikację początkową na partie?**
Czas ukończenia replikacji początkowej opiera się na rozmiarze dysku maszyny wirtualnej, używanym miejscu na dysku i dostępnej przepływności sieci. Szczegóły są dostępne w arkuszu dzielenia replikacji początkowej na partie na dla wybranego celu punktu odzyskiwania.

### <a name="cost-estimation"></a>Szacowanie kosztów
Na wykresie przedstawiono podsumowanie szacowanych łącznych kosztów odzyskiwania po awarii na platformie Azure w wybranym regionie docelowym w walucie określonej na potrzeby generowania raportu.

![Podsumowanie szacowania kosztów](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Podsumowanie pomaga zrozumieć ponoszone koszty magazynowania, mocy obliczeniowej, użycia sieci i licencjonowania w przypadku ochrony wszystkich zgodnych maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. Koszt jest obliczany dla zgodnych maszyn wirtualnych, a nie dla wszystkich profilowanych maszyn wirtualnych. 
 
Koszt można wyświetlić w rozliczeniu miesięcznym lub rocznym. Dowiedz się więcej o [obsługiwanych regionach docelowych](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) i [obsługiwanych walutach](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Koszt według składników**: Łączny koszt odzyskiwania po awarii jest dzielony na cztery składniki: koszt licencji usługi obliczeniowe, Magazyn, sieci i Usługa Site Recovery. Koszt jest obliczany na podstawie wykorzystania podczas replikacji oraz w czasie testowania odzyskiwania po awarii. Do obliczeń są używane: moc obliczeniowa, magazyn (warstwa Premium i Standardowa), usługa ExpressRoute/sieć VPN skonfigurowana między lokacją lokalną i platformą Azure oraz licencja usługi Site Recovery.

**Koszt według stanów**: Koszt odzyskiwania po całkowitej awarii jest dzielony na kategorie na podstawie dwóch różnych stanów: replikacji i testowania odzyskiwania po awarii. 

**Koszt replikacji**: Koszt, który jest naliczany podczas replikacji. Obejmuje on koszt magazynu, użycia sieci i licencji usługi Site Recovery. 

**Koszt testowania odzyskiwania po awarii**: Koszt, który jest naliczany podczas testów pracy w trybie Failover. Usługa Site Recovery uruchamia maszyny wirtualne podczas testu pracy w trybie failover. Koszt testowania odzyskiwania po awarii obejmuje koszt magazynu i mocy obliczeniowej działających maszyn wirtualnych. 

**Koszt usługi Azure Storage na miesiąc/rok**: Wykres słupkowy przedstawia łączny koszt magazynu, który jest naliczany dla magazynu premium i standardowa podczas replikacji i testowania odzyskiwania po awarii. W arkuszu [Szacowanie kosztów](hyper-v-deployment-planner-cost-estimation.md) możesz wyświetlić szczegółową analizę kosztów dla poszczególnych maszyn wirtualnych.

### <a name="growth-factor-and-percentile-values-used"></a>Używane wartości współczynnika wzrostu i percentyla
W tej sekcji w dolnej części arkusza pokazano wartość percentyla używaną przez wszystkie liczniki wydajności profilowanych maszyn wirtualnych (domyślnie jest używany 95. percentyl). Pokazano także współczynnik wzrostu (wartość domyślna to 30 procent) używany we wszystkich obliczeniach.

![Używane wartości współczynnika wzrostu i percentyla](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Zalecenia dotyczące danych wejściowych w postaci dostępnej przepustowości
![Omówienie profilowania przy użyciu danych wejściowych przepustowości](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Może wystąpić sytuacja, w której nie można ustawić przepustowości większej niż x Mb/s na potrzeby replikacji usługi Site Recovery. Przy użyciu narzędzia możesz wprowadzić dostępną przepustowość (za pomocą parametru -Bandwidth podczas generowania raportu) i uzyskać osiągalny cel punktu odzyskiwania w minutach. Osiągalna wartość celu punktu odzyskiwania pozwala zdecydować, czy trzeba aprowizować dodatkową przepustowość, czy rozwiązanie odzyskiwania po awarii umożliwiające osiągnięcie tego celu punktu odzyskiwania jest zadowalające.

![Osiągalna wartość celu punktu odzyskiwania](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Zalecenie dotyczące rozmieszczenia maszyny wirtualnej względem magazynu 
![Rozmieszczenie maszyny wirtualnej względem magazynu](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Typ magazynu dysków**: Każda standardowa lub premium konta magazynu, który jest używany do replikacji wszystkich odpowiednich maszyn wirtualnych wymienionych w **maszyny wirtualne do rozmieszczenia** kolumny.

**Sugerowany prefiks**: Sugerowany 3 znakowy prefiks, który może służyć do nazwy konta magazynu. Możesz użyć własnego prefiksu, ale propozycja narzędzia będzie zgodna z [konwencją nazewnictwa partycji dla kont magazynu](https://aka.ms/storage-performance-checklist).

**Sugerowana nazwa konta**: Nazwa konta magazynu po uwzględnieniu proponowanego prefiksu. Zastąp nazwę w nawiasach kątowych (< i >) niestandardowymi danymi wejściowymi.

**Konto magazynu dzienników**: Wszystkie dzienniki replikacji są przechowywane na koncie magazynu w warstwie standardowa. W przypadku maszyn wirtualnych replikowanych do konta magazynu w warstwie Premium skonfiguruj dodatkowe konto magazynu w warstwie Standardowa na potrzeby magazynu dzienników. Jedno konto magazynu dzienników w warstwie Standardowa może być używane przez wiele kont magazynu replikacji w warstwie Premium. Maszyny wirtualne replikowane do kont magazynu w warstwie Standardowa używają tego samego konta magazynu dla dzienników.

**Sugerowana nazwa konta dzienników**: Twoja nazwa konta dzienników magazynu po uwzględnieniu proponowanego prefiksu. Zastąp nazwę w nawiasach kątowych (< i >) niestandardowymi danymi wejściowymi.

**Podsumowanie rozmieszczania**: Podsumowanie łączna liczba maszyn wirtualnych obciążenia na koncie magazynu podczas replikacji i testowania trybu failover lub pracy awaryjnej. Podsumowanie zawiera następujące informacje:

* Łączna liczba maszyn wirtualnych zamapowanych na konto magazynu. 
* Łączna liczba operacji we/wy odczytu i zapisu na sekundę na wszystkich maszynach wirtualnych umieszczonych na tym koncie magazynu.
* Łączna liczba operacji we/wy zapisu (replikacji) na sekundę.
* Łączny skonfigurowany rozmiar na wszystkich dyskach.
* Łączna liczba dysków.

**Maszyny wirtualne do rozmieszczenia**: Lista wszystkich maszyn wirtualnych, które powinny zostać umieszczone na danym koncie magazynu w celu uzyskania optymalnej wydajności i użycia.

## <a name="compatible-vms"></a>Zgodne maszyny wirtualne
Raport programu Excel generowany przez Planistę wdrażania usługi Site Recovery zawiera szczegóły wszystkich zgodnych maszyn wirtualnych w arkuszu „Zgodne maszyny wirtualne”.

![Zgodne maszyny wirtualne](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Nazwa maszyny Wirtualnej**: Nazwa maszyny Wirtualnej, która jest używana w pliku VMListFile podczas generowania raportu. Ta kolumna obejmuje też dyski (VHD) dołączone do maszyn wirtualnych. Nazwy obejmują nazwy hostów funkcji Hyper-V, na których zostały rozmieszczone maszyny wirtualne po tym, jak narzędzie odnalazło je w trakcie okresu profilowania.

**Zgodność maszyny Wirtualnej**: Wartości są **tak** i **tak**\*. **Tak** \* dla wystąpień, w których maszyna wirtualna odpowiada [premium SSD](../virtual-machines/windows/disks-types.md). Tutaj profilowany dysk o wysokim współczynniku zmian lub dużej liczbie operacji we/wy na sekundę pasuje do rozmiaru dysku w warstwie Premium większego niż rozmiar mapowany do dysku. Decyzja o tym, na jaki typ dysku magazynu Premium będzie mapowany dysk, jest podejmowana na podstawie jego rozmiaru na poziomie konta magazynu: 
* Mniej niż 128 GB — P10.
* 128 GB do 256 GB — P15.
* 256 GB do 512 GB — P20.
* 512 GB do 1024 GB — P30.
* 1025 GB do 2048 GB — P40.
* 2049 GB do 4095 GB — P50.

Na przykład jeśli charakterystyki obciążenia dysku powodują umieszczenie go w kategorii P20 lub P30, ale z powodu rozmiaru jest mapowany w dół do niższego typu magazynu Premium, narzędzie oznacza daną maszynę wirtualną jako **Tak**\*. Narzędzie zaleca również zmianę rozmiaru dysku źródłowego tak, aby mieścił się w zalecanym typie dysku Premium Storage lub zmianę docelowego typu dysku po zakończeniu pracy w trybie failover.

**Typ magazynu**: Standardowa lub premium.

**Sugerowany prefiks**: Konto magazynu 3 znakowy prefiks.

**Konto magazynu**: Nazwa która używa prefiks sugerowana konta magazynu.

**Szczytowy odczytu i zapisu na SEKUNDĘ (ze współczynnikiem wzrostu)**: Szczytowe obciążenia odczytu i zapisu operacji We/Wy na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Łączna liczba operacji we/wy odczytu i zapisu na sekundę maszyny wirtualnej nie zawsze jest sumą liczby operacji we/wy odczytu i zapisu na sekundę poszczególnych dysków maszyny wirtualnej. Wartość szczytowa liczby operacji we/wy odczytu i zapisu na sekundę maszyny wirtualnej jest wartością szczytową sumy liczby operacji we/wy odczytu i zapisu na sekundę jej poszczególnych dysków w każdej minucie okresu profilowania.

**Szczytowy współczynnik zmian danych w MB/s (ze współczynnikiem wzrostu)**: Szczytowy współczynnik zmian danych na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Łączna wartość współczynnika zmian danych maszyny wirtualnej nie zawsze jest sumą współczynników zmian danych poszczególnych dysków maszyny wirtualnej. Wartość szczytowa współczynnika zmian danych maszyny wirtualnej jest wartością szczytową sumy współczynników zmian danych jej poszczególnych dysków w każdej minucie okresu profilowania.

**Rozmiar maszyny Wirtualnej platformy Azure**: Idealnie zamapowany rozmiar maszyny Wirtualnej programu Azure Cloud Services dla tej maszyny Wirtualnej w środowisku lokalnym. Mapowanie jest oparte na wielkości pamięci, liczbie dysków/rdzeni/kart sieciowych oraz liczbie operacji we/wy zapisu i odczytu lokalnej maszyny wirtualnej. Zawsze zalecany jest najmniejszy rozmiar maszyny wirtualnej platformy Azure zgodny ze wszystkimi charakterystykami lokalnej maszyny wirtualnej.

**Liczba dysków**: Całkowita liczba dysków maszyny wirtualnej (VHD) na maszynie Wirtualnej.

**Rozmiar dysku (GB)**: Całkowity rozmiar wszystkich dysków maszyny wirtualnej. W narzędziu jest też wyświetlany rozmiar poszczególnych dysków maszyny wirtualnej.

**Rdzenie**: Liczba rdzeni procesora CPU na maszynie Wirtualnej.

**Pamięć (MB)**: Pamięć RAM maszyny Wirtualnej.

**NICs**: Liczba kart sieciowych na maszynie Wirtualnej.

**Typ rozruchu**: Typ rozruchu maszyny Wirtualnej. Dozwolone wartości to BIOS i EFI.

## <a name="incompatible-vms"></a>Niezgodne maszyny wirtualne
Raport programu Excel generowany przez Planistę wdrażania usługi Site Recovery zawiera szczegóły wszystkich niezgodnych maszyn wirtualnych w arkuszu „Niezgodne maszyny wirtualne”.

![Niezgodne maszyny wirtualne](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Nazwa maszyny Wirtualnej**: Nazwa maszyny Wirtualnej, która jest używana w pliku VMListFile podczas generowania raportu. Ta kolumna obejmuje też dyski (VHD) dołączone do maszyn wirtualnych. Nazwy obejmują nazwy hostów funkcji Hyper-V, na których zostały rozmieszczone maszyny wirtualne po tym, jak narzędzie odnalazło je w trakcie okresu profilowania.

**Zgodność maszyny Wirtualnej**: Wskazuje, dlaczego danej maszyny Wirtualnej jest zgodna na potrzeby użycia z usługą Site Recovery. Niezgodność każdego dysku na podstawie opublikowanych [limitów magazynów](https://aka.ms/azure-storage-scalbility-performance) może wynikać z dowolnej spośród następujących przyczyn:

* Rozmiar dysku jest większy niż 4095 GB. Usługa Azure Storage obecnie nie obsługuje dysków danych większych niż 4095 GB.

* Dysk systemu operacyjnego jest większy niż 2047 GB dla maszyny wirtualnej 1. generacji (typ rozruchu: BIOS). W przypadku maszyn wirtualnych 1. generacji usługa Site Recovery nie obsługuje rozmiaru dysku systemu operacyjnego większego niż 2047 GB.

* Dysk systemu operacyjnego jest większy niż 300 GB dla maszyny wirtualnej 2. generacji (typ rozruchu: EFI). W przypadku maszyn wirtualnych 2. generacji usługa Site Recovery nie obsługuje rozmiaru dysku systemu operacyjnego większego niż 300 GB.

* Nazwy maszyn wirtualnych zawierające dowolny spośród następujących znaków nie są obsługiwane: “” [] `. Narzędzie nie może pobrać profilowanych danych maszyn wirtualnych, których nazwy zawierają dowolne spośród tych znaków. 

* Dysk VHD jest udostępniany między co najmniej dwiema maszynami wirtualnymi. Platforma Azure nie obsługuje maszyn wirtualnych z udostępnionym dyskiem VHD.

* Maszyna wirtualna z wirtualnym protokołem Fiber Channel nie jest obsługiwana. Usługa Site Recovery nie obsługuje maszyn wirtualnych z wirtualnym protokołem Fiber Channel.

* Klaster funkcji Hyper-V nie zawiera brokera replikacji. Usługa Site Recovery nie obsługuje maszyny wirtualnej w klastrze funkcji Hyper-V, jeśli nie skonfigurowano brokera repliki funkcji Hyper-V dla klastra.

* Maszyna wirtualna nie jest wysoko dostępna. Usługa Site Recovery nie obsługuje maszyny wirtualnej węzła klastra funkcji Hyper-V, którego dyski VHD są przechowywane na dysku lokalnym, a nie na dysku klastra. 

* Łączny rozmiar maszyny wirtualnej (suma replikacji i testu pracy w trybie failover) przekracza obsługiwany limit rozmiaru konta usługi Storage w wersji Premium (35 TB). Ta niezgodność występuje przeważnie, jeśli wartość charakterystyki wydajności pojedynczego dysku maszyny wirtualnej przekracza maksymalny obsługiwany limit standardowego magazynu platformy Azure lub usługi Site Recovery. Takie wystąpienie powoduje przeniesienie do strefy magazynów Premium Storage. Jednak maksymalny obsługiwany rozmiar konta magazynu Premium Storage to 35 TB. Pojedyncza chroniona maszyna wirtualna nie może być chroniona na wielu kontach magazynu. 

    Przeprowadzenie testu pracy w trybie failover na chronionej maszynie wirtualnej, jeśli na potrzeby testu pracy w trybie failover skonfigurowano dysk niezarządzany, powoduje uruchomienie go na tym samym koncie magazynu, na którym trwa replikacja. W tym wystąpieniu dodatkowa wymagana ilość miejsca w magazynie jest taka sama jak w przypadku replikacji. Dzięki temu replikacja będzie kontynuowana i równocześnie test pracy w trybie failover zakończy się sukcesem. W przypadku skonfigurowania dysku zarządzanego na potrzeby testu pracy w trybie failover nie trzeba uwzględniać dodatkowego miejsca dla maszyny wirtualnej testu pracy w trybie failover.

* Liczba źródłowych operacji we/wy na sekundę przekracza obsługiwany limit operacji we/wy na sekundę magazynu wynoszący 7500 operacji na dysk.

* Liczba źródłowych operacji we/wy na sekundę przekracza obsługiwany limit operacji we/wy na sekundę magazynu wynoszący 80 000 operacji na maszynę wirtualną.

* Średni współczynnik zmian danych źródłowej maszyny wirtualnej przekracza obsługiwany limit współczynnika zmian danych usługi Site Recovery wynoszący 10 MB/s dla średniego rozmiaru operacji we/wy.

* Średnia liczba operacji we/wy zapisu na sekundę źródłowej maszyny wirtualnej przekracza obsługiwany limit operacji we/wy na sekundę usługi Site Recovery równy 840 operacji.

* Obliczony magazyn migawek przekracza obsługiwany limit magazynu migawek wynoszący 10 TB.

**Szczytowy odczytu i zapisu na SEKUNDĘ (ze współczynnikiem wzrostu)**: Szczytowego obciążenia operacji We/Wy na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Łączna liczba operacji we/wy odczytu i zapisu na sekundę maszyny wirtualnej nie zawsze jest sumą liczby operacji we/wy odczytu i zapisu na sekundę poszczególnych dysków maszyny wirtualnej. Wartość szczytowa liczby operacji we/wy odczytu i zapisu na sekundę maszyny wirtualnej jest wartością szczytową sumy liczby operacji we/wy odczytu i zapisu na sekundę jej poszczególnych dysków w każdej minucie okresu profilowania.

**Szczytowy współczynnik zmian danych (MB/s) (ze współczynnikiem wzrostu)**: Szczytowy współczynnik zmian danych na dysku (domyślnie jest używany 95. percentyl) wraz z przyszłym współczynnikiem wzrostu (wartość domyślna to 30 procent). Zwróć uwagę, że łączna wartość współczynnika zmian danych maszyny wirtualnej nie zawsze jest sumą współczynników zmian danych poszczególnych dysków maszyny wirtualnej. Wartość szczytowa współczynnika zmian danych maszyny wirtualnej jest wartością szczytową sumy współczynników zmian danych jej poszczególnych dysków w każdej minucie okresu profilowania.

**Liczba dysków**: Całkowita liczba wirtualnych dysków twardych na maszynie Wirtualnej.

**Rozmiar dysku (GB)**: Łączny skonfigurowany rozmiar wszystkich dysków maszyny wirtualnej. W narzędziu jest też wyświetlany rozmiar poszczególnych dysków maszyny wirtualnej.

**Rdzenie**: Liczba rdzeni procesora CPU na maszynie Wirtualnej.

**Pamięć (MB)**: Ilość pamięci RAM na maszynie Wirtualnej.

**NICs**: Liczba kart sieciowych na maszynie Wirtualnej.

**Typ rozruchu**: Typ rozruchu maszyny Wirtualnej. Dozwolone wartości to BIOS i EFI.

## <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Site Recovery. Limity te są oparte na testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. Aby uzyskać najlepsze wyniki nawet po zakończeniu planowania wdrożenia, dokładnie przetestuj aplikację przy użyciu testu pracy w trybie failover w celu uzyskania prawdziwych informacji o wydajności aplikacji.
 
**Cel magazynu replikacji** | **Średni rozmiar operacji we/wy źródłowej maszyny wirtualnej** |**Średni współczynnik zmian danych źródłowej maszyny wirtualnej** | **Łączny współczynnik zmian danych źródłowej maszyny wirtualnej dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s na maszynę wirtualną | 168 GB na maszynę wirtualną
Premium Storage | 8 KB  | 5 MB/s na maszynę wirtualną | 421 GB na maszynę wirtualną
Premium Storage | 16 KB lub więcej| 20 MB/s na maszynę Wirtualną | 1684 GB na maszynę Wirtualną

Limity te są średnimi wartościami przy założeniu 30-procentowego nakładania się operacji we/wy. Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy. Poprzednie liczby zakładają typowe zaległości wynoszące około pięć minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="on-premises-storage-requirement"></a>Wymagania dotyczące magazynu lokalnego

Arkusz zawiera informacje o łącznym wymaganym wolnym miejscu w magazynie dla każdego woluminu serwerów funkcji Hyper-V (gdzie znajdują się dyski VHD) na potrzeby pomyślnego ukończenia replikacji początkowej i replikacji różnicowej. Przed włączeniem replikacji dodaj wymagane miejsce do magazynowania na woluminach, aby zapewnić, że replikacja nie spowoduje żadnych niepożądanych przestojów aplikacji produkcyjnych. 

  Planista wdrażania usługi Site Recovery identyfikuje optymalne wymagane miejsce do magazynowania w oparciu o rozmiar dysków VHD i przepustowość sieci używaną podczas replikacji.

![Wymagania dotyczące magazynu lokalnego](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Dlaczego trzeba mieć wolne miejsca na serwerze funkcji Hyper-V podczas replikacji?
* Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery tworzy migawkę każdego dysku VHD maszyny wirtualnej na potrzeby replikacji początkowej. W czasie replikacji początkowej nowe zmiany są zapisywane na dyskach przez aplikację. Usługa Site Recovery śledzi te zmiany różnicowe w plikach dziennika, które wymagają dodatkowego miejsca do magazynowania. Do momentu ukończenia replikacji początkowej pliki dziennika są przechowywane lokalnie. 

    Jeśli nie ma wystarczającej ilości miejsca dla plików dziennika i migawki (AVHDX), replikacja przechodzi w tryb ponownej synchronizacji i nigdy nie jest kończona. W najgorszym przypadku potrzeba dodatkowego wolnego miejsca o rozmiarze równym 100 procent rozmiaru dysku VHD na potrzeby replikacji początkowej.
* Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Usługa Site Recovery śledzi te zmiany różnicowe w plikach dziennika przechowywanych na woluminie, na którym znajdują się dyski VHD maszyny wirtualnej. Te pliki dziennika są replikowane na platformie Azure ze skonfigurowaną częstotliwością kopiowania. W zależności od dostępnej przepustowości sieci replikowanie plików dziennika na platformie platformy Azure może potrwać pewien czas. 

    Jeśli nie ma wystarczająco dużo wolnego miejsca do przechowywania plików dziennika, replikacja jest wstrzymywana. Następnie replikacja maszyny wirtualnej przechodzi w stan wymaganej ponownej synchronizacji.
* Jeśli przepustowość sieci jest niewystarczająca do wypychania plików dziennika do platformy Azure, pliki dziennika gromadzą się na woluminie. W najgorszym przypadku, gdy rozmiar plików dziennika zwiększa się do 50 procent rozmiaru dysku VHD, replikacja maszyny wirtualnej przechodzi w stan wymaganej ponownej synchronizacji. W najgorszym przypadku potrzeba dodatkowego wolnego miejsca o rozmiarze równym 50 procent rozmiaru dysku VHD na potrzeby replikacji różnicowej.

**Host funkcji Hyper-V**: Lista profilowanych serwerów funkcji Hyper-V. Jeśli serwer jest częścią klastra funkcji Hyper-V, wszystkie węzły klastra są grupowane razem.

**Wolumin (ścieżka dysku VHD)**: Każdy wolumin hosta funkcji Hyper-V, gdzie znajdują się dyski VHD/Vhdx. 

**Wolne miejsce dostępne (GB)**: Wolne miejsce dostępne na woluminie.

**Łączne miejsce do magazynowania wymagane na woluminie (GB)**: Całkowita ilość wolnego miejsca wymagane miejsce na woluminie na potrzeby pomyślnego zakończenia replikacji początkowej i replikacji różnicowej. 

**Łączna liczba dodatkowego magazynu do aprowizowania na woluminie na potrzeby pomyślnego zakończenia replikacji (GB)**: Zalecane łączne dodatkowe miejsce, które należy aprowizować na woluminie na potrzeby pomyślnego zakończenia replikacji początkowej i replikacji różnicowej.

## <a name="initial-replication-batching"></a>Dzielenie replikacji początkowej na partie 

### <a name="why-do-i-need-initial-replication-batching"></a>Dlaczego należy podzielić replikację początkową na partie?
Jeśli wszystkie maszyny wirtualne są chronione w tym samym czasie, wymaga to dużo więcej wolnego miejsca w magazynie. Jeśli w magazynie nie ma wystarczająco dużo dostępnego miejsca, replikacja maszyn wirtualnych przechodzi w tryb ponownej synchronizacji. Ponadto przepustowość sieci wymagana do pomyślnego ukończenia replikacji początkowej wszystkich maszyn wirtualnych jednocześnie jest znacznie większa. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Dzielenie replikacji początkowej na partie dla wybranego celu punktu odzyskiwania replikacji początkowej
Ten arkusz zawiera szczegółowy widok każdej partii dla replikacji początkowej. Dla każdego celu puntu odzyskiwania jest tworzony oddzielny arkusz dzielenia replikacji początkowej na partie. 

Jeśli zostanie wykonane zalecenie dotyczące wymaganego magazynu lokalnego dla każdego woluminu, główną informacją wymaganą do replikowania będzie lista maszyn wirtualnych, które mogą być chronione równolegle. Te maszyny wirtualne są zgrupowane razem w partii. Może istnieć wiele partii. Chroń maszyny wirtualne w podanej kolejności partii. Najpierw chroń maszyny wirtualne partii 1. Po zakończeniu replikacji początkowej chroń maszyny wirtualne partii 2 itd. Ten arkusz zawiera listę partii i odpowiadających im maszyn wirtualnych. 

![Szczegóły dotyczące dzielenia replikacji początkowej na partie](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Dodatkowe szczegóły dotyczące dzielenia replikacji początkowej na partie](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Każda partia udostępnia następujące informacje: 
**Host funkcji Hyper-V**: Host funkcji Hyper-V maszyny wirtualnej, która ma być chroniony.

**Maszyna wirtualna**: Maszyna wirtualna ma być chroniony. 

**Komentarze**: Jeśli wymagane dla określonego woluminu maszyny wirtualnej jest wykonanie akcji, komentarz znajduje się w tym miejscu. Jeśli na przykład ilość wolnego miejsca dostępnego na woluminie jest niewystarczająca, tekst komentarza jest następujący: „Dodaj dodatkowy magazyn, aby chronić tę maszynę wirtualną”.

**Wolumin (ścieżka dysku VHD)**: Nazwa woluminu, w którym znajdują się wirtualne dyski twarde maszyny Wirtualnej. 

**Wolne miejsce dostępne na woluminie (GB)**: Wolne miejsce dostępne na woluminie dla maszyny Wirtualnej. Podczas obliczania wolnego miejsca dostępnego na woluminach jest uwzględniane miejsce na dysku używane do replikacji różnicowej przez maszyny wirtualne z poprzednich partii, których dyski VHD znajdują się na tym samym woluminie. 

Na przykład maszyny VM1, VM2 i VM3 znajdują się na woluminie E:\VHDpath. Przed replikacją wolne miejsce na woluminie wynosi 500 GB. Maszyna VM1 jest częścią partii 1, maszyna VM2 jest częścią partii 2, a maszyna VM3 jest częścią partii 3. W przypadku maszyny VM1 dostępne wolne miejsce wynosi 500 GB. W przypadku maszyny VM2 dostępne wolne miejsce wynosi 500 — miejsce na dysku wymagane dla replikacji różnicowej maszyny VM1. Jeśli maszyna VM1 wymaga 300 GB miejsca dla replikacji różnicowej, wolne miejsce dostępne dla maszyny VM2 wynosi 500 GB - 300 GB = 200 GB. Podobnie maszyna VM2 wymaga 300 GB dla replikacji różnicowej. Wolne miejsce dostępne dla maszyny VM3 wynosi 200 GB - 300 GB = -100 GB.

**Magazyn na woluminie na potrzeby replikacji początkowej (GB) wymagany**: Wolne miejsce do magazynowania wymagane na woluminie dla maszyny Wirtualnej na potrzeby replikacji początkowej.

**Magazyn na woluminie na potrzeby replikacji różnicowej (GB) wymagany**: Wolne miejsce do magazynowania wymagane na woluminie dla maszyny Wirtualnej na potrzeby replikacji różnicowej.

**Wymagany dodatkowy magazyn oparty na niedoborze umożliwiający uniknięcie niepowodzenia replikacji (GB)**: Dodatkowego miejsca do magazynowania wymagane na woluminie dla maszyny Wirtualnej. Jest to maksymalna wartość wymaganego miejsca do magazynowania na potrzeby replikacji początkowej i replikacji różnicowej pomniejszona o wolne miejsce dostępne na woluminie.

**Minimalna przepustowość wymagana dla replikacji początkowej (MB/s)**: Minimalna przepustowość wymagana na potrzeby replikacji początkowej dla maszyny Wirtualnej.

**Minimalna przepustowość wymagana dla replikacji różnicowej (MB/s)**: Minimalna przepustowość wymagana na potrzeby replikacji różnicowej dla maszyny Wirtualnej.

### <a name="network-utilization-details-for-each-batch"></a>Szczegóły wykorzystania sieci dla każdej partii 
Każda tabela partii zawiera podsumowanie wykorzystania sieci przez partię.

**Przepustowość dostępna dla partii**: Przepustowość dostępna dla partii po uwzględnieniu przepustowości replikacji różnicowej poprzedniej partii.

**Przybliżona przepustowość dostępna dla replikacji początkowej partii**: Przepustowość dostępna dla replikacji początkowej maszyn wirtualnych w partii. 

**Przybliżona przepustowość wykorzystana dla replikacji różnicowej partii**: Przepustowość potrzebna do replikacji różnicowej maszyn wirtualnych w partii. 

**Szacowany czas replikacji początkowej dla partii (gg: mm)**: Szacowany czas replikacji początkowej w godziny: minuty.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [szacowania kosztów](hyper-v-deployment-planner-cost-estimation.md).
