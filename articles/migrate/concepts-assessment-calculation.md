---
title: Oceny w Azure Migrate
description: Dowiedz się więcej na temat ocen w Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0cf933dd1c8c61edfcea20ea954c5813f3848b28
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425701"
---
# <a name="about-assessments-in-azure-migrate"></a>Informacje o ocenach w Azure Migrate

W tym artykule opisano sposób obliczania ocen w [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool). Można uruchamiać oceny w grupach maszyn lokalnych, aby ustalić, czy są one gotowe do migracji do Azure Migrate.

## <a name="how-do-i-run-an-assessment"></a>Jak mogę uruchomić ocenę?
Ocenę można wykonać przy użyciu Azure Migrate: Ocena serwera lub inne narzędzie platformy Azure lub innych firm. Po utworzeniu projektu Azure Migrate należy dodać potrzebne narzędzie. [Dowiedz się więcej](how-to-add-tool-first-time.md)

### <a name="collect-compute-data"></a>Zbieranie danych obliczeniowych

Dane dotyczące wydajności dla ustawień obliczeń są zbierane w następujący sposób:

1. [Urządzenie Azure Migrate](migrate-appliance.md) zbiera punkt próbkowania w czasie rzeczywistym:

    - **Maszyny wirtualne VMware**: w przypadku maszyn wirtualnych vmware urządzenie Azure Migrate zbiera punkt próbkowania w czasie rzeczywistym co 20 sekund.
    - **Maszyny wirtualne funkcji Hyper-v**: w przypadku maszyn wirtualnych funkcji Hyper-v punkt próbkowania w czasie rzeczywistym jest zbierany co 30 sekund.
    - **Serwery fizyczne**: w przypadku serwerów fizycznych, punkt próbkowania w czasie rzeczywistym jest zbierany co pięć minut. 
    
2. Urządzenie zbiera punkty próbkowania (20 sekund, 30 sekund, pięć minut), aby utworzyć jeden punkt danych co 10 minut. Aby utworzyć pojedynczy punkt danych, urządzenie wybiera wartość szczytu ze wszystkich próbek, a następnie wysyła je do platformy Azure.
3. W ramach oceny serwera są przechowywane wszystkie 10-minutowe punkty próbkowania w ciągu ostatniego miesiąca.
4. Podczas tworzenia oceny Ocena serwera identyfikuje odpowiedni punkt danych do użycia dla odpowiedniego wymiaru, na podstawie wartości percentyla dla *historii wydajności* i *użycia percentylu*.

    - Na przykład jeśli historia wydajności jest ustawiona na jeden tydzień, a użycie percentyla to używany 95. percentyl, Ocena serwera sortuje 10-minutowe punkty próbkowania dla ostatniego tygodnia w kolejności rosnącej, a następnie wybiera wartość percentylu używany 95. dla odpowiedniego wymiaru. 
    - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
    - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, należy wybrać 99 percentyl dla użycia percentylu.

5. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla każdej metryki (użycie procesora CPU, użycie pamięci, operacje we/wy dysku (odczyt i zapis), przepływność dysku (odczyt i zapis) oraz przepływność sieci (w i wychodząca) zbieranie informacji o urządzeniu.

Aby uruchamiać oceny w ocenie serwera, należy przygotować się do oceny w środowisku lokalnym i na platformie Azure, a następnie skonfigurować urządzenie Azure Migrate do ciągłego odnajdywania maszyn lokalnych. Po odnalezieniu maszyn należy zebrać je do grup, aby je ocenić. Aby uzyskać bardziej szczegółowe i niebezpieczne oceny, można wizualizować i mapować zależności między maszynami, aby dowiedzieć się, jak przeprowadzić migrację.

- Dowiedz się więcej o uruchamianiu ocen dla [maszyn wirtualnych VMware](tutorial-prepare-vmware.md), [maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md)i [serwerów fizycznych](tutorial-prepare-physical.md).
- Dowiedz się więcej na temat oceniania serwerów [zaimportowanych przy użyciu pliku CSV](tutorial-assess-import.md).
- Dowiedz się więcej na temat konfigurowania [wizualizacji zależności](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Oceny w ocenie serwera 

Oceny tworzone przy użyciu oceny Azure Migrate Server są migawką danych w danym momencie. Narzędzie do oceny serwera oferuje dwa typy ocen.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny, które podejmują zalecenia na podstawie zebranych danych wydajności | Zalecenie dotyczące rozmiaru maszyny wirtualnej bazuje na danych użycia procesora i pamięci.<br/><br/> Zalecenia dotyczące typu dysku (standardowy dysk twardy/SSD lub dyski zarządzane w warstwie Premium) jest oparty na liczbie operacji we/wy na sekundę i przepływności dysku lokalnego.
**Zgodnie z lokalnym** | Oceny, które nie wykorzystują danych wydajności, aby wykonać zalecenia. | Zalecenie dotyczące rozmiaru maszyny wirtualnej bazuje na lokalnym rozmiarze maszyny wirtualnej<br/><br> Zalecany typ dysku jest oparty na wybranym typie magazynu dla oceny.

## <a name="collecting-performance-data"></a>Zbieranie danych wydajności

Dane dotyczące wydajności są zbierane w następujący sposób:

1. [Urządzenie Azure Migrate](migrate-appliance.md) zbiera punkt próbkowania w czasie rzeczywistym:

    - **Maszyny wirtualne VMware**: w przypadku maszyn wirtualnych vmware urządzenie Azure Migrate zbiera punkt próbkowania w czasie rzeczywistym co 20 sekund.
    - **Maszyny wirtualne funkcji Hyper-v**: w przypadku maszyn wirtualnych funkcji Hyper-v punkt próbkowania w czasie rzeczywistym jest zbierany co 30 sekund.
    - **Serwery fizyczne**: w przypadku serwerów fizycznych, punkt próbkowania w czasie rzeczywistym jest zbierany co pięć minut. 
    
2. Urządzenie zbiera punkty próbkowania (20 sekund, 30 sekund, pięć minut), aby utworzyć jeden punkt danych co 10 minut. Aby utworzyć pojedynczy punkt danych, urządzenie wybiera wartość szczytu ze wszystkich próbek, a następnie wysyła je do platformy Azure.
3. W ramach oceny serwera są przechowywane wszystkie 10-minutowe punkty próbkowania w ciągu ostatniego miesiąca.
4. Podczas tworzenia oceny Ocena serwera identyfikuje odpowiedni punkt danych do użycia dla odpowiedniego wymiaru, na podstawie wartości percentyla dla *historii wydajności* i *użycia percentylu*.

    - Na przykład jeśli historia wydajności jest ustawiona na jeden tydzień, a użycie percentyla to używany 95. percentyl, Ocena serwera sortuje 10-minutowe punkty próbkowania dla ostatniego tygodnia w kolejności rosnącej, a następnie wybiera wartość percentylu używany 95. dla odpowiedniego wymiaru. 
    - Wartość percentylu używany 95. gwarantuje, że zignorujesz wszelkie elementy odstające, które mogą zostać uwzględnione w przypadku wybrania 99 percentylu.
    - Jeśli chcesz wybrać szczytowe użycie dla tego okresu i nie chcesz pominąć żadnych wartości odstających, należy wybrać 99 percentyl dla użycia percentylu.

5. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla każdej metryki (użycie procesora CPU, użycie pamięci, operacje we/wy dysku (odczyt i zapis), przepływność dysku (odczyt i zapis) oraz przepływność sieci (w i wychodząca) zbieranie informacji o urządzeniu.
## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

Oto co obejmuje Ocena w Azure Migrate: Ocena serwera.

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja, do której chcesz przeprowadzić migrację. Ocena serwera obsługuje obecnie następujące docelowe regiony platformy Azure:<br/><br/> Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Niemcy środkowe, Niemcy Południowe, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa Środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Indie Południowe, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, US Gov Arizona, US Gov Teksas, US Gov Wirginia, zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2.
*Docelowy dysk pamięci masowej (w postaci wielkości liter)* * | Typ dysków, które mają być używane na potrzeby magazynu na platformie Azure. <br/><br/> Określ docelowy dysk pamięci masowej w postaci zarządzanej, standardowej dysku SSD zarządzanej lub standardowej zarządzanej przestrzeni DYSKowej.
**Docelowy dysk magazynujący (ustalanie wielkości na podstawie wydajności)** | Określ typ docelowego dysku magazynującego w postaci automatycznej, zarządzanej w warstwie Premium, standardowej zarządzanej pamięci DYSKowej lub standardowej zarządzanej pamięci SSD.<br/><br/> **Automatyczne**: zalecenie dysku opiera się na danych wydajności dysków (operacji wejścia/wyjścia na sekundę (IOPS) i przepływności.<br/><br/>**Premium/Standard**: Ocena zaleca użycie jednostki SKU dysku w wybranym typie magazynu.<br/><br/> Jeśli chcesz uzyskać umowę SLA dotyczącą pojedynczej instancji na maszynę wirtualną o 99,9%, rozważ użycie dysków zarządzanych w warstwie Premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium.<br/><br/> Usługa Azure Migrate obsługuje tylko dyski zarządzane na potrzeby oceny migracji.
**Wystąpienia zarezerwowane (RIs)** | Określ [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, dzięki czemu szacunki kosztów w ramach oceny są uwzględniane w ramach rabatu.<br/><br/> Usługi RIs są obecnie obsługiwane tylko w przypadku ofert z opcją płatność zgodnie z rzeczywistym użyciem w Azure Migrate.
**Kryteria ustalania wielkości** | Służy do odpowiedniego rozmiaru maszyny wirtualnej na platformie Azure.<br/><br/> Używaj wielkości liter lub wielkości na podstawie wydajności.
**Historia wydajności** | Używane z rozmiarem opartym na wydajności. Określ czas trwania używany podczas oceny danych wydajności.
**Użycie percentyla** | Używane z rozmiarem opartym na wydajności. Określa wartość percentylu przykładu wydajności, która ma być używana w przypadku zmiany wielkości liter. 
**Serie maszyn wirtualnych** | Określ serię maszyn wirtualnych platformy Azure, którą chcesz wziąć pod uwagę w przypadku zmiany rozmiarów. Na przykład jeśli nie masz środowiska produkcyjnego wymagającego maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię z listy lub serii.
**Współczynnik komfortu** | Bufor używany podczas oceny. Stosowane na podstawie danych użycia maszyny dla maszyn wirtualnych (procesor CPU, pamięć, dysk i sieć). W przypadku problemów takich jak użycie sezonowe, krótka historia wydajności i prawdopodobnie rośnie w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna z wykorzystaniem 20% zwykle skutkuje dwurdzeniową maszyną wirtualną. W przypadku współczynnika komfortu wynoszącego 2,0 x w zamian powstaje wielordzeniowa maszyna wirtualna.
**Oferta** | Wyświetla [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , w której zarejestrowano. Ocena serwera szacuje odpowiednio koszt.
**Waluta** | Waluta rozliczeniowa dla Twojego konta.
**Rabat (%)** | Wyświetla wszystkie zniżki związane z subskrypcją, które otrzymujesz w ramach oferty platformy Azure. Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne platformy Azure nie będą działać przez 24 godziny na dobę, 7 dni w tygodniu, możesz określić czas trwania (w dniach miesięcznie i godzinach dziennie), które będą uruchamiane. Oszacowania kosztów są odpowiednio obsługiwane.<br/><br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji tak (ustawienie domyślne) ceny za maszyny wirtualne z systemem Windows są brane pod uwagę w przypadku niezwiązanych z systemem Windows Azure.

[Zapoznaj się z najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen przy użyciu oceny serwera.

## <a name="how-are-assessments-calculated"></a>Jak są obliczane oceny? 

Oceny w Azure Migrate: Ocena serwera jest obliczana przy użyciu metadanych zebranych o maszynach lokalnych. W przypadku uruchamiania oceny na maszynach zaimportowanych przy użyciu programu. Plik CSV udostępnia metadane do obliczenia. Obliczenia są wykonywane na trzech etapach:

1. **Oblicz gotowość platformy Azure**: Oceń, czy maszyny są odpowiednie do migracji na platformę Azure.
2. **Obliczanie zaleceń dotyczących ustalania rozmiaru**: oszacowanie obliczeń, magazynu i rozmiaru sieci. 
2. **Oblicz miesięczne koszty**: Oblicz szacowane miesięczne koszty obliczeń i magazynowania na potrzeby uruchamiania maszyn na platformie Azure po migracji.

Obliczenia są w kolejności, a serwer maszynowy przechodzi do późniejszego etapu tylko wtedy, gdy przejdzie poprzednią. Jeśli na przykład serwer ulegnie awarii na potrzeby gotowości platformy Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure, a jego wielkość i koszt nie są wykonywane dla tego serwera.



## <a name="calculate-readiness"></a>Oblicz gotowość

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Ocena serwera ocenia każdą maszynę lokalną i przypisuje jej kategorię gotowości. 
- **Gotowe do użycia na platformie Azure**: maszynę można migrować na platformę Azure bez wprowadzania żadnych zmian. Zostanie ona uruchomiona na platformie Azure z pełną pomocą techniczną platformy Azure.
- **Warunkowo gotowy na platformę Azure**: maszyna może zacząć korzystać z platformy Azure, ale może nie mieć pełnej pomocy technicznej platformy Azure. Na przykład maszyna, na której działa Starsza wersja systemu Windows Server, nie jest obsługiwana na platformie Azure. Przed przeprowadzeniem migracji tych maszyn na platformę Azure należy zachować ostrożność. Postępuj zgodnie ze wskazówkami dotyczącymi korygowania proponowanymi w ocenie, aby rozwiązać problemy z gotowością.
- **Nie gotowy na platformę Azure**: maszyna nie zostanie uruchomiona na platformie Azure. Na przykład jeśli dysk maszyny lokalnej ma więcej niż 64 usług TBs, nie może być hostowany na platformie Azure. Postępuj zgodnie ze wskazówkami dotyczącymi korygowania, aby rozwiązać problem przed migracją. 
- **Nieznane gotowość**: Azure Migrate nie mógł ustalić gotowości maszyny z powodu niewystarczających metadanych.

Aby obliczyć gotowość, Ocena serwera sprawdza właściwości komputera i ustawienia systemu operacyjnego podsumowane w poniższych tabelach. 

### <a name="machine-properties"></a>Właściwości maszyny

Ocena serwera sprawdza następujące właściwości lokalnej maszyny wirtualnej w celu określenia, czy można uruchomić ją na platformie Azure.

**Właściwość** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchowym systemu BIOS, a nie z interfejsem UEFI. | Warunkowo gotowe, jeśli typ rozruchu to UEFI.
**Rdzeni** | Liczba rdzeni w maszynach musi być mniejsza lub równa maksymalnej liczbie rdzeni (128) obsługiwanej przez maszynę wirtualną platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate traktuje wykorzystane rdzenie do porównania. Jeśli czynnik komfortu jest określony w ustawieniach oceny, Liczba użytych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, Azure Migrate używa przyznanych rdzeni bez zastosowania współczynnika komfortu. | Gotowe, jeśli jest mniejsze lub równe limitom.
**Rozmiar** | Rozmiar pamięci maszyny musi być równy lub mniejszy niż maksymalna ilość pamięci (3892 gigabajtów [GB] na platformie Azure M Series Standard_M128m&nbsp;<sup>2</sup>), która jest dozwolona dla maszyny wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate uwzględnia wykorzystanie pamięci do porównania. W przypadku określenia współczynnika komfortu wykorzystanie pamięci jest mnożone przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnej historii, przydzieloną pamięć jest używana bez zastosowania współczynnika komfortu.<br/><br/> | Gotowe, jeśli w ramach limitów.
**Dysk magazynu** | Przydzielony rozmiar dysku nie może być mniejszy niż 32 TB. Mimo że platforma Azure obsługuje dyski 64 TB z dyskami SSD w warstwie Ultra, Azure Migrate: Ocena serwera obecnie sprawdza, czy rozmiar dysku wynosi 32 TB, ponieważ nie obsługuje jeszcze SSD w warstwie Ultra. <br/><br/> Liczba dysków dołączonych do maszyny musi wynosić co najmniej 65, łącznie z dyskiem systemu operacyjnego. | Gotowe, jeśli w ramach limitów.
**Sieć** | Komputer musi mieć dołączoną 32 lub mniej interfejsów sieciowych (nic). | Gotowe, jeśli w ramach limitów.

### <a name="guest-operating-system"></a>System operacyjny gościa
Wraz z właściwościami maszyny wirtualnej Ocena serwera sprawdza system operacyjny gościa maszyn, aby określić, czy może on działać na platformie Azure.

> [!NOTE]
> W przypadku maszyn wirtualnych VMware Ocena serwera używa systemu operacyjnego określonego dla maszyny wirtualnej w vCenter Server do obsługi analizy systemu operacyjnego gościa. W przypadku maszyn wirtualnych z systemem Linux działających w oprogramowaniu VMware obecnie nie jest dostępna dokładna wersja jądra systemu operacyjnego gościa.

Następująca logika jest używana przez ocenę serwera do identyfikowania gotowości platformy Azure w oparciu o system operacyjny.

**System operacyjny** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
Windows Server 2016 & wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 R2 & wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 & wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2008 R2 ze wszystkimi usługami SPs | Platforma Azure oferuje pełną pomoc techniczną.| Gotowa na platformę Azure
Windows Server 2008 (32-bitowe i 64-bit) | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2003, 2003 R2 | Te systemy operacyjne przekazały datę końca okresu obsłudze i wymagają [niestandardowej umowy pomocy technicznej (CSA)](https://aka.ms/WSosstatement) w celu uzyskania pomocy technicznej na platformie Azure. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Te systemy operacyjne przekazały datę końca okresu obsłudze. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Klient z systemem Windows 7, 8 i 10 | Platforma Azure zapewnia pomoc techniczną tylko w przypadku [subskrypcji programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowa na platformę Azure
Windows 10 Pro Desktop | Platforma Azure zapewnia pomoc techniczną z [wielodostępnymi prawami hostingu.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Te systemy operacyjne przekazały datę końca okresu obsłudze. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Linux | System Azure poświadcza te [systemy operacyjne Linux](../virtual-machines/linux/endorsed-distros.md). Inne systemy operacyjne Linux mogą zaczynać się na platformie Azure, ale zalecamy uaktualnienie systemu operacyjnego do wersji zatwierdzonej przed przeprowadzeniem migracji na platformę Azure. | Gotowe na platformę Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowe, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne<br/><br/> Na przykład Oracle Solaris, Apple macOS itp., FreeBSD itp. | Na platformie Azure nie są zatwierdzona te systemy operacyjne. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy zainstalowanie obsługiwanego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.  
System operacyjny określony jako **inny** w vCenter Server | W takim przypadku Azure Migrate nie może zidentyfikować systemu operacyjnego. | Nieznane gotowość. Upewnij się, że system operacyjny działający na maszynie wirtualnej jest obsługiwany na platformie Azure.
32-bitowe systemy operacyjne | Maszyna może zaczynać się na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego maszyny z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego.

## <a name="calculate-sizing-as-is-on-premises"></a>Obliczanie rozmiaru (w trybie lokalnym)

Po oznaczeniu maszyny jako gotowej na platformie Azure Ocena serwera sprawia, że zalecenia dotyczące zmiany rozmiarów umożliwiają zidentyfikowanie maszyny wirtualnej platformy Azure i jednostki SKU dysku. Jeśli używasz jako lokalizacji lokalnej, Ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków.

**Ustalanie rozmiaru obliczeń**: przydziela jednostki SKU maszyny wirtualnej platformy Azure na podstawie rozmiaru przydzielonego lokalnie.
**Rozmiar magazynu/dysku**: Ocena serwera sprawdza typ magazynu określony we właściwościach oceny (standardowy dysk twardy/SSD/Premium) i zaleca odpowiedni typ dysku. Domyślny typ magazynu to dyski Premium.
**Ustalanie wielkości sieci**: Ocena serwera traktuje kartę sieciową na komputerze lokalnym.


## <a name="calculate-sizing-performance-based"></a>Obliczanie rozmiaru (opartego na wydajności)

Gdy maszyna zostanie oznaczona jako gotowa na platformie Azure, w przypadku korzystania z ustalania rozmiarów w oparciu o wydajność Ocena serwera w następujący sposób:

- Ocena serwera traktuje historię wydajności maszyny w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure.
- Jeśli serwery zostały zaimportowane przy użyciu pliku CSV, podane wartości są używane. Ta metoda jest szczególnie przydatna, jeśli masz nadmiernie przydzieloną maszynę lokalną, wykorzystanie jest w rzeczywistości niską i chcesz uzyskać odpowiednie rozmiary maszyny wirtualnej na platformie Azure, aby zaoszczędzić koszty. 
- Jeśli nie chcesz używać danych dotyczących wydajności, zresetuj kryteria ustalania wielkości tak, aby były lokalne, zgodnie z opisem w poprzedniej sekcji.

### <a name="calculate-storage-sizing"></a>Oblicz rozmiar magazynu

W przypadku określania wielkości magazynu Azure Migrate próbuje zmapować każdy dysk dołączony do maszyny na dysk na platformie Azure i działa w następujący sposób:

1. Ocena serwera umożliwia dodanie operacji odczytu i zapisu na dysku, aby uzyskać łączną liczbę operacji we/wy na sekundę. Podobnie dodaje wartości przepływności odczytu i zapisu w celu uzyskania całkowitej przepływności każdego dysku.
2. Jeśli typ magazynu został określony jako automatyczny, na podstawie efektywnych wartości IOPS i przepływności, funkcja oceny serwera określa, czy dysk powinien być mapowany na standardowy dysk twardy, standardowy dysk SSD lub dysku Premium na platformie Azure. Jeśli typ magazynu jest ustawiony na HDD w warstwie Standardowa/SSD/Premium, Ocena serwera próbuje znaleźć jednostkę SKU dysku w wybranym typie magazynu (dyski HDD w warstwie Standardowa/SSD/Premium).
3. Dyski są wybierane w następujący sposób:
    - Jeśli Ocena serwera nie może znaleźć dysku z wymaganą liczbą operacji we/wy i przepływności, oznacza to, że maszyna jest nieodpowiednia dla platformy Azure.
    - Jeśli Ocena serwera odnajdzie zestaw odpowiednich dysków, wybiera dyski, które obsługują lokalizację określoną w ustawieniach oceny.
    - Jeśli istnieje wiele kwalifikujących się dysków, Ocena serwera wybiera dysk o najniższym koszcie.
    - Jeśli dane dotyczące wydajności dla dowolnego dysku są niedostępne, dane konfiguracji dysku (rozmiar dysku) są używane w celu znalezienia standardowego dysku SSD na platformie Azure.

### <a name="calculate-network-sizing"></a>Oblicz rozmiar sieci

Ocena serwera próbuje znaleźć maszynę wirtualną platformy Azure, która może obsługiwać liczbę kart sieciowych podłączonych do maszyny lokalnej oraz wydajność wymaganą przez te karty sieciowe.
- W celu uzyskania efektywnej wydajności sieci lokalnej maszyny wirtualnej Ocena serwera agreguje dane przesyłane na sekundę (MB/s) z komputera (w sieci) na wszystkich kartach sieciowych i stosuje współczynnik komfortu. Używa tej liczby, aby znaleźć maszynę wirtualną platformy Azure, która może obsługiwać wymaganą wydajność sieci.
- Wraz z wydajnością sieci Ocena serwera uwzględnia również, czy maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
- Jeśli żadne dane wydajności sieci nie są dostępne, Ocena serwera uwzględnia tylko liczbę kart sieciowych na potrzeby ustalania rozmiarów maszyn wirtualnych.


### <a name="calculate-compute-sizing"></a>Oblicz rozmiar obliczeń

Po obliczeniu wymagań dotyczących magazynu i sieci Ocena serwera uwzględnia wymagania dotyczące procesora i pamięci, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.
- Azure Migrate analizuje efektywne wykorzystanie rdzeni i pamięci, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.
- Jeśli odpowiedni rozmiar nie zostanie znaleziony, oznacza to, że komputer jest oznaczony jako nieodpowiedni dla platformy Azure.
- Jeśli zostanie znaleziony odpowiedni rozmiar, Azure Migrate stosuje obliczenia magazynu i sieci. Następnie stosuje ustawienia lokalizacji i warstwy cenowej dla ostatecznego zalecenia dotyczącego rozmiaru maszyny wirtualnej.
- Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.


### <a name="calculate-confidence-ratings"></a>Oblicz klasyfikacje zaufania

Każda Ocena oparta na wydajności w Azure Migrate jest skojarzona z oceną zaufania z zakresu od jednej (najniższej) do pięciu gwiazdek (najwyższa).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Klasyfikacje zaufania nie mają zastosowania w przypadku ocen *lokalnych* .
- W przypadku ustalania wielkości na podstawie wydajności, wymagania dotyczące oceny serwera:
    - Dane użycia procesora CPU i pamięci maszyn wirtualnych.
    - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do maszyny wirtualnej.
    - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

   Jeśli którykolwiek z tych numerów wykorzystania jest niedostępny w vCenter Server, zalecenie dotyczące rozmiaru może być niezawodne.

W zależności od wartości procentowej dostępnych punktów danych Ocena zaufania dla oceny przebiega w następujący sposób.

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0-20% | 1 gwiazdka
   21-40% | 2 gwiazdki
   41-60% | 3 gwiazdki
   61-80% | 4 gwiazdki
   81-100% | 5 gwiazdek

> [!NOTE]
> Klasyfikacje zaufania nie są przypisywane do ocen serwerów zaimportowanych przy użyciu programu. Plik CSV do Azure Migrate. 

#### <a name="low-confidence-ratings"></a>Oceny o niskiej pewności

Poniżej przedstawiono kilka powodów, dla których ocena może uzyskać klasyfikację o niskiej pewności:

- Twoje środowisko nie było profilem przez czas, w którym tworzysz ocenę. Jeśli na przykład utworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Niektóre maszyny wirtualne zostały wyłączone w okresie, dla którego została obliczona Ocena. Jeśli wszystkie maszyny wirtualne są wyłączone przez pewien czas, Ocena serwera nie może zebrać danych wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w okresie, dla którego została obliczona Ocena. Na przykład jeśli utworzono ocenę dla historii wydajności w ostatnim miesiącu, ale niektóre maszyny wirtualne zostały utworzone w danym środowisku tylko tydzień temu, historia wydajności nowych maszyn wirtualnych nie będzie miała na cały czas trwania.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli tego nie zrobisz, ustalanie rozmiarów na podstawie wydajności może być niezawodne. W takim przypadku zaleca się przełączenie oceny do rozmiarów lokalnych.

## <a name="calculate-monthly-costs"></a>Oblicz miesięczne koszty

Po zakończeniu ustalania rozmiarów Azure Migrate oblicza koszty obliczeń i magazynowania po migracji.

- **Koszt obliczeń**: przy użyciu zalecanego rozmiaru maszyny wirtualnej platformy Azure, Azure Migrate korzysta z interfejsu API rozliczeń, aby obliczyć miesięczny koszt maszyny wirtualnej.
    - Obliczanie wymaga systemu operacyjnego, programu Software Assurance, wystąpień zarezerwowanych, czasu działania maszyny wirtualnej, lokalizacji i ustawień waluty.
    - Agreguje koszt na wszystkich maszynach, aby obliczyć łączny koszt obliczeń miesięcznych.
- **Koszt magazynu**: miesięczny koszt magazynu dla maszyny jest obliczany przez zsumowanie miesięcznego kosztu wszystkich dysków dołączonych do maszyny w następujący sposób:
    - Ocena serwera oblicza łączne miesięczne koszty magazynowania, sumując koszty magazynowania wszystkich maszyn.
    - Obecnie obliczenia nie uwzględniają ofert określonych w ustawieniach oceny.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Następne kroki

[Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen. 
