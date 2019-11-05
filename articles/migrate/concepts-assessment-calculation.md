---
title: Obliczenia oceny w Azure Migrate | Microsoft Docs
description: Zawiera omówienie obliczeń oceny w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: hamusa
ms.openlocfilehash: d72e5a6dea8b411b6214e7749b8993f9f5a6e7a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466923"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Obliczenia oceny w Azure Migrate

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń. Śledzi również wystąpienia chmury prywatnej i publicznej na platformie Azure. Centrum oferuje Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ocena serwera to narzędzie w Azure Migrate, które ocenia serwery lokalne pod kątem migracji na platformę Azure. Ten artykuł zawiera informacje o tym, jak są obliczane oceny.

## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Określa lokalizację platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/>Ocena serwera obsługuje obecnie następujące regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Niemcy środkowe, Niemcy północny, Japonia Południowo-Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Indie Południowe, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, US Gov Arizona, US Gov Teksas, US Gov Wirginia, zachodnie stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2Y.
**Typ magazynu** | Określa typ dysków, które mają być używane na potrzeby magazynu na platformie Azure. <br/><br/> W przypadku lokalnego określania rozmiarów można określić typ docelowego dysku magazynującego, który ma być zarządzany przez usługę Premium, SSD w warstwie Standardowa zarządzany lub HDD w warstwie Standardowa. W przypadku ustalania rozmiarów na podstawie wydajności można określić typ docelowego dysku magazynującego jako automatyczne, oparte na warstwie Premium, zarządzane HDD w warstwie Standardowa i zarządzane przez SSD w warstwie Standardowa. Jeśli typ magazynu zostanie określony jako automatyczny, zalecenia dotyczące dysku opierają się na danych wydajności dysków: operacji wejścia/wyjścia na sekundę (IOPS) i przepływności. <br/><br/>W przypadku określenia typu magazynu jako wersji Premium lub standardowej Ocena będzie polecać dyskową jednostkę SKU w wybranym typie magazynu. Jeśli chcesz uzyskać umowę SLA dotyczącą pojedynczej instancji na maszynę wirtualną o 99,9%, możesz określić typ magazynu jako dysk zarządzany w warstwie Premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium. Usługa Azure Migrate obsługuje dyski zarządzane tylko na potrzeby oceny migracji.
**Wystąpienia zarezerwowane (RIs)** | Ta właściwość pomaga określić [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure. Szacowanie kosztów w ocenie potrwają rabaty na konto. Usługi RIs są obecnie obsługiwane tylko w przypadku ofert z opcją płatność zgodnie z rzeczywistym użyciem w Azure Migrate.
**Kryteria ustalania wielkości** | Ustawia kryteria do użycia dla maszyn wirtualnych o *odpowiednim rozmiarze* dla platformy Azure. Możesz wybrać opcję ustalania rozmiaru *na podstawie wydajności* lub rozmiaru maszyn wirtualnych *jako lokalnie* , bez uwzględniania historii wydajności.
**Historia wydajności** | Określa czas, jaki należy wziąć pod uwagę podczas oceniania danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest *oparte na wydajności*.
**Użycie percentyla** | Określa wartość percentylości zestawu próbek wydajności, który ma być brany pod uwagę w przypadku zmiany wielkości liter. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest oparty na wydajności.
**Serie maszyn wirtualnych** | Pozwala określić serie maszyn wirtualnych, które mają być brane pod uwagę w przypadku zmiany rozmiarów. Na przykład jeśli masz środowisko produkcyjne, którego nie planujesz migrować do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię z listy lub serii, a wielkość liter jest wykonywana tylko w wybranej serii.
**Współczynnik komfortu** | Azure Migrate oceny serwera traktuje bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | Wyświetla [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , która jest zarejestrowana. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Przedstawia walutę rozliczeń dla Twojego konta.
**Rabat (%)** | Wyświetla rabat związany z subskrypcją, który otrzymujesz w górnej części oferty platformy Azure. Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne nie będą działać przez 24 godziny na dobę, 7 dni w tygodniu na platformie Azure, możesz określić czas trwania (liczbę dni miesięcznie i liczbę godzin dziennie), dla których będą one uruchomione, a oszacowania kosztów są odpowiednio obsługiwane. Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. Ustawieniem domyślnym jest tak.

## <a name="how-are-assessments-calculated"></a>Jak są obliczane oceny?

Ocena w Azure Migrate oceny serwera jest obliczana przy użyciu metadanych zebranych o serwerach lokalnych. Jeśli źródłem odnajdywania jest import przy użyciu. Plik CSV, ocena jest obliczana przy użyciu metadanych dostarczonych przez użytkownika o serwerach. Obliczenia oceny są obsługiwane w trzech etapach. W przypadku każdego serwera obliczenia oceny rozpoczynają się od analizy przydatności do platformy Azure, a następnie ustalają rozmiar i na koniec miesięczne oszacowanie kosztów. Serwer jest przenoszony do późniejszego etapu tylko wtedy, gdy przejdzie poprzedni. Na przykład jeśli serwer nie może sprawdzić przydatności do platformy Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure, a jego wielkość i koszt nie są wykonywane dla tego serwera.

## <a name="azure-suitability-analysis"></a>Analiza przydatności na platformie Azure

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Ocena serwera ocenia każdą maszynę lokalną pod kątem jej przydatności do migracji na platformę Azure. Przypisuje także każdej ocenianej maszynie do jednej z następujących kategorii przydatności:
- **Gotowe do użycia na platformie Azure**: maszynę można migrować na platformę Azure bez wprowadzania żadnych zmian. Zostanie ona uruchomiona na platformie Azure z pełną pomocą techniczną platformy Azure.
- **Warunkowo gotowy na platformę Azure**: maszyna może zacząć pracę na platformie Azure, ale może nie mieć pełnej pomocy technicznej platformy Azure. Na przykład maszyna, na której działa Starsza wersja systemu Windows Server, nie jest obsługiwana na platformie Azure. Przed przeprowadzeniem migracji tych maszyn na platformę Azure należy zachować ostrożność i postępować zgodnie z zaleceniami związanymi z korygowaniem w celu rozwiązania problemów z gotowością.
- **Nie gotowy na platformę Azure**: maszyna nie zostanie uruchomiona na platformie Azure. Jeśli na przykład maszyna lokalna ma dołączony dysk o pojemności ponad 64 terabajtów (TB), nie może być hostowany na platformie Azure. Przed przeprowadzeniem migracji maszyny do platformy Azure należy postępować zgodnie z zaleceniami dotyczącymi korygowania sugerowanymi w ocenie. Ustalanie wielkości liter i oszacowanie kosztów nie jest wykonywane dla maszyn oznaczonych jako niegotowe dla platformy Azure.
- **Nieznane gotowość**: Azure Migrate nie mógł ustalić gotowości maszyny z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

Ocena serwera przegląduje właściwości maszyny i systemu operacyjnego gościa w celu ustalenia gotowości maszyny lokalnej na platformę Azure.

### <a name="machine-properties"></a>Właściwości maszyny

Ocena serwera sprawdza następujące właściwości lokalnej maszyny wirtualnej w celu określenia, czy można uruchomić ją na platformie Azure.

**Właściwość** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchowym systemu BIOS, a nie z interfejsem UEFI. | Warunkowo gotowe, jeśli typ rozruchu to UEFI.
**Rdzeni** | Liczba rdzeni w maszynach musi być mniejsza lub równa maksymalnej liczbie rdzeni (128) obsługiwanej przez maszynę wirtualną platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate traktuje wykorzystane rdzenie do porównania. Jeśli czynnik komfortu jest określony w ustawieniach oceny, Liczba użytych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, Azure Migrate używa przyznanych rdzeni bez zastosowania współczynnika komfortu. | Gotowe, jeśli jest mniejsze lub równe limitom.
**Rozmiar** | Rozmiar pamięci maszyny musi być równy lub mniejszy niż maksymalna ilość pamięci (3892 gigabajtów [GB] na platformie Azure M Series Standard_M128m&nbsp;<sup>2</sup>), która jest dozwolona dla maszyny wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate uwzględnia wykorzystanie pamięci do porównania. W przypadku określenia współczynnika komfortu wykorzystanie pamięci jest mnożone przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnej historii, przydzieloną pamięć jest używana bez zastosowania współczynnika komfortu.<br/><br/> | Gotowe, jeśli w ramach limitów.
**Dysk magazynu** | Przydzielony rozmiar dysku nie może być mniejszy niż 32 TB. Mimo że platforma Azure obsługuje dyski o pojemności 64 TB z dyskami SSD w warstwie Ultra, Azure Migrate: Ocena serwera obecnie sprawdza dostępność 32 TB miejsca, ponieważ nie są one jeszcze obsługiwane w SSD w warstwie Ultra. <br/><br/> Liczba dysków dołączonych do maszyny musi wynosić co najmniej 65, łącznie z dyskiem systemu operacyjnego. | Gotowe, jeśli w ramach limitów.
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
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Te systemy operacyjne przekazały datę końca okresu obsłudze. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Klient z systemem Windows 7, 8 i 10 | Platforma Azure zapewnia pomoc techniczną tylko w przypadku [subskrypcji programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowa na platformę Azure
Windows 10 Pro Desktop | Platforma Azure zapewnia pomoc techniczną z [wielodostępnymi prawami hostingu.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Te systemy operacyjne przekazały datę końca okresu obsłudze. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Linux | System Azure poświadcza te [systemy operacyjne Linux](../virtual-machines/linux/endorsed-distros.md). Inne systemy operacyjne Linux mogą zaczynać się na platformie Azure, ale zalecamy uaktualnienie systemu operacyjnego do wersji zatwierdzonej przed przeprowadzeniem migracji na platformę Azure. | Gotowe na platformę Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowe, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne<br/><br/> Na przykład Oracle Solaris, Apple Mac OS itp., FreeBSD itp. | Na platformie Azure nie są zatwierdzona te systemy operacyjne. Maszyna może zaczynać się na platformie Azure, ale nie obsługuje systemu operacyjnego. | Warunkowo gotowy na platformę Azure. Zalecamy zainstalowanie obsługiwanego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.  
System operacyjny określony jako **inny** w vCenter Server | W takim przypadku Azure Migrate nie może zidentyfikować systemu operacyjnego. | Nieznane gotowość. Upewnij się, że system operacyjny działający na maszynie wirtualnej jest obsługiwany na platformie Azure.
32-bitowe systemy operacyjne | Maszyna może zaczynać się na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy na platformę Azure. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie systemu operacyjnego maszyny z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego.

## <a name="sizing"></a>Ustalanie rozmiaru

Po oznaczeniu maszyny jako gotowej na platformie Azure Ocena serwera wprowadza zalecenia dotyczące zmiany rozmiarów, co obejmuje zidentyfikowanie odpowiedniej maszyny wirtualnej platformy Azure i jednostki SKU dysku dla lokalnej maszyny wirtualnej. Zalecenia te różnią się w zależności od określonych właściwości oceny.

- Jeśli Ocena korzysta z *ustalania rozmiaru na podstawie wydajności*, Azure Migrate traktuje historię wydajności maszyny w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure. W przypadku serwerów ze źródłem odnajdywania jako Importuj są brane pod uwagę wartości wykorzystania wydajności określone przez użytkownika. Ta metoda jest szczególnie przydatna, jeśli masz nadmiernie przydzieloną lokalną maszynę wirtualną, ale wykorzystanie jest niskie i chcesz zmienić rozmiar maszyny wirtualnej na platformie Azure, aby zaoszczędzić koszty. Ta metoda pomoże zoptymalizować rozmiary podczas migracji.
- Jeśli nie chcesz uwzględniać danych wydajności dotyczących rozmiarów maszyn wirtualnych i chcesz przełączyć maszyny lokalne na platformę Azure, możesz ustawić kryteria ustalania rozmiarów *jako lokalne*. Następnie Ocena serwera spowoduje rozmiary maszyn wirtualnych w oparciu o konfigurację lokalną, bez uwzględniania danych użycia. W takim przypadku działania związane z ustalaniem wielkości dysku są oparte na typie magazynu określonym we właściwościach oceny (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium).

### <a name="performance-based-sizing"></a>Ustalanie wielkości na podstawie wydajności

W przypadku ustalania rozmiarów na podstawie wydajności Ocena serwera rozpoczyna się od dysków podłączonych do maszyny wirtualnej, a następnie kart sieciowych. Następnie mapuje jednostkę SKU maszyny wirtualnej platformy Azure na podstawie wymagań obliczeniowych lokalnej maszyny wirtualnej. Urządzenie Azure Migrate profiluje lokalne środowisko do zbierania danych wydajności dotyczących procesora CPU, pamięci, dysków i karty sieciowej.

**Kroki zbierania danych wydajności:**

1. W przypadku maszyn wirtualnych VMware urządzenie Azure Migrate zbiera punkt próbkowania w czasie rzeczywistym co 20-sekundowy interwał. W przypadku maszyn wirtualnych funkcji Hyper-V punkt próbkowania w czasie rzeczywistym jest zbierany co 30 sekund. W przypadku serwerów fizycznych punkt próbkowania w czasie rzeczywistym jest zbierany co 5-minutowy interwał. 
2. Urządzenie zbiera przykładowe punkty zebrane co 10 minut i wysyła maksymalną wartość dla ostatnich 10 minut do oceny serwera. 
3. W ramach oceny serwera są przechowywane wszystkie 10-minutowe punkty próbkowania przez ostatni miesiąc. Następnie, w zależności od właściwości oceny określonych dla *historii wydajności* i *użycia percentylu*, identyfikuje odpowiedni punkt danych do użycia w celu zmiany wielkości liter. Na przykład jeśli historia wydajności jest ustawiona na 1 dzień, a wykorzystanie percentylu to używany 95. percentyl, Ocena serwera używa 10-minutowych punktów próbkowania w ciągu ostatniego dnia, sortuje je w kolejności rosnącej i wybiera wartość używany 95. percentylu dla odpowiedniego wymiaru. 
4. Ta wartość jest mnożona przez współczynnik komfortu, aby uzyskać efektywne dane użycia wydajności dla każdej metryki (użycie procesora CPU, użycie pamięci, operacje we/wy dysku (odczyt i zapis), przepływność dysku (odczyt i zapis) oraz przepływność sieci (w i wychodząca) zbieranie informacji o urządzeniu.

Po ustaleniu wartości efektywnego wykorzystania magazyn, Sieć i rozmiar obliczeń są obsługiwane w następujący sposób.

> [!NOTE]
> W przypadku serwerów dodanych za pośrednictwem importu dane wydajności dostarczone przez użytkownika są używane bezpośrednio w celu uzyskania odpowiedniego poziomu.

**Rozmiar magazynu**: Azure Migrate próbuje zmapować każdy dysk dołączony do maszyny na dysk na platformie Azure.

> [!NOTE]
> Azure Migrate oceny serwera obsługuje tylko dyski zarządzane do oceny.

  - Ocena serwera umożliwia dodanie operacji odczytu i zapisu na dysku, aby uzyskać łączną liczbę operacji we/wy na sekundę. Podobnie dodaje wartości przepływności odczytu i zapisu w celu uzyskania całkowitej przepływności każdego dysku.
  - Jeśli typ magazynu został określony jako automatyczny, na podstawie wartości liczby operacji we/wy na sekundę, Ocena serwera określa, czy dysk powinien być mapowany na standardowy dysk twardy, standardowy dysk SSD czy w warstwie Premium na platformie Azure. Jeśli typ magazynu jest ustawiony na HDD w warstwie Standardowa/SSD/Premium, Ocena serwera próbuje znaleźć jednostkę SKU dysku w wybranym typie magazynu (dyski HDD w warstwie Standardowa/SSD/Premium).
  - Jeśli Ocena serwera nie może znaleźć dysku z wymaganą liczbą operacji we/wy i przepływności, oznacza to, że maszyna jest nieodpowiednia dla platformy Azure.
  - Jeśli Ocena serwera odnajdzie zestaw odpowiednich dysków, wybiera dyski, które obsługują lokalizację określoną w ustawieniach oceny.
  - Jeśli istnieje wiele kwalifikujących się dysków, Ocena serwera wybiera dysk o najniższym koszcie.
  - Jeśli dane dotyczące wydajności dla dowolnego dysku są niedostępne, dane konfiguracji dysku (rozmiar dysku) są używane w celu znalezienia standardowego dysku SSD na platformie Azure.

**Ustalanie wielkości sieci**: Ocena serwera próbuje znaleźć maszynę wirtualną platformy Azure, która może obsługiwać liczbę kart sieciowych podłączonych do maszyny lokalnej oraz wydajność wymaganą przez te karty sieciowe.
- W celu uzyskania efektywnej wydajności sieci lokalnej maszyny wirtualnej Ocena serwera agreguje dane przesyłane na sekundę (MB/s) z komputera (w sieci) na wszystkich kartach sieciowych i stosuje współczynnik komfortu. Używa tej liczby, aby znaleźć maszynę wirtualną platformy Azure, która może obsługiwać wymaganą wydajność sieci.
- Wraz z wydajnością sieci Ocena serwera uwzględnia również, czy maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
- Jeśli żadne dane wydajności sieci nie są dostępne, Ocena serwera uwzględnia tylko liczbę kart sieciowych na potrzeby ustalania rozmiarów maszyn wirtualnych.

> [!NOTE]
> Określanie liczby kart sieciowych nie jest obecnie obsługiwane w przypadku importowanych serwerów

**Ustalanie rozmiaru obliczeń**: po obliczeniu wymagań dotyczących magazynu i sieci Ocena serwera uwzględnia wymagania dotyczące procesora CPU i pamięci, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.
- Azure Migrate analizuje efektywne wykorzystanie rdzeni i pamięci, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.
- Jeśli odpowiedni rozmiar nie zostanie znaleziony, oznacza to, że komputer jest oznaczony jako nieodpowiedni dla platformy Azure.
- Jeśli zostanie znaleziony odpowiedni rozmiar, Azure Migrate stosuje obliczenia magazynu i sieci. Następnie stosuje ustawienia lokalizacji i warstwy cenowej dla ostatecznego zalecenia dotyczącego rozmiaru maszyny wirtualnej.
- Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

### <a name="as-on-premises-sizing"></a>Zgodnie z rozmiarem lokalnym

Jeśli używasz *jako lokalizacji lokalnej*, Ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków. Zamiast tego przypisuje jednostkę SKU maszyny wirtualnej na platformie Azure na podstawie rozmiaru przydzielonego lokalnie. Podobnie w przypadku określania wielkości dysku Ocena serwera sprawdza typ magazynu określony we właściwościach oceny (HDD w warstwie Standardowa/SSD/Premium) i zaleca odpowiedni typ dysku. Domyślny typ magazynu to dyski Premium.

## <a name="confidence-ratings"></a>Klasyfikacje zaufania
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

### <a name="low-confidence-ratings"></a>Oceny o niskiej pewności

Poniżej przedstawiono kilka powodów, dla których ocena może uzyskać klasyfikację o niskiej pewności:

- Twoje środowisko nie było profilem przez czas, w którym tworzysz ocenę. Jeśli na przykład utworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej dzień po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Niektóre maszyny wirtualne zostały wyłączone w okresie, dla którego została obliczona Ocena. Jeśli wszystkie maszyny wirtualne są wyłączone przez pewien czas, Ocena serwera nie może zebrać danych wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w okresie, dla którego została obliczona Ocena. Na przykład jeśli utworzono ocenę dla historii wydajności w ostatnim miesiącu, ale niektóre maszyny wirtualne zostały utworzone w danym środowisku tylko tydzień temu, historia wydajności nowych maszyn wirtualnych nie będzie miała na cały czas trwania.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest mniejsza niż pięć gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli tego nie zrobisz, ustalanie rozmiarów na podstawie wydajności może być niezawodne. W takim przypadku zaleca się przełączenie oceny do rozmiarów lokalnych.

## <a name="monthly-cost-estimation"></a>Oszacowanie kosztu miesięcznego

Po zakończeniu ustalania rozmiarów Azure Migrate oblicza koszty obliczeń i magazynowania po migracji.

- **Koszt obliczeń**: przy użyciu zalecanego rozmiaru maszyny wirtualnej platformy Azure, Azure Migrate korzysta z interfejsu API rozliczeń, aby obliczyć miesięczny koszt maszyny wirtualnej.
    - Obliczanie wymaga systemu operacyjnego, programu Software Assurance, wystąpień zarezerwowanych, czasu działania maszyny wirtualnej, lokalizacji i ustawień waluty.
    - Agreguje koszt na wszystkich maszynach, aby obliczyć łączny koszt obliczeń miesięcznych.
- **Koszt magazynu**: miesięczny koszt magazynu dla maszyny jest obliczany przez zsumowanie miesięcznego kosztu wszystkich dysków dołączonych do maszyny w następujący sposób:
    - Ocena serwera oblicza łączne miesięczne koszty magazynowania, sumując koszty magazynowania wszystkich maszyn.
    - Obecnie obliczenia nie uwzględniają ofert określonych w ustawieniach oceny.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Następne kroki

Utwórz ocenę dla [maszyn wirtualnych VMware](tutorial-assess-vmware.md) lub [maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md).
