---
title: Obliczenia dotyczące oceny w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie obliczenia dotyczące oceny usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 012a352b00de2e2d1bf64fd18125ddd10faba5cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679123"
---
# <a name="assessment-calculations"></a>Obliczenia dotyczące oceny

[Usługa Azure Migrate](migrate-overview.md) ocenia obciążenia lokalne pod kątem migracji na platformę Azure. Ten artykuł zawiera informacje dotyczące sposobu obliczania ocen.


## <a name="overview"></a>Omówienie

Ocena usługi Azure Migrate ma trzy etapy. Ocena zaczyna się od analizy przydatności, po której następuje zmiana rozmiaru, i na koniec comiesięczny szacowania kosztów. Maszyna tylko przenosi na późniejszym etapie Jeśli przekazuje poprzedniego. Na przykład w przypadku niepowodzenia sprawdzania gotowości usługi Azure machine on jest oznaczony jako nieodpowiedni dla platformy Azure, a zmiany rozmiaru i wyceny nie będzie odbywać się.

## <a name="azure-suitability-analysis"></a>Analiza zgodności platformy Azure

Nie wszystkie maszyny są odpowiednie do uruchamiania w chmurze, jak chmura ma własne ograniczenia i wymagania. Usługa Azure Migrate ocenia każdy na komputerze lokalnym kątem możliwości migracji na platformę Azure i klasyfikuje maszyn do jednej z następujących kategorii:
- **Gotowa na platformę Azure** — mogą być migrowane maszyny jako — jest na platformie Azure bez wprowadzania żadnych zmian. Zostanie uruchomiony na platformie Azure z obsługą pełnej platformy Azure.
- **Warunkowo gotowa na platformę Azure** -maszynę można uruchomić na platformie Azure, ale nie może mieć pomocy technicznej pełnej platformy Azure. Na przykład maszyny za pomocą starszej wersji systemu operacyjnego Windows Server nie jest obsługiwane na platformie Azure. Musisz należy zachować ostrożność, przed przeprowadzeniem migracji tych maszyn na platformę Azure i postępuj zgodnie z wskazówki dotyczące rozwiązywania problemów, sugerowane w ocenie, aby rozwiązać problemy z gotowością przed przeprowadzeniem migracji.
- **Niegotowa na platformę Azure** -maszyny nie uruchomi się na platformie Azure. Na przykład jeśli na komputerze lokalnym ma dysk o rozmiarze ponad 4 TB podłączone do niego, nie mogą być hostowane na platformie Azure. Należy wykonać wskazówki dotyczące rozwiązywania problemów, sugerowane w ocenie, aby rozwiązać ten problem gotowości przed migracją na platformę Azure. Szacowanie odpowiedni rozmiar i koszt nie jest wykonywane dla maszyn, które są oznaczone jako niegotowa na platformę Azure.
- **Nieznana gotowość** — usługa Azure Migrate nie może odnaleźć gotowości maszyny z powodu niewystarczających danych dostępnych w programie vCenter Server.

Usługa Azure Migrate przegląda właściwości maszyny i system operacyjny gościa do identyfikowania gotowości maszyn lokalnych na platformę Azure.

### <a name="machine-properties"></a>Właściwości maszyny
Usługa Azure Migrate monitoruje następujące właściwości lokalnej maszyny Wirtualnej, aby ustalić, czy uruchomić Maszynę wirtualną na platformie Azure.

**Właściwość** | **Szczegóły** | **Stan gotowości na platformę Azure**
--- | --- | ---
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchu systemu BIOS i UEFI nie. | Warunkowo gotowa, jeśli typ rozruchu to UEFI.
**Cores** | Liczby rdzeni na komputerach musi być równa lub mniejsza niż maksymalna liczba rdzeni (128) obsługiwanych na Maszynie wirtualnej platformy Azure.<br/><br/> Jeśli Historia wydajności jest dostępny, usługa Azure Migrate uwzględnia rdzenie wykorzystywanych do porównania. Jeśli współczynnik komfortu jest określony w ustawieniach oceny, liczby rdzeni wykorzystywanych jest mnożony przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnych historii wydajności, usługę Azure migrate przydzielone rdzenie, bez stosowania współczynnik komfortu. | Gotowe, jeśli są mniejsze niż lub równe limitów.
**Pamięć** | Rozmiar pamięci maszyny musi być równa lub mniejsza niż maksymalna ilość pamięci (3892 GB dla serii Azure M Standard_M128m&nbsp;<sup>2</sup>) dozwolone na Maszynie wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli Historia wydajności jest dostępny, usługa Azure Migrate uwzględnia pamięci wykorzystywanych do porównania. Jeśli zostanie określona współczynnik komfortu, wykorzystywanych pamięci jest mnożony przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii ilość przydzielonej pamięci jest używany bez stosowania współczynnik komfortu.<br/><br/> | Gotowe, jeśli komputer znajduje się w granicach.
**Dysk magazynu** | Przydzielony rozmiar dysku musi być 4 TB (4096 GB) lub mniej.<br/><br/> Liczba dysków dołączonych do maszyny musi być 65 lub mniej, łącznie z dysku systemu operacyjnego. | Gotowe, jeśli komputer znajduje się w granicach.
**Sieć** | Maszyna musi być 32 lub najwyżej dwie karty sieciowe podłączone do niego. | Gotowe, jeśli komputer znajduje się w granicach.

### <a name="guest-operating-system"></a>System operacyjny gościa
Wraz z właściwości maszyny Wirtualnej usługi Azure Migrate również analizuje systemu operacyjnego gościa maszyny wirtualnej w środowisku lokalnym, aby ustalić, czy maszyny Wirtualnej można uruchamiać na platformie Azure.

> [!NOTE]
> Usługa Azure Migrate uwzględnia systemu operacyjnego określone w programie vCenter Server, aby wykonywać następujące analizę. Ponieważ odnajdywanie przeprowadzone przez usługę Azure Migrate jest oparte na urządzeniu, nie ma sposób sprawdzić, czy system operacyjny działający na maszynie Wirtualnej jest taki sam jak jeden określony w programie vCenter Server.

Poniższa logika jest używany przez usługę Azure Migrate do identyfikowania gotowości na platformę Azure maszyny Wirtualnej na podstawie systemu operacyjnego.

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

## <a name="sizing"></a>Ustalanie rozmiaru

Po maszyny jest oznaczony jako gotowa na platformę Azure, usługa Azure Migrate rozmiarów maszyny Wirtualnej i jej dysków na platformie Azure. Jeśli kryterium ustalania rozmiaru określone we właściwościach oceny do ustalenia rozmiaru na podstawie wydajności Usługa Azure Migrate uwzględnia historii wydajności maszyn do identyfikowania typu dysk i rozmiar maszyny Wirtualnej na platformie Azure. Ta metoda jest przydatne w scenariuszach, gdzie ma nadmierną alokację maszyny Wirtualnej w środowisku lokalnym, ale wykorzystanie jest niskie, i chcesz odpowiedni rozmiar maszyn wirtualnych na platformie Azure koszty.

Jeśli nie chcesz należy wziąć pod uwagę historię wydajności w przypadku ustalania rozmiaru maszyny Wirtualnej i chcesz przełączyć maszynę Wirtualną jako — jest na platformie Azure, możesz określić kryterium ustalania rozmiaru jako *jako lokalne* i usługa Azure Migrate następnie rozmiar maszyn wirtualnych, opartych na lokalne Konfiguracja bez uwzględniania danych wykorzystania. Zmienianie rozmiaru dysku, w tym przypadku będzie odbywać się zależnie od typu magazynu, które określisz we właściwościach oceny (dysk w warstwie standardowa lub Premium disk)

### <a name="performance-based-sizing"></a>Ustalanie rozmiaru na podstawie wydajności

W przypadku ustalania rozmiaru na podstawie wydajności, usługi Azure Migrate rozpoczyna się od ciągu dyski dołączone do maszyny Wirtualnej, a następnie kart sieciowych, a następnie mapy Maszynie wirtualnej platformy Azure na podstawie wymagań obliczeniowych maszyn wirtualnych w środowisku lokalnym.

- **Magazyn**: Usługa Azure Migrate próbuje zamapować każdego dysku podłączonego do maszyny, na dysku na platformie Azure.

    > [!NOTE]
    > Usługa Azure Migrate obsługuje tylko zarządzane dyski dla oceny.

    - Aby pobrać efektywne dysku operacji We/Wy na sekundę (IOPS) i przepływność (MB/s), usługa Azure Migrate mnoży dysku na SEKUNDĘ i przepływności, zapewniając współczynnik komfortu. Na podstawie skuteczne operacje We/Wy i wartości przepustowości, usługa Azure Migrate zidentyfikuje, jeśli dysk powinien być mapowany na dysku w warstwie standardowa lub premium na platformie Azure.
    - Jeśli usługa Azure Migrate nie może odnaleźć dysku za pomocą wymaganych operacji We/Wy i przepływność, oznacza maszyny jako nienadające się do platformy Azure. [Dowiedz się więcej](../azure-subscription-service-limits.md#storage-limits) dotyczących platformy Azure ogranicza na dysku i maszyny Wirtualnej.
    - Jeśli znajdzie zbiór odpowiednich dysków, usługa Azure Migrate wybiera te, które obsługują metoda nadmiarowości magazynu, a w lokalizacji określonej w ustawieniach oceny.
    - W przypadku wielu dysków kwalifikują wybiera ten, który generuje najniższy koszt.
    - Jeśli dane wydajności dla dysków w niedostępny, wszystkie dyski są mapowane na dyski w warstwie standardowa na platformie Azure.

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
Jeśli kryterium ustalania rozmiaru to *jako lokalnego rozmiaru*, usługa Azure Migrate nie należy wziąć pod uwagę historię wydajności maszyn wirtualnych i dysków i przydziela SKU maszyny Wirtualnej na platformie Azure, w zależności od rozmiaru przydzielone lokalnie. Podobnie w przypadku dla rozmiaru dysku, sprawdza typ magazynu określona we właściwościach oceny (standardowa/Premium) i w związku z tym zaleca typ dysku. Domyślny typ magazynu jest dysków w warstwie Premium.

### <a name="confidence-rating"></a>Ocena zaufania
Każda bazująca na wydajności ocena w usłudze Azure Migrate jest skojarzona z oceną zaufania obejmującą zakres od 1 gwiazdki do 5 gwiazdek (1 gwiazdka to najniższa ocena, 5 gwiazdek — najwyższa). Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny. Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate. Ocena zaufania nie ma przełożenia na oceny lokalne.

W przypadku ustalania rozmiaru na podstawie wydajności usługa Azure Migrate potrzebuje danych o użyciu procesora CPU i pamięci maszyny wirtualnej. Ponadto dla każdego dysku podłączonego do maszyny wirtualnej potrzebuje ona danych o liczbie operacji odczytu/zapisu na sekundę i przepływności. Analogicznie dla każdej karty sieciowej podłączonej do maszyny wirtualnej usługa Azure Migrate potrzebuje danych o ruchu wchodzącym/wychodzącym sieci do ustalenia rozmiaru na podstawie wydajności. Jeśli którekolwiek z powyższych danych użycia są niedostępne w programie vCenter Server, zalecenie dotyczące rozmiaru określone przez usługę Azure Migrate może nie być wiarygodne. W zależności od odsetka dostępnych punktów danych ocena zaufania dla oceny jest określana w następujący sposób:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

   Poniżej są dotyczące powodów dlaczego oceny można pobrać oceną zaufania niski:

- Nie profilujesz swojego środowiska przez czas trwania, dla którego tworzysz ocenę. Jeśli na przykład tworzysz ocenę z czasem trwania wydajności ustawionym na 1 dzień, musisz poczekać co najmniej dzień po uruchomieniu odnajdywania, aby zebrać wszystkie punkty danych.

- Kilka maszyn wirtualnych zostało wyłączonych w czasie, dla którego jest obliczana ocena. Jeśli którakolwiek maszyna wirtualna została odłączona od zasilania na pewien czas, nie będziemy mogli zebrać danych o wydajności dla tego okresu.

- Kilka maszyn wirtualnych zostało utworzonych w czasie, dla którego jest obliczana ocena. Jeśli na przykład tworzysz ocenę dla historii wydajności za ostatni miesiąc, ale kilka maszyn wirtualnych zostało utworzonych w środowisku tylko tydzień temu. W takich przypadkach historia wydajności nowych maszyn wirtualnych nie będzie dotyczyła całego czasu oceny.

  > [!NOTE]
  > Jeśli ocena zaufania dowolnej oceny jest poniżej 5 gwiazdek, zalecamy Poczekaj co najmniej jeden dzień dla urządzenia, aby przeprowadzić profilowanie w środowisku i następnie *ponownie Oblicz* oceny. Jeśli nie można wykonać powyższego, ustalanie rozmiaru na podstawie wydajności może nie być wiarygodne i zaleca się, aby przełączyć się na *ustalanie rozmiaru jako lokalnego*, zmieniając właściwości oceny.

## <a name="monthly-cost-estimation"></a>Szacowanie miesięcznych kosztów

Po zakończeniu zalecenia wymiarowania efektywnego usługi Azure Migrate oblicza koszty zasobów obliczeniowych i magazynu po migracji.

- **Koszt zasobów obliczeniowych**: Przy użyciu zalecanego rozmiaru maszyny Wirtualnej platformy Azure, usługa Azure Migrate używa interfejsu API rozliczeń do obliczania miesięczny koszt dla maszyny Wirtualnej. Obliczenie ma systemu operacyjnego, programu software assurance, zarezerwowane wystąpienia, maszyny Wirtualnej przestojów, lokalizacji i ustawienia waluty pod uwagę. Koszty są agregowane dla wszystkich maszyn, aby obliczyć całkowity miesięczny koszt obliczeń.
- **Koszt usługi Storage**: Miesięczny koszt magazynu dla maszyny jest obliczana przez zsumowanie miesięczny koszt wszystkich dysków dołączonych do maszyny. Usługa Azure Migrate oblicza całkowity koszt miesięczny magazynu przez agregowanie koszty magazynowania wszystkich maszyn. Obecnie obliczenie nie przyjmuje oferty określone w ustawieniach ocenę pod uwagę.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Kolejne kroki

[Utwórz ocenę dla maszyn wirtualnych VMware w środowisku lokalnym](tutorial-assessment-vmware.md)
