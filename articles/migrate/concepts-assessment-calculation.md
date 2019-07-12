---
title: Obliczenia dotyczące oceny w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie obliczenia dotyczące oceny usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805983"
---
# <a name="assessment-calculations"></a>Obliczenia dotyczące oceny

Azure oceny migracji serwer ocenia obciążenia lokalne pod kątem migracji na platformę Azure. Ten artykuł zawiera informacje dotyczące sposobu obliczania ocen.


[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i wystąpienia chmury prywatne/publiczne, na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.

## <a name="overview"></a>Przegląd

Ocena oceny Server migracji platformy Azure ma trzy etapy. Ocena zaczyna się od analizy przydatności, po której następuje zmiana rozmiaru, i na koniec comiesięczny szacowania kosztów. Maszyna tylko przenosi na późniejszym etapie Jeśli przekazuje poprzedniego. Na przykład w przypadku niepowodzenia sprawdzania gotowości usługi Azure machine on jest oznaczony jako nieodpowiedni dla platformy Azure, a zmiany rozmiaru i wyceny nie będzie odbywać się.


## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

**Property** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/> Usługa Azure Migrate obsługuje obecnie następujące regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Indie środkowe, środkowe stany USA, Chiny wschodnie, Chiny Północne, Azja Wschodnia, wschodnie stany USA, wschodnie stany USA 2, Niemcy środkowe, Niemcy północno-wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Północna Środkowe stany USA, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, Indie Południowe, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo, administracja Gov Teksas stany USA, administracja USA — Arizona USA — Wirginia, środkowe stany USA Zachodnia, Europa Zachodnia, Indie Zachodnie, Indie, zachodnie stany USA i zachodnie stany USA 2.<br/> Domyślny region docelowy to Zachodnie stany USA 2.
**Typ magazynu** | Standardowa HHD dysków/Standard dysków SSD dyski/Premium.<br/><br/> Po określeniu typu magazynu jako automatyczne w ocenie zalecenie dysku opiera się na dane wydajności dysków (operacje We/Wy i przepływność).<br/><br/> Jeśli określisz typ magazynu jako standardowa/Premium, wybrana zaleca oceny dysk jednostki SKU w ramach typu magazynu.<br/><br/> Jeśli chcesz uzyskać jedno wystąpienie maszyny Wirtualnej w umowie SLA na poziomie 99,9%, można ustawić typ magazynu jako dyski zarządzane w warstwie Premium. Wszystkie dyski w ocenie będą następnie zalecane jako dyski zarządzane w warstwie Premium. <br/> Usługa Azure Migrate obsługuje tylko dyski zarządzane na potrzeby oceny migracji.<br/> 
**Wystąpienia zarezerwowane (RI)** | Należy określić tę właściwość, jeśli zostały zarezerwowane wystąpienia w systemie Azure. Szacunkowych kosztów w ocenie będzie uwzględniać rabaty wystąpień Zarezerwowanych. Wystąpienia zarezerwowane są obecnie obsługiwane tylko w przypadku płatności oferty w usłudze Azure Migrate.
**Kryterium rozmiaru** | Używane do odpowiedniego rozmiaru maszyn wirtualnych. Zmiana rozmiaru może być oparte na wydajności lub jako — lokalnie, bez uwzględniania historii wydajności.
**Historia wydajności** | Czas trwania uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku, gdy ustalania rozmiaru jest oparte na wydajności.
**Użycie percentyla** | Wartość percentyla próbki wydajności używany do właściwych rozmiarów maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku, gdy ustalania rozmiaru jest oparte na wydajności.
**Serie maszyn wirtualnych** | Serie maszyn wirtualnych używane do szacowania rozmiaru. Jeśli na przykład masz środowisko produkcyjne, którego nie zamierzasz migrować do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię A z listy serii. Ustalanie właściwego rozmiaru zostanie wykonane tylko przy użyciu wybranych serii.
**Współczynnik komfortu** | Ocena serwera migracji platformy Azure uwzględnia bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której dokonano rejestracji. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Twoja waluta rozliczeniowa. 
**Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure.<br/> Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli Twoje maszyny wirtualne nie będą działać 24 x 7 w systemie Azure, można określić czas trwania (liczba dni w miesiącu) i liczby godzin dziennie, dla którego one będzie działać i oszacowanie kosztów związanych będzie odbywać się odpowiednio.<br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz pakiet software assurance i kwalifikują się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. 



## <a name="azure-suitability-analysis"></a>Analiza zgodności platformy Azure

Nie wszystkie maszyny są odpowiednie do uruchamiania na platformie Azure. Azure oceny migracji serwera ocenia poszczególnych maszyn lokalnych do migracji, a następnie klasyfikuje maszyn do jednej z następujących kategorii przydatność:
- **Gotowa na platformę Azure** — mogą być migrowane maszyny jako — jest na platformie Azure bez wprowadzania żadnych zmian. Zostanie uruchomiony na platformie Azure z obsługą pełnej platformy Azure.
- **Warunkowo gotowa na platformę Azure** -maszynę można uruchomić na platformie Azure, ale nie może mieć pomocy technicznej pełnej platformy Azure. Na przykład maszyny za pomocą starszej wersji systemu operacyjnego Windows Server nie jest obsługiwane na platformie Azure. Musisz należy zachować ostrożność, przed przeprowadzeniem migracji tych maszyn na platformę Azure i postępuj zgodnie z wskazówki dotyczące rozwiązywania problemów, sugerowane w ocenie, aby rozwiązać problemy z gotowością przed przeprowadzeniem migracji.
- **Niegotowa na platformę Azure** -maszyny nie uruchomi się na platformie Azure. Na przykład jeśli na komputerze lokalnym ma dysk o rozmiarze ponad 4 TB podłączone do niego, nie mogą być hostowane na platformie Azure. Należy wykonać wskazówki dotyczące rozwiązywania problemów, sugerowane w ocenie, aby rozwiązać ten problem gotowości przed migracją na platformę Azure. Szacowanie odpowiedni rozmiar i koszt nie jest wykonywane dla maszyn, które są oznaczone jako niegotowa na platformę Azure.
- **Nieznana gotowość** — usługa Azure Migrate nie może odnaleźć gotowości maszyny z powodu niewystarczających danych dostępnych w programie vCenter Server.



### <a name="machine-properties"></a>Właściwości maszyny

Usługa Azure Migrate monitoruje następujące właściwości lokalnej maszyny Wirtualnej, aby ustalić, czy można uruchomić na platformie Azure.

**Property** | **Szczegóły** | **Stan gotowości na platformę Azure**
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchu systemu BIOS i UEFI nie. | Warunkowo gotowa, jeśli typ rozruchu to UEFI.
**Cores** | Liczby rdzeni na komputerach musi być równa lub mniejsza niż maksymalna liczba rdzeni (128) obsługiwanych na Maszynie wirtualnej platformy Azure.<br/><br/> Jeśli Historia wydajności jest dostępny, usługa Azure Migrate uwzględnia rdzenie wykorzystywanych do porównania. Jeśli współczynnik komfortu jest określony w ustawieniach oceny, liczby rdzeni wykorzystywanych jest mnożony przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnych historii wydajności, usługę Azure migrate przydzielone rdzenie, bez stosowania współczynnik komfortu. | Gotowe, jeśli są mniejsze niż lub równe limitów.
**Pamięć** | Rozmiar pamięci maszyny musi być równa lub mniejsza niż maksymalna ilość pamięci (3892 GB dla serii Azure M Standard_M128m&nbsp;<sup>2</sup>) dozwolone na Maszynie wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli Historia wydajności jest dostępny, usługa Azure Migrate uwzględnia pamięci wykorzystywanych do porównania. Jeśli zostanie określona współczynnik komfortu, wykorzystywanych pamięci jest mnożony przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii ilość przydzielonej pamięci jest używany bez stosowania współczynnik komfortu.<br/><br/> | Gotowe, jeśli komputer znajduje się w granicach.
**Dysk magazynu** | Przydzielony rozmiar dysku musi być 4 TB (4096 GB) lub mniej.<br/><br/> Liczba dysków dołączonych do maszyny musi być 65 lub mniej, łącznie z dysku systemu operacyjnego. | Gotowe, jeśli komputer znajduje się w granicach.
**Sieć** | Maszyna musi być 32 lub najwyżej dwie karty sieciowe podłączone do niego. | Gotowe, jeśli komputer znajduje się w granicach.

### <a name="guest-operating-system"></a>System operacyjny gościa
Wraz z właściwości maszyny Wirtualnej Azure migracji serwera oceny patrzy na systemie operacyjnym gościa maszyny, aby ustalić, czy można uruchomić na platformie Azure.

> [!NOTE]
> Ocena serwera migracji platformy Azure korzysta z systemu operacyjnego określone dla maszyny Wirtualnej w programie vCenter Server w celu analizy. Ocena serwera migracji platformy Azure jest urządzenie oparta na protokole odnajdowania maszyn wirtualnych i nie można zweryfikować, czy system operacyjny działający na maszynie Wirtualnej jest taka sama jak określona w programie vCenter Server.

Poniższa logika jest używany przez migrację serwera oceny Azure, do identyfikowania gotowości na platformę Azure na podstawie systemu operacyjnego.

**System operacyjny** | **Szczegóły** | **Stan gotowości na platformę Azure**
--- | --- | ---
System Windows Server 2016 i wszystkie dodatki Service Pack | System Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 R2 i wszystkie dodatki Service Pack | System Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 i wszystkie dodatki Service Pack | System Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2008 R2 z wszystkie dodatki Service Pack | System Azure oferuje pełną pomoc techniczną.| Gotowa na platformę Azure
Windows Server 2008 (32-bitowych i 64-bitowy) | System Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2003, 2003 R2 | Te systemy operacyjne przeszły koniec okresu pomocy technicznej i trzeba [umowy pomocy technicznej niestandardowe (CSA)](https://aka.ms/WSosstatement) obsługi na platformie Azure. | Warunkowo gotowa na platformę Azure, Rozważ uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Te systemy operacyjne przeszły ich zakończenia okresu pomocy technicznej, maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure. | Warunkowo gotowa na platformę Azure, zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Klient Windows 7, 8 i 10 | Platforma Azure oferuje obsługi za pomocą [tylko subskrypcji programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowa na platformę Azure
Windows 10 Pro pulpitu | Platforma Azure oferuje obsługi za pomocą [praw hostingu wielodostępnej.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Te systemy operacyjne przeszły ich zakończenia okresu pomocy technicznej, maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure. | Warunkowo gotowa na platformę Azure, zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Linux | Platforma Azure wspiera te [systemów operacyjnych Linux](../virtual-machines/linux/endorsed-distros.md). Innych systemów operacyjnych Linux można uruchomić na platformie Azure, ale zaleca się uaktualnienie systemu operacyjnego do zalecanych wersji przed migracją na platformę Azure. | Gotowa na platformę Azure, jeśli wersja jest zalecane.<br/><br/>Warunkowo gotowa, jeśli wersja nie jest zalecane.
Inne systemy operacyjne<br/><br/> np. Oracle Solaris, itp. system operacyjny Mac firmy Apple, FreeBSD itp. | Azure nie popiera tych systemów operacyjnych. Maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure. | Warunkowo gotowa na platformę Azure, zalecane jest instalacji obsługiwanego systemu operacyjnego przed migracją na platformę Azure.  
System operacyjny określony jako **innych** w programie vCenter Server | Usługa Azure Migrate nie może zidentyfikować w takim przypadku system operacyjny. | Nieznana gotowość. Upewnij się, że system operacyjny działający na maszynie Wirtualnej jest obsługiwana na platformie Azure.
32-bitowych systemach operacyjnych | Maszynę można uruchomić na platformie Azure, ale Azure mogą nie zapewnić pełną pomoc techniczną. | Warunkowo gotowa na platformę Azure, Rozważ uaktualnienie systemu operacyjnego maszyny z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego przed migracją na platformę Azure.

## <a name="sizing"></a>Zmiany rozmiaru

Po maszyny jest oznaczony jako gotowa na platformę Azure, usługa Azure Migrate rozmiarów maszyny Wirtualnej i jej dysków na platformie Azure.

- Jeśli ocena korzysta z rozmiaru na podstawie wydajności, usługa Azure Migrate uwzględnia historii wydajności maszyn do identyfikowania typu dysk i rozmiar maszyny Wirtualnej na platformie Azure. Ta metoda jest szczególnie przydatne, jeśli został nadmierną alokację maszyny Wirtualnej w środowisku lokalnym, ale wykorzystanie jest niskie i ma odpowiedni rozmiar maszyny Wirtualnej na platformie Azure w celu obniżenia kosztów.
- Jeśli jesteś przy użyciu jako lokalne oceny, ocena Server migracji platformy Azure będzie rozmiar maszyn wirtualnych, zgodnie z ustawieniami w środowisku lokalnym, bez uwzględniania danych wykorzystania. Zmienianie rozmiaru dysku, w tym przypadku zależy od typu magazynu, które określisz we właściwościach oceny (dysk w warstwie standardowa lub Premium disk).

### <a name="performance-based-sizing"></a>Ustalanie rozmiaru na podstawie wydajności

W przypadku ustalania rozmiaru na podstawie wydajności, usługa Azure Migrate zaczyna się od dysków dołączonych do maszyny Wirtualnej, następuje kart sieciowych i następnie mapy Maszynie wirtualnej platformy Azure na podstawie wymagania dotyczące obliczeń maszyny wirtualnej w środowisku lokalnym.

- **Magazyn**: Usługa Azure Migrate próbuje zamapować każdego dysku podłączonego do maszyny, na dysku na platformie Azure.
    - Aby pobrać efektywne dysku operacji We/Wy na sekundę (IOPS) i przepływność (MB/s), usługa Azure Migrate mnoży dysku na SEKUNDĘ i przepływności, zapewniając współczynnik komfortu. Na podstawie skuteczne operacje We/Wy i wartości przepustowości, usługa Azure Migrate zidentyfikuje, jeśli dysk powinien być mapowany na dysku w warstwie standardowa lub premium na platformie Azure.
    - Jeśli usługa Azure Migrate nie może odnaleźć dysku za pomocą wymaganych operacji We/Wy i przepływność, oznacza maszyny jako nienadające się do platformy Azure. [Dowiedz się więcej](../azure-subscription-service-limits.md#storage-limits) dotyczących platformy Azure ogranicza na dysku i maszyny Wirtualnej.
    - Jeśli znajdzie zbiór odpowiednich dysków, usługa Azure Migrate wybiera te, które obsługują metoda nadmiarowości magazynu, a w lokalizacji określonej w ustawieniach oceny.
    - W przypadku wielu dysków kwalifikują wybiera ten, który generuje najniższy koszt.
    - Jeśli dane wydajności dla dysków jest niedostępny, wszystkie dyski są mapowane na dyski w warstwie standardowa na platformie Azure.

- **Sieć**: Usługa Azure Migrate podejmie próbę odnalezienia maszyn wirtualnych platformy Azure, który może obsługiwać liczbę kart sieciowych dołączonych do maszyny lokalnej i wydajności wymaganych przez te karty sieciowe.
    - Aby uzyskać wydajność sieci od lokalnej maszyny Wirtualnej, usługa Azure Migrate agreguje dane przesyłane na sekundę (MB/s) poza maszyny (sieć się) dla wszystkich kart sieciowych i stosuje współczynnik komfortu. Ten numer jest używany, można znaleźć maszyny Wirtualnej platformy Azure, który może obsługiwać żądanej wartości wydajności sieci.
    - Wraz z wydajność sieci, uwzględniane są również obowiązujące Jeśli maszyna wirtualna platformy Azure może obsługiwać wymagane liczba kart sieciowych.
    - Jeśli sieci wydajności są dostępne żadne dane, liczba kart sieciowych jest traktowany jako w przypadku ustalania rozmiaru maszyny Wirtualnej.

- **Obliczenia**: Po obliczeniu są wymagania dotyczące magazynu i sieci, usługa Azure Migrate uwzględnia wymagania procesora CPU i pamięci można znaleźć odpowiedniego rozmiaru maszyny Wirtualnej na platformie Azure.
    - Usługa Azure Migrate analizuje wykorzystywanych rdzeni i pamięci, a także obowiązują współczynnik komfortu, aby pobrać efektywne rdzeni i pamięci. Na podstawie tego numeru, próbuje odnaleźć odpowiedniego rozmiaru maszyny Wirtualnej na platformie Azure.
    - Jeśli zostanie znaleziony żaden odpowiedni rozmiar, maszyny jest oznaczony jako nieodpowiedni dla platformy Azure.
    - Jeśli zostanie znaleziony odpowiedni rozmiar, usługa Azure Migrate dotyczy obliczeń magazynu i sieci. Następnie stosuje lokalizacji i ustawień warstwy cenowej, dla końcowego zalecenie dotyczące rozmiaru maszyny Wirtualnej.
    - Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

### <a name="as-on-premises-sizing"></a>Jako lokalne zmiany rozmiaru

Jeśli używasz lokalne zmiany rozmiaru, oceny Server przydziela SKU maszyny Wirtualnej na platformie Azure, w oparciu o rozmiar środowiska lokalnego. Podobnie w przypadku dla rozmiaru dysku, sprawdza typ magazynu określona we właściwościach oceny (standardowa/Premium) i w związku z tym zaleca typ dysku. Domyślny typ magazynu jest dysków w warstwie Premium.

## <a name="confidence-ratings"></a>Klasyfikacje ufności
Każda oparte na wydajności ocena w usłudze Azure Migrate jest skojarzona z oceną zaufania obejmującą zakres od jednego (najniższy) do pięciu Start (najwyższy).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Ocena zaufania nie jest odpowiednie dla oceny lokalne.
- W przypadku ustalania rozmiaru na podstawie wydajności, ocena Server migracji platformy Azure wymaga:
    - Dane użycia Procesora i pamięci maszyny Wirtualnej.
    - Ponadto dla każdego dysku podłączonego do maszyny wirtualnej potrzebuje ona danych o liczbie operacji odczytu/zapisu na sekundę i przepływności.
    - Podobnie dla każdej karty sieciowej jest dołączony do maszyny Wirtualnej, ocena zaufania musi we/wy sieci w celu ustalania rozmiaru na podstawie wydajności.
    - Jeśli z powyższych numerów wykorzystania nie są dostępne w programie vCenter Server, zalecenie dotyczące rozmiaru może nie być wiarygodne. 

W zależności od odsetka dostępnych punktów danych ocena zaufania dla oceny jest określana w następujący sposób:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

### <a name="low-confidence-ratings"></a>Klasyfikacje niski ufności

Niektóre z powodów dlaczego oceny można pobrać oceną zaufania niski:

- Środowisko nie profilu na czas trwania, dla którego tworzysz ocenę. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawiona na 1 dzień, należy poczekać co najmniej dzień po jej uruchomieniu odnajdywania dla wszystkich punktów danych uzyskać zebrane.
- Niektórych maszyn wirtualnych zostało wyłączonych w trakcie okresu, dla którego jest obliczana ocena. Jeśli wszystkie maszyny wirtualne została odłączona od zasilania na pewien czas, oceny Server migracji platformy Azure nie można zebrać dane dotyczące wydajności dla tego okresu.
- Niektórych maszyn wirtualnych zostało utworzonych w okresie, dla którego jest obliczana ocena. Na przykład jeśli tworzysz ocenę dla historii wydajności ostatni miesiąc, ale niektóre maszyny wirtualne zostały utworzone w środowisku tylko tydzień temu, Historia wydajności nowych maszyn wirtualnych nie będzie dostępne przez cały czas.

  > [!NOTE]
  > Jeśli ocena zaufania dowolnej oceny jest poniżej pięć gwiazdek, zalecamy poczekać co najmniej dzień dla urządzenia, aby przeprowadzić profilowanie w środowisku, a następnie ponownie obliczyć ocenę. Jeśli nie, ustalania rozmiaru na podstawie wydajności może nie być wiarygodne i firma Microsoft i zaleca się przejście oceny do użycia jako rozmiaru lokalnych.
  
## <a name="monthly-cost-estimation"></a>Szacowanie miesięcznych kosztów

Po zakończeniu zalecenia wymiarowania efektywnego usługi Azure Migrate oblicza koszty zasobów obliczeniowych i magazynu po zakończeniu migracji.

- **Koszt zasobów obliczeniowych**: Przy użyciu zalecanego rozmiaru maszyny Wirtualnej platformy Azure, usługa Azure Migrate używa interfejsu API rozliczeń do obliczania miesięczny koszt dla maszyny Wirtualnej.
    - Obliczenie ma systemu operacyjnego, programu software assurance, zarezerwowane wystąpienia, maszyny Wirtualnej przestojów, lokalizacji i ustawienia waluty pod uwagę.
    - Koszty są agregowane dla wszystkich maszyn, aby obliczyć całkowity miesięczny koszt obliczeń.
- **Koszt usługi Storage**: Miesięczny koszt magazynu dla maszyny jest obliczana przez zsumowanie miesięczny koszt wszystkich dysków dołączonych do maszyny
    - Ocena serwera migracji platformy Azure oblicza całkowity koszt miesięczny magazynu przez agregowanie koszty magazynowania wszystkich maszyn.
    - Obecnie obliczenie nie przyjmuje oferty określone w ustawieniach ocenę pod uwagę.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Kolejne kroki

Utwórz ocenę dla [maszyny wirtualne VMware](tutorial-assess-vmware.md) lub [maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md).
