---
title: Architektura usługi Azure Backup
description: Zawiera omówienie architektury, składników i procesów używanych przez usługę Azure Backup.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: dacurwin
ms.openlocfilehash: 7c0a1650490a863f5b3a3cf09a5500d72359e7f1
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272002"
---
# <a name="azure-backup-architecture"></a>Architektura usługi Azure Backup

Możesz użyć [usługi Azure Backup](backup-overview.md) do tworzenia kopii zapasowych danych z platformy Microsoft Azure w chmurze. Ten artykuł zawiera podsumowanie architektury usługi Azure Backup, składników i procesów. 

## <a name="what-does-azure-backup-do"></a>Do czego służy usługa Azure Backup?

Usługa Azure Backup tworzy kopię zapasową danych, stan komputera i obciążeń uruchamianych na maszynach lokalnych i wystąpień maszyn wirtualnych (VM). Istnieje szereg scenariuszy usługi Azure Backup.

## <a name="how-does-azure-backup-work"></a>Jak działa usługa Azure Backup?

Można tworzyć kopie zapasowe maszyn i danych za pomocą pewnej liczby metod:

- **Tworzenie kopii zapasowych maszyn lokalnych**:
    - Kopię zapasową można wykonywać lokalnych maszyn Windows bezpośrednio na platformie Azure przy użyciu agenta usługi Azure kopia zapasowa Microsoft Azure Recovery Services (MARS). Maszyny z systemem Linux nie są obsługiwane.
    - Kopię zapasową można wykonywać maszyn lokalnych do tworzenia kopii zapasowej serwera (System Center Data Protection Manager (DPM) lub Microsoft Azure Backup serwera (MABS)). Następnie można utworzyć kopię zapasową kopii zapasowej serwera w magazynie usługi Recovery Services na platformie Azure.

- **Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure**:
    - Kopii zapasowych maszyn wirtualnych platformy Azure bezpośrednio. Usługa Azure Backup instaluje rozszerzenie kopii zapasowej agenta maszyny Wirtualnej platformy Azure, który jest uruchomiony na maszynie Wirtualnej. To rozszerzenie tworzy kopię zapasową całej maszyny Wirtualnej.
    - Możesz kopię zapasową określonych plików i folderów na maszynie Wirtualnej platformy Azure, uruchamiając agenta usług MARS.
    - Kopii zapasowych maszyn wirtualnych platformy Azure do serwera usługi Mab, która działa na platformie Azure, a następnie kopii zapasowych serwera usługi Mab do magazynu usługi Recovery Services.

Dowiedz się więcej o [co można utworzyć kopię](backup-overview.md) i o [obsługiwane scenariusze tworzenia kopii zapasowej](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Gdzie jest wykonywana kopia zapasowa danych?

Usługa Azure Backup przechowuje dane kopii zapasowej w magazynie usługi Recovery Services. Magazyn jest jednostką magazynu online na platformie Azure, który jest używany do przechowywania danych, takich jak kopie zapasowe, punkty odzyskiwania i zasady tworzenia kopii zapasowych.

Magazyny usługi Recovery Services oferują następujące funkcje:

- Magazyny ułatwiają organizowanie danych kopii zapasowych, przy jednoczesnym zmniejszeniu nakładów.
- W każdej subskrypcji platformy Azure można utworzyć maksymalnie 500 magazynów.
- Można monitorować elementy kopii zapasowej w magazynie, łącznie z maszyn wirtualnych platformy Azure i lokalnej maszyny.
- Dostęp za pomocą platformy Azure można zarządzać [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Należy określić, jak dane w magazynie jest replikowana w celu zapewnienia nadmiarowości:
    - **Magazyn lokalnie nadmiarowy (LRS)** : Aby chronić przed awariami w centrum danych, można użyć magazynu LRS. LRS replikuje dane do jednostki skali magazynu. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **Magazyn geograficznie nadmiarowy (GRS)** : Aby chronić przed awariami, można użyć GRS. Magazyn GRS replikuje dane do regionu pomocniczego. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Domyślnie magazynów usługi Recovery Services używają GRS. 

## <a name="backup-agents"></a>Agenci kopii zapasowej

Usługa Azure Backup udostępnia różnych agentów kopii zapasowej, w zależności od rodzaju maszyny jest tworzona kopia zapasowa:

**Agent** | **Szczegóły** 
--- | --- 
**Agenta usług MARS** | <ul><li>Uruchamia poszczególne środowiska lokalnego systemu Windows Server na maszynach w celu tworzenia kopii zapasowych plików, folderów i stanu systemu.</li> <li>Działa na maszynach wirtualnych platformy Azure, aby utworzyć kopię zapasową plików, folderów i stanu systemu.</li> <li>Jest uruchamiany na serwerach programu DPM/serwera usługi Mab do tworzenia kopii zapasowych programu DPM/serwera usługi Mab dysku magazynu lokalnego na platformę Azure.</li></ul> 
**Rozszerzenie maszyny Wirtualnej platformy Azure** | Działa na maszynach wirtualnych platformy Azure, aby utworzyć ich kopię zapasową w magazynie.

## <a name="backup-types"></a>Typy kopii zapasowych

W poniższej tabeli opisano różne typy kopii zapasowych i ich używania:

**Typ kopii zapasowej** | **Szczegóły** | **Użycie**
--- | --- | ---
**Full** | Pełna kopia zapasowa zawiera całe źródło danych. Trwa większe obciążenie przepustowości sieci niż różnicowe, czy przyrostowe kopie zapasowe. | Używany do tworzenia początkowej kopii zapasowej.
**Różnicowa** |  Różnicowa kopia zapasowa przechowuje bloki, które zmianie od czasu początkowej pełnej kopii zapasowej. Używa mniejszej ilości magazynu i sieci, a nie zachowuje nadmiarowych kopii niezmienionych danych.<br/><br/> Nieefektywne, ponieważ bloki danych, które są niezmienione między nowszych kopii zapasowych są przesyłane i przechowywane. | Nie są używane przez usługę Azure Backup.
**Przyrostowe** | Przyrostowe kopie zapasowe są przechowywane jedynie te bloki danych, które zmianie od czasu poprzedniej kopii zapasowej. Wysoka wydajność magazynu i sieci. <br/><br/> Przy użyciu przyrostowej kopii zapasowej nie ma potrzeby uzupełnienie z pełnymi kopiami zapasowymi. | Używane przez program DPM/serwera usługi Mab kopii zapasowych na dyskach, a w wszystkie kopie zapasowe na platformie Azure.

## <a name="sql-server-backup-types"></a>Typy kopii zapasowych programu SQL Server

W poniższej tabeli opisano różne typy kopii zapasowych baz danych programu SQL Server i jak często służą one:

**Typ kopii zapasowej** | **Szczegóły** | **Użycie**
--- | --- | ---
**Pełna kopia zapasowa** | pełna kopia zapasowa bazy danych powoduje utworzenie kopii zapasowej całej bazy danych. Zawiera on wszystkie dane w konkretnej bazy danych lub zestaw plików lub grup plików. Pełna kopia zapasowa zawiera również wystarczającą ilość dzienniki, aby odzyskać usuniętych danych. | Można wyzwalać maksymalnie jedną pełną kopię zapasową dziennie.<br/><br/> Można wprowadzić pełnej kopii zapasowej na interwał codziennie lub co tydzień.
**Różnicowa kopia zapasowa** | Różnicowa kopia zapasowa opiera się na najbardziej aktualne, poprzednie danych pełnej kopii zapasowej.<br/><br/> Przechwytuje dane, które uległy zmianie od czasu pełnej kopii zapasowej. |  Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.<br/><br/> Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej w tym samym dniu.
**Kopia zapasowa dziennika transakcji** | kopia zapasowa dziennika umożliwia przywrócenie do określonego punktu w czasie z dokładnością do sekundy. | Maksymalnie można skonfigurować kopie zapasowych dziennika transakcji co 15 minut.

### <a name="comparison-of-backup-types"></a>Porównanie typów kopii zapasowych

Użycie magazynu, cel czasu odzyskiwania i użycie sieci są różne dla każdego typu kopii zapasowej. Na poniższej ilustracji przedstawiono porównanie typów kopii zapasowej:

- Źródło danych A składa się z 10 magazynu bloków, A1 – A10, których kopię zapasową co miesiąc.
- Bloki A2, A3, A4 i A9 zmieniają się w trakcie pierwszego miesiąca, a blok A5 zmienia się w następnym miesiącu.
- Dla różnych kopii zapasowych w drugim miesiącu zmienione bloki A2, A3, A4 i a9 są kopie zapasowe są tworzone. W trzecim miesiącu jest ponownie wykonywana kopia zapasowa tych samych bloków, a dodatkowo zmienionego bloku A5. Kopie zapasowe zmienionych bloków będą ciągle wykonywane aż do czasu wykonania kolejnej pełnej kopii zapasowej.
- Tworzenia przyrostowych kopii zapasowych, w drugim miesiącu bloki A2, A3, A4 i A9 są oznaczane jako zmienione i przesyłane. W trzecim miesiącu jest oznaczany i transferowany jedynie zmieniony blok A5. 

![Obraz przedstawiający porównanie metod wykonywania kopii zapasowej](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkcje programu Kopia zapasowa

Poniższa tabela zawiera podsumowanie obsługiwanych funkcji dla różnych typów kopii zapasowej:

**Funkcja** | **Maszyny z systemem Windows Server w środowisku lokalnym (direct)** | **Maszyny wirtualne platformy Azure** | **Maszyny lub aplikacji za pomocą programu DPM/serwera usługi Mab**
--- | --- | --- | ---
Tworzenie kopii zapasowej do magazynu | ![Yes][green] | ![Yes][green] | ![Tak][green] 
Tworzenie kopii zapasowej na dysku programu DPM/serwera usługi Mab, następnie na platformie Azure | | | ![Tak][green] 
Kompresuj dane przesyłane do utworzenia kopii zapasowej | ![Tak][green] | Kompresja nie jest używana podczas transferu danych. Magazyn jest nieco zwiększony, ale przywracania jest szybsze.  | ![Tak][green] 
Uruchamianie tworzenia przyrostowych kopii zapasowych |![Tak][green] |![Yes][green] |![Yes][green] 
Wykonywanie kopii zapasowych deduplikowanych dysków | | | ![Częściowo][yellow]<br/><br/> Dla programu DPM/serwera usługi Mab serwery wdrożone tylko lokalnie. 

![Klucz tabeli](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Architektura: Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

1. Po włączeniu funkcji kopii zapasowej dla maszyny Wirtualnej platformy Azure, tworzenie kopii zapasowej uruchamia się zgodnie z harmonogramem, które określisz.
1. Podczas pierwszego tworzenia kopii zapasowej kopii zapasowej rozszerzenie jest zainstalowane na maszynie Wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych Windows rozszerzenie VMSnapshot jest zainstalowane.
    - W przypadku maszyn wirtualnych systemu Linux jest instalowany rozszerzenia VMSnapshot systemu Linux.
1. Rozszerzenie tworzy migawkę poziom miejsca do magazynowania. 
    - Dla maszyn wirtualnych Windows, które są uruchomione kopia zapasowa służy do koordynowania przy użyciu Windows woluminów w tle kopii Service (VSS) do spójnego na poziomie aplikacji migawki maszyny wirtualnej. Domyślnie kopie zapasowe pełnych kopii zapasowych usługi VSS. Jeśli kopia zapasowa jest nie można utworzyć migawki spójności aplikacji, jego tworzy migawkę spójna na poziomie plików.
    - W przypadku maszyn wirtualnych systemu Linux kopie zapasowe migawek spójna na poziomie plików. Migawki spójne z aplikacji musisz ręcznie dostosować wstępnie lub używanego po nim skrypty.
    - Kopia zapasowa jest zoptymalizowany przez utworzenie kopii zapasowej każdego dysku maszyny Wirtualnej w sposób równoległy. Dla każdego dysku, na których powstaje kopia zapasowa usługi Azure Backup odczytuje bloków na dysku i zapisuje tylko dane zmienione. 
1. Po migawki, dane są przesyłane do magazynu. 
    - Tylko bloki zmienione od czasu ostatniej kopii zapasowej są kopiowane dane.
    - Dane nie są szyfrowane. Usługa Azure Backup można utworzyć kopię zapasową maszyn wirtualnych platformy Azure, które zostały zaszyfrowane za pomocą usługi Azure Disk Encryption.
    - Dane migawki nie może zostać natychmiast skopiowany do magazynu. W godzinach szczytu tworzenie kopii zapasowej może potrwać kilka godzin. Łączny czas tworzenia kopii zapasowej dla maszyny Wirtualnej będzie mniej niż 24 godziny dla zasad tworzenia kopii zapasowej dzienny.
1. Po wysłaniu danych w magazynie, migawka jest usuwana, a punkt odzyskiwania jest tworzony.

Maszyny wirtualne platformy Azure wymagają dostępu do Internetu dla polecenia kontroli. Jeśli wykonujesz kopie zapasowe obciążeń wewnątrz maszyny Wirtualnej (na przykład SQL Server kopie zapasowe bazy danych), danych zaplecza również potrzebuje dostępu do Internetu. 

![Kopia zapasowa maszyn wirtualnych platformy Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: Bezpośrednie tworzenie kopii zapasowych maszyn z systemem Windows Server w środowisku lokalnym lub maszynie Wirtualnej platformy Azure, plików lub folderów

1. Aby skonfigurować scenariusz, Pobierz i zainstaluj agenta usług MARS na maszynie. Następnie wybierz co do tworzenia kopii zapasowych, kiedy zostanie wykonane kopie zapasowe i ile ich będzie znajdować się na platformie Azure.
1. Tworzenie początkowej kopii zapasowej jest uruchamiane zgodnie z ustawieniami tworzenia kopii zapasowej.
1. Agenta usług MARS korzysta z usług VSS, warto zrobić migawkę punktu w czasie z woluminów wybranych do utworzenia kopii zapasowej.
    - Agenta usług MARS używa tylko operacja zapisu systemu Windows, aby przechwycić migawkę.
    - Agent korzysta z wszystkie składniki zapisywania usługi VSS aplikacji, nie odzwierciedla migawek spójności aplikacji.
1. Po wykonaniu migawki za pomocą usługi VSS, agenta usług MARS tworzy wirtualny dysk twardy (VHD) w folderze pamięci podręcznej, który określiłeś w przypadku skonfigurowania kopii zapasowej. Agent przechowuje także sum kontrolnych dla każdego bloku danych.
1. Zgodnie z harmonogramem, które określisz, uruchamiane przyrostowe kopie zapasowe, chyba że zostanie uruchomione kopii zapasowej usługi ad hoc.
1. W przyrostowych kopii zapasowych zmienionych plików są identyfikowane i utworzeniu nowego wirtualnego dysku twardego. Wirtualny dysk twardy jest kompresowane i szyfrowane, a następnie przesyłane do magazynu.
1. Po zakończeniu przyrostowej kopii zapasowej, nowego wirtualnego dysku twardego jest scalany z dysku VHD utworzonego po replikacji początkowej. Ten wirtualny dysk twardy w scalonych zawiera najnowszy stan do użycia w porównaniu do bieżącej kopii zapasowej.

![Tworzenie kopii zapasowych maszyn systemu Windows Server w środowisku lokalnym za pomocą agenta usług MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: Tworzenie kopii zapasowych w programie DPM lub usłudze MABS

1. Agenta ochrony programu DPM lub MABS można zainstalować na maszynach, które mają być chronione. Następnie dodasz maszyn do grupy ochrony programu DPM.
    - Aby chronić maszyn lokalnych, serwer DPM lub MABS musi być w środowisku lokalnym.
    - Aby chronić maszyny wirtualne platformy Azure, serwer serwera usługi Mab musi znajdować się na platformie Azure, uruchomione jako maszyny Wirtualnej platformy Azure.
    - Za pomocą programu DPM/MABS można chronić kopii zapasowych woluminów, udziałów, plików i folderów. Umożliwia również ochronę stanu systemu na komputerze (od zera), a określone aplikacje można chronić za pomocą programu app-aware ustawienia kopii zapasowej.
1. Podczas konfigurowania ochrony dla maszyny lub aplikacji w programie DPM/serwera usługi Mab, możesz wybrać do utworzenia kopii zapasowej na dysku lokalnym serwera usługi Mab/DPM w celu przechowywania krótkoterminowego i na platformie Azure dla ochrony w trybie online. Należy również określić uruchamiania kopii zapasowej do lokalnego magazynu programu DPM/serwera usługi Mab i uruchamiania kopii zapasowej online na platformie Azure.
1. Dysku chronionego obciążenia kopia zapasowa jest tworzona na dyskach lokalnych serwera usługi Mab/DPM, zgodnie z harmonogramem, wskazana.
4. Dyski programu DPM/serwera usługi Mab kopię zapasową w magazynie przez agenta usług MARS, który jest uruchomiony na serwerze programu DPM/serwera usługi MAB.

![Tworzenie kopii zapasowych maszyn i obciążeń chronionych przez program DPM lub MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Magazynu maszyny Wirtualnej platformy Azure

Dysków maszyn wirtualnych platformy Azure do przechowywania ich systemu operacyjnego, aplikacji i danych. Każda maszyna wirtualna platformy Azure ma co najmniej dwa dyski: dysk systemu operacyjnego i tymczasowy dysk. Maszyny wirtualne platformy Azure może być również dyski z danymi dla danych aplikacji. Dyski są przechowywane jako pliki VHD.

- Wirtualne dyski twarde są przechowywane jako stronicowe obiekty BLOB na kontach magazynu w warstwie standardowa lub premium na platformie Azure:
    - **Magazynu w warstwie standardowa:** Niezawodne, niedrogie dyskowa dla maszyn wirtualnych uruchomionych obciążeń, które nie są wrażliwe na czas oczekiwania. Dyski standardowe dysków półprzewodnikowych (SSD) lub standardowych dysków twardych (HDD), można użyć magazynu w warstwie standardowa.
    - **Usługa Premium storage:** Obsługę przez dyski o wysokiej wydajności. Korzysta z dysków SSD w warstwie premium.
- Istnieją warstwy wydajności dla dysków:
    - **Dysk w warstwie standardowa dysk twardy:** Obsługiwane przez dyski twarde i użyte na potrzeby ekonomicznego magazynowania.
    - **Standardowy dysk SSD:** Łączy elementy dysków SSD w warstwie premium i standardowe dyski HDD. Oferuje bardziej spójną wydajność i niezawodność niż dysk twardy, ale nadal ekonomiczne.
    - **Dysk SSD w warstwie Premium:** Obsługiwane przez dyski SSD i udostępnia o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych, które są uruchomione I O dużych obciążeń wejścia /.
- Dyski mogą być zarządzane lub niezarządzane:
    - **Dyski niezarządzane:** Tradycyjny typ dysków używany przez maszyny wirtualne. W przypadku tych dysków utworzyć konto magazynu i je wprowadzić podczas tworzenia dysku. Następnie należy opracować sposób zmaksymalizowania zasobów magazynu dla maszyn wirtualnych.
    - **Dyski zarządzane:** Platforma Azure tworzy i zarządza kontami magazynu. Należy określić rozmiar i wydajność warstwy dysków, a platforma Azure utworzy dyski zarządzane dla Ciebie. Dodawanie dysków i skalować maszyny wirtualne, konta magazynu są obsługiwane przez platformę Azure.

Aby uzyskać więcej informacji o dysku i typy dostępnego miejsca na dysku dla maszyn wirtualnych zobacz następujące artykuły:

- [Usługi Azure managed disks dla maszyn wirtualnych Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Usługi Azure managed disks dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Typy dostępnych dysków dla maszyn wirtualnych](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure dzięki usłudze premium storage 

Korzystając z usługi premium storage z usługą Azure Backup można tworzyć kopie zapasowe maszyn wirtualnych platformy Azure:

- Podczas procesu tworzenia kopii zapasowych maszyn wirtualnych dzięki usłudze premium storage usługa Backup tworzy tymczasową lokalizację, o nazwie *AzureBackup -* , w ramach konta magazynu. Rozmiar lokalizacji przejściowej jest równy rozmiar migawki punktu odzyskiwania.
- Upewnij się, że konta usługi premium storage ma odpowiednią ilością wolnego miejsca do obsługi tymczasowej lokalizacji przejściowej. [Dowiedz się więcej](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Nie należy modyfikować lokalizacji tymczasowej.
- Po zakończeniu zadania tworzenia kopii zapasowej Lokalizacja tymczasowa zostanie usunięta.
- Cena przestrzeni dyskowej użytej do lokalizacji tymczasowej jest zgodna z [cennikiem usługi premium storage](../virtual-machines/windows/disks-types.md#billing).

Po przywróceniu maszyn wirtualnych platformy Azure przy użyciu usługi premium storage można przywrócić je do wersji premium lub magazynu w warstwie standardowa. Zazwyczaj można będzie je przywrócić do usługi premium storage. Ale jeśli potrzebujesz tylko podzbiór plików z maszyny Wirtualnej, może być oszczędności finansowe, aby przywrócić je do magazynu w warstwie standardowa.

### <a name="back-up-and-restore-managed-disks"></a>Tworzenie kopii zapasowej i przywracanie dysków zarządzanych

Można tworzyć kopie zapasowe maszyn wirtualnych platformy Azure z dyskami zarządzanymi:

- Utworzono kopię zapasową maszyn wirtualnych z dyskami zarządzanymi w taki sam sposób, że wykonać żadnych innych maszyn wirtualnych platformy Azure. Można utworzyć kopię zapasową maszyny Wirtualnej bezpośrednio z poziomu ustawień maszyny wirtualnej, lub można włączyć kopii zapasowej dla maszyn wirtualnych w magazynie usługi Recovery Services.
- Kopie zapasowe maszyn wirtualnych możesz tworzyć na dyskach zarządzanych za pomocą kolekcji RestorePoint tworzonych na tych dyskach.
- Usługa Azure Backup obsługuje również tworzenie kopii zapasowych maszyn wirtualnych z dyskami zarządzanymi, które zostały zaszyfrowane za pomocą usługi Azure Disk Encryption.

Po przywróceniu maszyn wirtualnych z dyskami zarządzanymi, można przywrócić do całej maszyny Wirtualnej z dyskami zarządzanymi lub konta magazynu:

- Podczas procesu przywracania Azure obsługuje dyski zarządzane. Jeśli używasz opcji konta magazynu, możesz zarządzać konta magazynu, który jest tworzony podczas procesu przywracania.
- W przypadku przywracania zarządzanych maszynę Wirtualną, która jest zaszyfrowany, upewnić się, że klucze maszyny Wirtualnej i wpisy tajne istnieje w magazynie kluczy przed rozpoczęciem procesu przywracania.

## <a name="next-steps"></a>Kolejne kroki

- Macierz obsługi, aby przejrzeć [informacje na temat obsługiwanych funkcji i ograniczenia dotyczące scenariusze tworzenia kopii zapasowej](backup-support-matrix.md).
- Konfigurowanie kopii zapasowej dla jednego z tych scenariuszy:
    - [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md).
    - [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
    - [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
    - [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

