---
title: Obliczenia oceny w Azure Migrate | Microsoft Docs
description: Zawiera omówienie obliczeń oceny w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234291"
---
# <a name="assessment-calculations"></a>Obliczenia dotyczące oceny

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz wystąpień chmury prywatnej/publicznej na platformę Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ocena serwera to narzędzie w Azure Migrate, które ocenia serwery lokalne pod kątem migracji na platformę Azure. Ten artykuł zawiera informacje o tym, jak są obliczane oceny.

## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

**Property** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/> Ocena serwera obsługuje obecnie następujące regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Niemcy środkowe, Niemcy Południowe, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa Środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Indie Południowe, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, US Gov Arizona, US Gov Teksas, US Gov Wirginia, zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2.
**Typ magazynu** | Za pomocą tej właściwości można określić typ dysków, na które chcesz przenieść na platformę Azure. <br/><br/> W przypadku ustalania rozmiarów lokalnych można określić docelowy typ magazynu jako dyski zarządzane w warstwie Premium, dyski zarządzane SSD w warstwie Standardowa lub dyski zarządzane HDD w warstwie Standardowa. W przypadku ustalania rozmiarów na podstawie wydajności można określić typ dysku docelowego w postaci dysków zarządzanych w warstwie Premium, dysków zarządzanych przez program HDD w warstwie Standardowa lub dysków zarządzanych przy użyciu SSD w warstwie Standardowa.<br/><br/> Jeśli typ magazynu zostanie określony jako automatyczny, zalecenia dotyczące dysku są wykonywane na podstawie danych wydajności dysków (IOPS i przepływności). W przypadku określenia typu magazynu jako warstwy Premium/standardowa Ocena będzie polecać dyskową jednostkę SKU w wybranym typie magazynu. Jeśli chcesz uzyskać umowę SLA dotyczącą pojedynczej instancji na maszynę wirtualną o 99,9%, możesz określić typ magazynu jako dysk zarządzany w warstwie Premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium. Usługa Azure Migrate obsługuje dyski zarządzane tylko na potrzeby oceny migracji.
**Wystąpienia zarezerwowane (RI)** | Ta właściwość pozwala określić, czy istnieją [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, szacowania kosztów w ocenie są następnie wykonywane z uwzględnieniem zniżek na RI. Wystąpienia zarezerwowane są obecnie obsługiwane tylko w przypadku oferty z opcją płatność zgodnie z rzeczywistym użyciem w Azure Migrate.
**Kryterium rozmiaru** | Kryterium, które ma być używane dla maszyn wirtualnych o odpowiednim rozmiarze dla platformy Azure. Można albo zmienić rozmiar maszyny wirtualnej na *podstawie wydajności* , jak i *w ten sposób*, bez uwzględniania historii wydajności.
**Historia wydajności** | Czas, który należy wziąć pod uwagę przy ocenie danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest *oparte na wydajności*.
**Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest *oparty na wydajności*.
**Serie maszyn wirtualnych** |     Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Na przykład jeśli masz środowisko produkcyjne, które nie jest planowane do migracji na maszyny wirtualne z serii A na platformie Azure, możesz wykluczyć serię z listy lub serii, a po prawej stronie jest wykonywane tylko w wybranej serii.
**Współczynnik komfortu** | Azure Migrate oceny serwera traktuje bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której dokonano rejestracji. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Twoja waluta rozliczeniowa.
**Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure.<br/> Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne nie będą działały 24x7 na platformie Azure, możesz określić czas trwania (liczbę dni miesięcznie i liczbę godzin dziennie), dla których byłyby uruchomione, a oszacowania kosztów zostaną odpowiednio wykonane.<br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określ, czy masz program Software Assurance i czy masz uprawnienia do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. Wartość domyślna to Yes.

## <a name="how-are-assessments-calculated"></a>Jak są obliczane oceny?

Ocena w Azure Migrate oceny serwera jest obliczana przy użyciu metadanych zbieranych o serwerach lokalnych. Obliczenia oceny są wykonywane w trzech etapach; w przypadku każdego serwera obliczenia oceny rozpoczynają się od analizy przydatności do platformy Azure, a następnie ustalają rozmiar i na koniec miesięczne oszacowanie kosztów. Serwer jest przenoszony tylko do późniejszego etapu, jeśli przekazuje poprzednią. Jeśli na przykład serwer zakończy sprawdzanie zgodności z platformą Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure, a zmiany rozmiarów i kosztów nie są wykonywane dla tego samego.

## <a name="azure-suitability-analysis"></a>Analiza przydatności na platformie Azure

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Azure Migrate oceny serwera ocenia każdą maszynę lokalną pod kątem przydatności do migracji do platformy Azure i klasyfikuje ocenione maszyny do jednej z następujących kategorii przydatności:
- **Gotowe do użycia na platformie Azure** — można migrować maszynę na platformę Azure bez wprowadzania żadnych zmian. Zostanie ono uruchomione na platformie Azure z pełną pomocą techniczną platformy Azure.
- **Warunkowo gotowy na platformę Azure** — maszyna może przeprowadzić rozruch na platformie Azure, ale może nie mieć pełnej pomocy technicznej platformy Azure. Na przykład maszyna ze starszą wersją systemu operacyjnego Windows Server nie jest obsługiwana na platformie Azure. Przed przeprowadzeniem migracji tych maszyn na platformę Azure należy zachować ostrożność i postępować zgodnie z zaleceniami dotyczącymi korygowania zalecanymi w ramach oceny, aby rozwiązać problemy z gotowością.
- **Nie gotowy na platformę Azure** — maszyna nie zostanie uruchomiona na platformie Azure. Na przykład jeśli maszyna lokalna ma dysk o rozmiarze większym niż 64 TB, nie może być hostowany na platformie Azure. Przed przeprowadzeniem migracji na platformę Azure należy postępować zgodnie z zaleceniami dotyczącymi korygowania sugerowanymi w ocenie. Ustalanie wielkości liter i oszacowanie kosztów nie jest wykonywane dla maszyn oznaczonych jako niegotowe dla platformy Azure.
- **Nieznane gotowość** — Azure Migrate nie mógł znaleźć gotowości maszyny z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

Azure Migrate oceny serwera przegląd właściwości komputera i systemu operacyjnego gościa w celu zidentyfikowania gotowości maszyny lokalnej na platformie Azure.

### <a name="machine-properties"></a>Właściwości maszyny

Ocena serwera sprawdza następujące właściwości lokalnej maszyny wirtualnej w celu ustalenia, czy można uruchomić ją na platformie Azure.

**Property** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchu w systemie BIOS, a nie z interfejsem UEFI. | Warunkowo gotowe, jeśli typ rozruchu to UEFI.
**Rdzeni** | Liczba rdzeni w maszynach musi być mniejsza lub równa maksymalnej liczbie rdzeni (128 rdzeni) obsługiwanych przez maszynę wirtualną platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate traktuje wykorzystane rdzenie do porównania. Jeśli czynnik komfortu jest określony w ustawieniach oceny, Liczba użytych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, Azure Migrate używa przyznanych rdzeni, bez zastosowania współczynnika komfortu. | Gotowe, jeśli jest mniejsze lub równe limitom.
**Pamięć** | Rozmiar pamięci maszyny musi być równy lub mniejszy niż maksymalna ilość pamięci (3892 GB na platformie Azure M Series Standard_M128m&nbsp;<sup>2</sup>) dozwolona dla maszyny wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate uwzględnia wykorzystanie pamięci do porównania. W przypadku określenia współczynnika komfortu wykorzystanie pamięci jest mnożone przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii, przydzieloną pamięć jest używana, bez zastosowania współczynnika komfortu.<br/><br/> | Gotowe, jeśli w ramach limitów.
**Dysk magazynu** | Przydzielony rozmiar dysku musi być 32 TB miejsca lub mniejszy.<br/><br/> Liczba dysków dołączonych do maszyny musi być 65 lub mniejsza, łącznie z dyskiem systemu operacyjnego. | Gotowe, jeśli w ramach limitów.
**Sieć** | Maszyna musi mieć dołączoną 32 lub mniej kart sieciowych. | Gotowe, jeśli w ramach limitów.

### <a name="guest-operating-system"></a>System operacyjny gościa
Wraz z właściwościami maszyny wirtualnej Azure Migrate oceny serwera są sprawdzane w systemie operacyjnym gościa maszyn, aby określić, czy można je uruchomić na platformie Azure.

> [!NOTE]
> W przypadku maszyn wirtualnych VMware Azure Migrate oceny serwera używa systemu operacyjnego określonego dla maszyny wirtualnej w vCenter Server, aby przeprowadzić analizę systemu operacyjnego gościa. W przypadku maszyn wirtualnych z systemem Linux działających w oprogramowaniu VMware obecnie nie jest dostępna dokładna wersja jądra systemu operacyjnego gościa.

Poniższa logika jest używana przez Azure Migrate oceny serwera, aby identyfikować gotowość platformy Azure na podstawie systemu operacyjnego.

**System operacyjny** | **Szczegóły** | **Stan gotowości platformy Azure**
--- | --- | ---
Windows Server 2016 & wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 R2 & wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 & wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2008 R2 ze wszystkimi usługami SPs | Platforma Azure oferuje pełną pomoc techniczną.| Gotowa na platformę Azure
Windows Server 2008 (32-bitowe i 64-bit) | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2003, 2003 R2 | Te systemy operacyjne przekazały koniec okresu świadczenia pomocy technicznej i potrzebują [niestandardowej umowy pomocy technicznej (CSA)](https://aka.ms/WSosstatement) na potrzeby obsługi platformy Azure. | Warunkowo gotowa na platformę Azure Rozważ uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Te systemy operacyjne przekazały koniec okresu obowiązywania, komputer może zostać uruchomiony na platformie Azure, ale żadna pomoc techniczna systemu operacyjnego nie jest świadczona przez platformę Azure. | Warunkowo gotowa na platformę Azure zaleca się uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Klient z systemem Windows 7, 8 i 10 | Platforma Azure zapewnia pomoc techniczną tylko w przypadku [subskrypcji programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowa na platformę Azure
Windows 10 Pro Desktop | Platforma Azure zapewnia pomoc techniczną z wielodostępnymi [prawami hostingu.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Te systemy operacyjne przekazały koniec okresu obowiązywania, komputer może zostać uruchomiony na platformie Azure, ale żadna pomoc techniczna systemu operacyjnego nie jest świadczona przez platformę Azure. | Warunkowo gotowa na platformę Azure zaleca się uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Linux | System Azure poświadcza te [systemy operacyjne Linux](../virtual-machines/linux/endorsed-distros.md). Inne systemy operacyjne Linux mogą zostać uruchomione na platformie Azure, ale zaleca się uaktualnienie systemu operacyjnego do wersji zatwierdzonej przed przeprowadzeniem migracji na platformę Azure. | Gotowe na platformę Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowe, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne<br/><br/> Na przykład Oracle Solaris, Apple Mac OS itp., FreeBSD itp. | Na platformie Azure nie są poświadczone te systemy operacyjne. Komputer może przeprowadzić rozruch na platformie Azure, ale nie ma pomocy technicznej systemu operacyjnego na platformie Azure. | Warunkowo gotowa na platformę Azure zaleca się zainstalowanie obsługiwanego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.  
System operacyjny określony jako **inny** w vCenter Server | W takim przypadku Azure Migrate nie może zidentyfikować systemu operacyjnego. | Nieznane gotowość. Upewnij się, że system operacyjny działający na maszynie wirtualnej jest obsługiwany na platformie Azure.
32-bitowe systemy operacyjne | Komputer może przeprowadzić rozruch na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowa na platformę Azure Rozważ uaktualnienie systemu operacyjnego maszyny z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.

## <a name="sizing"></a>Zmiany rozmiaru

Po oznaczeniu maszyny jako gotowej na platformie Azure Ocena serwera wykonuje zmiany rozmiarów, co obejmuje identyfikację odpowiedniej maszyny wirtualnej platformy Azure i jednostki SKU dysku dla lokalnej maszyny wirtualnej. Zalecenia dotyczące rozmiaru różnią się w zależności od określonych właściwości oceny.

- Jeśli Ocena korzysta z *ustalania rozmiaru na podstawie wydajności*, Azure Migrate traktuje historię wydajności maszyny w celu zidentyfikowania rozmiaru maszyny wirtualnej i typu dysku na platformie Azure. Ta metoda jest szczególnie przydatna, jeśli masz nadmierną przydzieloną lokalną maszynę wirtualną, ale wykorzystanie jest niskie i chcesz uzyskać odpowiednie rozmiary maszyny wirtualnej na platformie Azure, aby zaoszczędzić koszty. Ta metoda pomoże zoptymalizować rozmiary podczas migracji.
- Jeśli nie chcesz uwzględniać danych wydajności dotyczących rozmiaru maszyny wirtualnej i chcesz przełączyć maszynę lokalną na platformę Azure, możesz określić kryterium ustalania rozmiaru, tak jak w przypadku oceny stanu *lokalnego* i serwera, będzie Rozmiarować maszyny wirtualne na podstawie lokalnego Konfiguracja bez uwzględniania danych użycia. W tym przypadku rozmiar dysku zostanie wykonany w oparciu o typ magazynu określony we właściwościach oceny (dyski HDD w warstwie Standardowa, dyski SSD w warstwie Standardowa lub dyski w warstwie Premium).

### <a name="performance-based-sizing"></a>Ustalanie wielkości na podstawie wydajności

W przypadku ustalania rozmiarów na podstawie wydajności Azure Migrate oceny serwera rozpoczyna się od dysków podłączonych do maszyny wirtualnej, a następnie kart sieciowych, a następnie mapuje jednostkę SKU maszyny wirtualnej platformy Azure na podstawie wymagań obliczeniowych lokalnej maszyny wirtualnej. Urządzenie Azure Migrate profiluje lokalne środowisko do zbierania danych wydajności dotyczących procesora CPU, pamięci, dysków i karty sieciowej.

**Zbieranie danych o wydajności**

- W przypadku maszyn wirtualnych VMware urządzenie Azure Migrate zbiera punkt próbkowania w czasie rzeczywistym co 20-sekundowy interwał dla maszyn wirtualnych funkcji Hyper-V, czyli punkt próbkowania w czasie rzeczywistym jest zbierany co 30 sekund.
- Następnie urządzenie pobiera próbki zbierane przez 10 minut i wysyła maksymalną wartość dla ostatnich 10 minut do Azure Migrate oceny serwera.
- Azure Migrate oceny serwera przechowuje wszystkie 10-minutowe punkty próbkowania przez ostatni miesiąc i w zależności od właściwości oceny określonych dla *historii wydajności* i *użycia percentylu*identyfikuje odpowiedni punkt danych. , które powinny być używane do zmiany wielkości liter. Na przykład jeśli historia wydajności jest ustawiona na jeden dzień, a wykorzystanie percentylu jest używany 95. percentylu, używa 10-minutowych punktów próbkowania dla ostatniego dnia, sortuje je w kolejności rosnącej i wybiera wartość percentylu używany 95. dla zmiany wielkości liter.
- Powyższa wartość jest następnie mnożona czynnikiem komfortu, aby uzyskać efektywne dane użycia wydajności dla każdej metryki (użycie procesora CPU, użycie pamięci, operacje we/wy dysku (odczyt i zapis), przepływność dysku (odczyt i zapis), przepływność sieci (w i wychodząca)) urządzenie zbiera dane.
- Po zidentyfikowaniu wartości efektywnego wykorzystania obliczenia, magazyn i rozmiar sieci są wykonywane w następujący sposób:

**Rozmiar magazynu**: Azure Migrate próbuje zamapować każdy dysk dołączony do maszyny na dysk na platformie Azure.

> [!NOTE]
> Azure Migrate: Ocena serwera obsługuje tylko dyski zarządzane do oceny.

  - Liczba operacji we/wy odczytu i zapisu dysku jest dodawana, aby uzyskać łączną liczbę operacji we/wy, podobnie odczyt i przepływność zapisu, które są dodawane w celu uzyskania całkowitej przepływności każdego dysku
  - Jeśli typ magazynu został określony jako automatyczny, na podstawie efektywnych wartości IOPS i przepływności, Azure Migrate oceny serwera, wskazuje, czy dysk powinien być mapowany na standardowy dysk twardy, standardowy SSD lub dysku Premium na platformie Azure. Jeśli typ magazynu jest ustawiony na HDD w warstwie Standardowa/SSD/Premium, podejmie próbę znalezienia dysku SKU w wybranym typie magazynu (dyski HDD w warstwie Standardowa/SSD/Premium).
  - Jeśli Ocena serwera nie może znaleźć dysku z wymaganą liczbą IOPS & przepływności, oznacza to, że maszyna jest nieodpowiednia dla platformy Azure.
  - W przypadku znalezienia zestawu odpowiednich dysków wybiera te, które obsługują lokalizację określoną w ustawieniach oceny.
  - Jeśli istnieje wiele kwalifikujących się dysków, wybiera jeden z najniższym kosztem.
  - Jeśli dane dotyczące wydajności dla dowolnego dysku są niedostępne, dane konfiguracji dysku (rozmiar dysku) są używane do znalezienia standardowego dysku SSD na platformie Azure.

**Ustalanie wielkości sieci**: Azure Migrate oceny serwera próbuje znaleźć maszynę wirtualną platformy Azure, która może obsługiwać liczbę kart sieciowych podłączonych do maszyny lokalnej oraz wydajność wymaganą przez te karty sieciowe.
    - W celu uzyskania efektywnej wydajności sieci lokalnej maszyny wirtualnej Ocena serwera agreguje dane przesyłane na sekundę (MB/s) z komputera (w sieci) na wszystkich kartach sieciowych i stosuje współczynnik komfortu. Ta liczba jest używana do znajdowania maszyny wirtualnej platformy Azure, która może obsługiwać wymaganą wydajność sieci.
    - Wraz z wydajnością sieci, uważa również, że maszyna wirtualna platformy Azure może obsługiwać wymaganą liczbę kart sieciowych.
    - Jeśli żadne dane wydajności sieci nie są dostępne, tylko liczba kart sieciowych jest uwzględniana w przypadku zmiany rozmiarów maszyny wirtualnej.

**Ustalanie wielkości obliczeń**: Po obliczeniu wymagań dotyczących magazynu i sieci Azure Migrate Ocena serwera uwzględnia wymagania dotyczące procesora i pamięci, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.
    - Azure Migrate analizuje efektywne wykorzystanie rdzeni i pamięci, aby znaleźć odpowiedni rozmiar maszyny wirtualnej na platformie Azure.
    - Jeśli odpowiedni rozmiar nie zostanie znaleziony, oznacza to, że komputer jest oznaczony jako nieodpowiedni dla platformy Azure.
    - Jeśli zostanie znaleziony odpowiedni rozmiar, Azure Migrate stosuje obliczenia magazynu i sieci. Następnie stosuje ustawienia lokalizacji i warstwy cenowej dla końcowego zalecenia dotyczącego rozmiaru maszyny wirtualnej.
    - Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

### <a name="as-on-premises-sizing"></a>Zgodnie z rozmiarem lokalnym

Jeśli używasz jako *lokalnego rozmiaru*, Ocena serwera nie uwzględnia historii wydajności maszyn wirtualnych i dysków, a także ALOKUJE jednostkę SKU maszyny wirtualnej na platformie Azure na podstawie rozmiaru przydzielonego lokalnie. Podobnie w przypadku ustalania wielkości dysku sprawdza typ magazynu określony we właściwościach oceny (HDD w warstwie Standardowa/SSD/Premium) i zaleca odpowiedni typ dysku. Domyślny typ magazynu to dyski Premium.

## <a name="confidence-ratings"></a>Klasyfikacje zaufania
Każda Ocena oparta na wydajności w Azure Migrate jest skojarzona z oceną zaufania, która mieści się w zakresie od jednego (najmniejszego) do pięciu (najwyższa).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Klasyfikacja zaufania nie ma zastosowania w przypadku ocen lokalnych.
- W przypadku ustalania rozmiarów na podstawie wydajności Azure Migrate oceny serwera:
    - Dane użycia procesora CPU i pamięci maszyny wirtualnej.
    - Ponadto dla każdego dysku podłączonego do maszyny wirtualnej potrzebuje ona danych o liczbie operacji odczytu/zapisu na sekundę i przepływności.
    - Podobnie w przypadku każdej karty sieciowej podłączonej do maszyny wirtualnej Klasyfikacja zaufania potrzebuje sieci we/wy do ustalania rozmiarów na podstawie wydajności.
    - Jeśli którykolwiek z powyższych numerów wykorzystania nie jest dostępny w vCenter Server, zalecenie dotyczące rozmiaru może być niezawodne.

W zależności od odsetka dostępnych punktów danych ocena zaufania dla oceny jest określana w następujący sposób:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

### <a name="low-confidence-ratings"></a>Oceny o niskiej pewności

Niektóre z powodów, dla których ocena może uzyskać klasyfikację o niskiej pewności:

- Twoje środowisko nie było profilem przez czas, w którym tworzysz ocenę. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać przez co najmniej dobę po rozpoczęciu odnajdywania dla wszystkich punktów danych do zebrania.
- Niektóre maszyny wirtualne zostały wyłączone w okresie, dla którego jest obliczana Ocena. Jeśli wszystkie maszyny wirtualne zostały wyłączone przez pewien czas, Azure Migrate Ocena serwera nie może zebrać danych wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w okresie między okresem, dla którego jest obliczana Ocena. Na przykład jeśli tworzysz ocenę dla historii wydajności w ciągu ostatnich miesięcy, ale niektóre maszyny wirtualne zostały utworzone w środowisku tylko tydzień temu, historia wydajności nowych maszyn wirtualnych nie będzie dostępna przez cały czas trwania.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest poniżej pięciu gwiazdek, zalecamy odczekanie co najmniej jednego dnia urządzenia, aby profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli nie, ustalanie rozmiarów na podstawie wydajności może być niezawodne i zalecane jest przełączenie oceny w celu użycia jako lokalnego określania rozmiarów.

## <a name="monthly-cost-estimation"></a>Oszacowanie kosztu miesięcznego

Po zakończeniu ustalania rozmiarów Azure Migrate oblicza koszty obliczeń i magazynowania po migracji.

- **Koszt obliczeń**: Przy użyciu zalecanego rozmiaru maszyny wirtualnej platformy Azure Azure Migrate używa interfejsu API rozliczeń, aby obliczyć miesięczny koszt maszyny wirtualnej.
    - Obliczanie wymaga systemu operacyjnego, programu Software Assurance, wystąpień zarezerwowanych, czasu działania maszyny wirtualnej, lokalizacji i ustawień waluty.
    - Agreguje koszt na wszystkich maszynach, aby obliczyć łączny koszt obliczeń miesięcznych.
- **Koszt magazynu**: Miesięczny koszt magazynu dla maszyny jest obliczany przez zsumowanie miesięcznego kosztu wszystkich dysków dołączonych do maszyny
    - Azure Migrate oceny serwera oblicza łączne miesięczne koszty magazynowania, sumując koszty magazynowania wszystkich maszyn.
    - Obecnie obliczenie nie przyjmuje ofert określonych w ustawieniach oceny na koncie.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Kolejne kroki

Utwórz ocenę dla [maszyn wirtualnych VMware](tutorial-assess-vmware.md) lub [maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md).
