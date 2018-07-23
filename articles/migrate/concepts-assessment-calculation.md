---
title: Obliczenia dotyczące oceny w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie obliczenia dotyczące oceny usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/20/2018
ms.author: raynew
ms.openlocfilehash: 890614133649762788418c538bd22bb6ffc425bf
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173762"
---
# <a name="assessment-calculations"></a>Obliczenia dotyczące oceny

[Usługa Azure Migrate](migrate-overview.md) ocenia obciążenia lokalne pod kątem migracji na platformę Azure. Ten artykuł zawiera informacje dotyczące sposobu obliczania ocen.


## <a name="overview"></a>Przegląd

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
**Typ rozruchu** | Platforma Azure obsługuje maszyny wirtualne z typem rozruchu systemu BIOS i UEFI nie. | Warunkowo gotowa na platformę Azure, jeśli typ rozruchu to UEFI.
**Liczba rdzeni** | Liczby rdzeni na komputerach musi być równa lub mniejsza niż maksymalna liczba rdzeni (32) obsługiwanych na Maszynie wirtualnej platformy Azure.<br/><br/> Jeśli Historia wydajności jest dostępny, usługa Azure Migrate uwzględnia rdzenie wykorzystywanych do porównania. Jeśli współczynnik komfortu jest określony w ustawieniach oceny, liczby rdzeni wykorzystywanych jest mnożony przez współczynnik komfortu.<br/><br/> Jeśli nie ma żadnych historii wydajności, usługę Azure migrate przydzielone rdzenie, bez stosowania współczynnik komfortu. | Brak gotowości, jeśli liczba rdzeni jest większa niż 32.
**Pamięć** | Rozmiar pamięci maszyny musi być równa lub mniejsza niż maksymalna ilość pamięci (448 GB) dozwolone na Maszynie wirtualnej platformy Azure. <br/><br/> Jeśli Historia wydajności jest dostępny, usługa Azure Migrate uwzględnia pamięci wykorzystywanych do porównania. Jeśli zostanie określona współczynnik komfortu, wykorzystywanych pamięci jest mnożony przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii ilość przydzielonej pamięci jest używany bez stosowania współczynnik komfortu.<br/><br/> | Brak gotowości, jeśli rozmiar pamięci jest większa od 448 GB.
**Dysk magazynu** | Przydzielony rozmiar dysku musi być 4 TB (4096 GB) lub mniej.<br/><br/> Liczba dysków dołączonych do maszyny musi być 65 lub mniej, łącznie z dysku systemu operacyjnego. | Nie jest gotowy, jeśli dysk ma rozmiar większy niż 4 TB lub jeśli istnieje więcej niż 65 dysków dołączonych do maszyny.
**Sieć** | Maszyna musi być 32 lub najwyżej dwie karty sieciowe podłączone do niego. | Nie jest gotowy, jeśli komputer ma więcej niż 32 kart sieciowych

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
Windows Server 2003-2008 | Te systemy operacyjne przeszły koniec okresu pomocy technicznej i trzeba [umowy pomocy technicznej niestandardowe (CSA)](https://aka.ms/WSosstatement) obsługi na platformie Azure. | Warunkowo gotowa na platformę Azure, Rozważ uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Te systemy operacyjne przeszły ich zakończenia okresu pomocy technicznej, maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure. | Warunkowo gotowa na platformę Azure, zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Klient Windows 7, 8 i 10 | Platforma Azure oferuje obsługi subskrypcji programu Visual Studio tylko. | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Te systemy operacyjne przeszły ich zakończenia okresu pomocy technicznej, maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure. | Warunkowo gotowa na platformę Azure, zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Linux | Platforma Azure wspiera te [systemów operacyjnych Linux](../virtual-machines/linux/endorsed-distros.md). Innych systemów operacyjnych Linux można uruchomić na platformie Azure, ale zaleca się uaktualnienie systemu operacyjnego do zalecanych wersji przed migracją na platformę Azure. | Gotowa na platformę Azure, jeśli wersja jest zalecane.<br/><br/>Warunkowo gotowa, jeśli wersja nie jest zalecane.
Inne systemy operacyjne<br/><br/> np. Oracle Solaris, itp. system operacyjny Mac firmy Apple, FreeBSD itp. | Azure nie popiera tych systemów operacyjnych. Maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure. | Warunkowo gotowa na platformę Azure, zalecane jest instalacji obsługiwanego systemu operacyjnego przed migracją na platformę Azure.  
System operacyjny określony jako *innych* w programie vCenter Server | Usługa Azure Migrate nie może zidentyfikować w takim przypadku system operacyjny. | Nieznana gotowość. Upewnij się, że system operacyjny działający na maszynie Wirtualnej jest obsługiwana na platformie Azure.
32-bitowych systemach operacyjnych | Maszynę można uruchomić na platformie Azure, ale Azure mogą nie zapewnić pełną pomoc techniczną. | Warunkowo gotowa na platformę Azure, Rozważ uaktualnienie systemu operacyjnego maszyny z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego przed migracją na platformę Azure.

## <a name="sizing"></a>Zmiana rozmiaru

Po maszyny jest oznaczony jako gotowa na platformę Azure, usługa Azure Migrate rozmiarów maszyny Wirtualnej i jej dysków na platformie Azure. Jeśli kryterium ustalania rozmiaru określone we właściwościach oceny do ustalenia rozmiaru na podstawie wydajności Usługa Azure Migrate uwzględnia historii wydajności maszyn do identyfikowania typu dysk i rozmiar maszyny Wirtualnej na platformie Azure. Ta metoda jest przydatne w scenariuszach, gdzie ma nadmierną alokację maszyny Wirtualnej w środowisku lokalnym, ale wykorzystanie jest niskie, i chcesz odpowiedni rozmiar maszyn wirtualnych na platformie Azure koszty.

> [!NOTE]
> Usługa Azure Migrate umożliwia zbieranie informacji o historii wydajności lokalnych maszyn wirtualnych z programu vCenter Server. Aby zapewnić dokładne doboru, upewnij się, że ustawienie statystyk w programie vCenter Server jest ustawione na poziom 3 i poczekaj na co najmniej jeden dzień przed rozpoczęciem odnajdowania lokalnych maszyn wirtualnych. Jeśli ustawienie statystyk w programie vCenter Server jest poniżej poziomu 3, nie są zbierane dane dotyczące wydajności dla dysku i sieci.

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

- **Obliczenia**: po obliczane są wymagania dotyczące magazynu i sieci, usługa Azure Migrate uwzględnia wymagania procesora CPU i pamięci można znaleźć odpowiedniego rozmiaru maszyny Wirtualnej na platformie Azure.
    - Usługa Azure Migrate analizuje wykorzystywanych rdzeni i pamięci, a także obowiązują współczynnik komfortu, aby pobrać efektywne rdzeni i pamięci. Na podstawie tego numeru, próbuje odnaleźć odpowiedniego rozmiaru maszyny Wirtualnej na platformie Azure.
    - Jeśli zostanie znaleziony żaden odpowiedni rozmiar, maszyny jest oznaczony jako nieodpowiedni dla platformy Azure.
    - Jeśli zostanie znaleziony odpowiedni rozmiar, usługa Azure Migrate dotyczy obliczeń magazynu i sieci. Następnie stosuje lokalizacji i ustawień warstwy cenowej, dla końcowego zalecenie dotyczące rozmiaru maszyny Wirtualnej.
    - Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.

### <a name="as-on-premises-sizing"></a>Jako lokalne zmiany rozmiaru
Jeśli kryterium ustalania rozmiaru to *jako lokalnego rozmiaru*, usługa Azure Migrate nie należy wziąć pod uwagę historię wydajności maszyn wirtualnych i dysków i przydziela SKU maszyny Wirtualnej na platformie Azure, w zależności od rozmiaru przydzielone lokalnie. Podobnie w przypadku dla rozmiaru dysku, sprawdza typ magazynu określona we właściwościach oceny (standardowa/Premium) i w związku z tym zaleca typ dysku. Domyślny typ magazynu jest dysków w warstwie Premium.

### <a name="confidence-rating"></a>Ocena zaufania
Każda oparte na wydajności ocena w usłudze Azure Migrate jest skojarzona z oceną zaufania obejmującą zakres od 1 gwiazdki do 5 gwiazdek (1 gwiazdka to najniższa i 5 gwiazdek — najwyższa). Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny. Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate. Ocena zaufania nie stosuje się do oceny lokalne.

W przypadku ustalania rozmiaru na podstawie wydajności usługa Azure Migrate potrzebuje danych o użyciu procesora CPU i pamięci maszyny wirtualnej. Ponadto dla każdego dysku podłączonego do maszyny wirtualnej potrzebuje ona danych o liczbie operacji odczytu/zapisu na sekundę i przepływności. Analogicznie dla każdej karty sieciowej podłączonej do maszyny wirtualnej usługa Azure Migrate potrzebuje danych o ruchu wchodzącym/wychodzącym sieci do ustalenia rozmiaru na podstawie wydajności. Jeśli którekolwiek z powyższych danych użycia są niedostępne w programie vCenter Server, zalecenie dotyczące rozmiaru określone przez usługę Azure Migrate może nie być wiarygodne. W zależności od odsetka dostępnych punktów danych ocena zaufania dla oceny jest określana w następujący sposób:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

Ocena może nie mieć dostępnych wszystkich punktów danych z jednego z następujących powodów:
- Ustawienie statystyk w programie vCenter Server jest inne niż poziom 3. Jeśli ustawienie statystyk w programie vCenter Server jest mniejsze niż poziom 3, dane o wydajności dysku i sieci nie są zbierane z programu vCenter Server. W takim przypadku zalecenie określane przez usługę Azure Migrate dla dysku i sieci nie opiera się na użyciu. Bez uwzględniania liczby operacji we/wy na sekundę/przepływności dysku usługa Azure Migrate nie może określić, czy dysk będzie potrzebować dysku w warstwie Premium na platformie Azure, dlatego w tym przypadku usługa Azure Migrate zaleca wszystkie dyski w warstwie Standardowa.
- Ustawienie statystyk w programie vCenter Server zostało ustawione na poziom 3 przez krótszy czas przed rozpoczęciem odnajdywania. Rozważmy na przykład scenariusz, w którym dzisiaj zmienisz poziom ustawień statystyk na 3 i jutro rozpoczniesz odnajdowanie przy użyciu urządzenia modułu zbierającego (po 24 godzinach). Jeśli tworzysz ocenę dla jednego dnia, masz wszystkie punkty danych i oceną zaufania dla oceny będzie 5 gwiazdek. Ale jeśli zmieniasz czas trwania wydajności we właściwościach oceny na jeden miesiąc, ocena zaufania spada, ponieważ dane o wydajności dysku i sieci dla ostatniego miesiąca byłyby niedostępne. Jeśli chcesz wziąć pod uwagę dane wydajności za ostatni miesiąc, zaleca się utrzymanie ustawienia statystyk programu vCenter Server na poziomie 3 przez jeden miesiąc przed rozpoczęciem odnajdywania.
- Kilka maszyn wirtualnych zostało wyłączonych w czasie, dla którego jest obliczana ocena. Jeśli którakolwiek maszyna wirtualna została odłączona od zasilania na pewien czas, program vCenter Server nie będzie miał danych o wydajności dla tego okresu.
- Kilka maszyn wirtualnych zostało utworzonych w czasie, dla którego jest obliczana ocena. Jeśli na przykład tworzysz ocenę dla historii wydajności za ostatni miesiąc, ale kilka maszyn wirtualnych zostało utworzonych w środowisku tylko tydzień temu. W takich przypadkach historia wydajności nowych maszyn wirtualnych nie będzie dotyczyła całego czasu oceny.

> [!NOTE]
> Jeśli ocena zaufania dowolnej oceny jest poniżej 4 gwiazdek, zalecamy zmianę ustawienia poziomu statystyk programu vCenter Server na 3, odczekanie przez czas, który chcesz wziąć pod uwagę podczas oceny (1 dzień/1 tydzień/1 miesiąc), a następnie przeprowadzenie odnajdywania i oceny. Jeśli nie można wykonać powyższego, ustalanie rozmiaru na podstawie wydajności może nie być wiarygodne i zaleca się, aby przełączyć się na *ustalanie rozmiaru jako lokalnego*, zmieniając właściwości oceny.

## <a name="monthly-cost-estimation"></a>Szacowanie miesięcznych kosztów

Po zakończeniu zalecenia wymiarowania efektywnego usługi Azure Migrate oblicza koszty zasobów obliczeniowych i magazynu po migracji.

- **Koszt zasobów obliczeniowych**: przy użyciu zalecanego rozmiaru maszyny Wirtualnej platformy Azure, usługę Azure migrate interfejs API rozliczeń można obliczyć koszt miesięczny dla maszyny Wirtualnej. Obliczenie ma systemu operacyjnego, programu software assurance, zarezerwowane wystąpienia, maszyny Wirtualnej przestojów, lokalizacji i ustawienia waluty pod uwagę. Koszty są agregowane dla wszystkich maszyn, aby obliczyć całkowity miesięczny koszt obliczeń.
- **Koszt usługi Storage**: Magazyn miesięczny koszt maszyny jest obliczana przez zsumowanie miesięczny koszt wszystkich dysków dołączonych do maszyny. Usługa Azure Migrate oblicza całkowity koszt miesięczny magazynu przez agregowanie koszty magazynowania wszystkich maszyn. Obecnie obliczenie nie przyjmuje oferty określone w ustawieniach ocenę pod uwagę.

Koszty są wyświetlane w walucie określonej w ustawieniach oceny.


## <a name="next-steps"></a>Kolejne kroki

[Utwórz ocenę dla maszyn wirtualnych VMware w środowisku lokalnym](tutorial-assessment-vmware.md)
