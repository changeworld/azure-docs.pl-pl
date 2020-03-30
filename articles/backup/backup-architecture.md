---
title: Omówienie architektury
description: Zawiera omówienie architektury, składników i procesów używanych przez usługę Azure Backup.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273620"
---
# <a name="azure-backup-architecture-and-components"></a>Architektura i składniki usługi Azure Backup

Za pomocą [usługi Azure Backup](backup-overview.md) można wykonać kopię zapasową danych na platformie chmurowej platformy Microsoft Azure. W tym artykule podsumowano architekturę, składniki i procesy usługi Azure Backup.

## <a name="what-does-azure-backup-do"></a>Do czego działa usługa Azure Backup?

Usługa Azure Backup tworzą kopię zapasową danych, stanu komputera i obciążeń uruchomionych na komputerach lokalnych i wystąpieniach maszyny wirtualnej platformy Azure(VM). Istnieje wiele scenariuszy usługi Azure Backup.

## <a name="how-does-azure-backup-work"></a>Jak działa usługa Azure Backup?

Można wyw kopii zapasowej maszyn i danych przy użyciu wielu metod:

- **Zapasowe maszyny lokalne:**
  - Możesz wykonać kopię zapasową lokalnych maszyn z systemem Windows bezpośrednio na platformie Azure przy użyciu agenta usługi MARS (Azure Backup Microsoft Azure Recovery Services). Maszyny z systemem Linux nie są obsługiwane.
  - Można wykonać kopię zapasową komputerów lokalnych na serwerze kopii zapasowej — Menedżer ochrony danych systemu Center (DPM) lub Microsoft Azure Backup Server (MABS). Następnie można wykonać kopię zapasową serwera kopii zapasowej do magazynu usług odzyskiwania na platformie Azure.

- **19.00.2019**
  - Można bezpośrednio wyw kopii zapasowej maszyn wirtualnych platformy Azure. Usługa Azure Backup instaluje rozszerzenie kopii zapasowej agenta maszyny Wirtualnej platformy Azure, który jest uruchomiony na maszynie wirtualnej. To rozszerzenie jest kopii zapasowej całej maszyny Wirtualnej.
  - Można wywrzeć kopie zapasowe określonych plików i folderów na maszynie Wirtualnej platformy Azure, uruchamiając agenta MARS.
  - Można wygenerować kopii zapasowej maszyn wirtualnych platformy Azure do mabs, który jest uruchomiony na platformie Azure, a następnie można następnie kopii zapasowej mabs do magazynu usług odzyskiwania.

Dowiedz się więcej o [tym, co można wykonać kopię zapasową](backup-overview.md) i o [obsługiwanych scenariuszach tworzenia kopii zapasowych](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Gdzie jest kopia zapasową danych?

Usługa Azure Backup przechowuje kopie zapasowe danych w magazynie usług odzyskiwania. Magazyn jest jednostką magazynu online na platformie Azure, która jest używana do przechowywania danych, takich jak kopie zapasowe, punkty odzyskiwania i zasady tworzenia kopii zapasowych.

Magazyny usług odzyskiwania mają następujące funkcje:

- Magazyny ułatwiają organizowanie danych kopii zapasowej, minimalizując jednocześnie obciążenie związane z zarządzaniem.
- W każdej subskrypcji platformy Azure można utworzyć maksymalnie 500 magazynów.
- Można monitorować elementy kopii zapasowej w magazynie, w tym maszyn wirtualnych platformy Azure i na komputerach lokalnych.
- Dostęp do magazynu można zarządzać za pomocą [kontroli dostępu opartej na rolach platformy Azure (RBAC).](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- Można określić sposób replikowania danych w przechowalni w celu zapewnienia nadmiarowości:
  - **Lokalnie nadmiarowa pamięć masowa (LRS)**: Aby chronić przed awarią w centrum danych, można użyć lrs. LRS replikuje dane do jednostki skalowania magazynu. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Magazyn geograficznie nadmiarowy (GRS)**: Aby chronić przed awariami w całym regionie, można użyć GRS. GRS replikuje dane do regionu pomocniczego. [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Domyślnie magazyny usług odzyskiwania używają GRS.

## <a name="backup-agents"></a>Agenci tworzenia kopii zapasowych

Usługa Azure Backup udostępnia różnych agentów kopii zapasowych, w zależności od typu kopii zapasowej komputera:

**Agent** | **Szczegóły**
--- | ---
**Agent MARS** | <ul><li>Działa na poszczególnych komputerach z systemem Windows Server w celu utworzenia kopii zapasowej plików, folderów i stanu systemu.</li> <li>Działa na maszynach wirtualnych platformy Azure, aby zamieść zapasową plików, folderów i stanu systemu.</li> <li>Działa na serwerach programu DPM/MABS w celu utworzenia kopii zapasowej dysku magazynu lokalnego programu DPM/MABS na platformie Azure.</li></ul>
**Rozszerzenie maszyny Wirtualnej platformy Azure** | Działa na maszynach wirtualnych platformy Azure, aby przywrócić ich do magazynu.

## <a name="backup-types"></a>Typy kopii zapasowych

W poniższej tabeli wyjaśniono różne typy kopii zapasowych i kiedy są używane:

**Typ kopii zapasowej** | **Szczegóły** | **Użycia**
--- | --- | ---
**Pełne** | Pełna kopia zapasowa zawiera całe źródło danych. Wymaga większej przepustowości sieci niż różnicowe lub przyrostowe kopie zapasowe. | Służy do wstępnej kopii zapasowej.
**Różnicowe** |  Różnicowa kopia zapasowa przechowuje bloki, które uległy zmianie od czasu początkowej pełnej kopii zapasowej. Używa mniejszej ilości sieci i magazynu i nie przechowuje nadmiarowych kopii niezmienionych danych.<br/><br/> Nieefektywne, ponieważ bloki danych, które są niezmienione między późniejszymi kopiami zapasowymi są przesyłane i przechowywane. | Nie jest używany przez usługę Azure Backup.
**Przyrostowe** | Przyrostowa kopia zapasowa przechowuje tylko bloki danych, które uległy zmianie od czasu poprzedniej kopii zapasowej. Wysoka wydajność pamięci masowej i sieci. <br/><br/> Dzięki przyrostowej kopii zapasowej nie ma potrzeby uzupełniania o pełne kopie zapasowe. | Używane przez program DPM/MABS do tworzenia kopii zapasowych dysków i używane we wszystkich kopiach zapasowych na platformie Azure. Nie jest używany do tworzenia kopii zapasowych programu SQL Server.

## <a name="sql-server-backup-types"></a>Typy kopii zapasowych programu SQL Server

W poniższej tabeli wyjaśniono różne typy kopii zapasowych używanych w bazach danych programu SQL Server i często używane:

**Typ kopii zapasowej** | **Szczegóły** | **Użycia**
--- | --- | ---
**Pełna kopia zapasowa** | pełna kopia zapasowa bazy danych powoduje utworzenie kopii zapasowej całej bazy danych. Zawiera wszystkie dane w określonej bazie danych lub w zestawie grup plików lub plików. Pełna kopia zapasowa zawiera również wystarczającą liczbę dzienników, aby odzyskać te dane. | Można wyzwalać maksymalnie jedną pełną kopię zapasową dziennie.<br/><br/> Możesz utworzyć pełną kopię zapasową w odstępach dziennych lub tygodniowych.
**Różnicowa kopia zapasowa** | Różnicowa kopia zapasowa jest oparta na najnowszej, poprzedniej pełnej kopii zapasowej danych.<br/><br/> Przechwytuje tylko dane, które zostały zmienione od czasu pełnej kopii zapasowej. |  Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.<br/><br/> Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej w tym samym dniu.
**Kopia zapasowa dziennika transakcji** | kopia zapasowa dziennika umożliwia przywrócenie do określonego punktu w czasie z dokładnością do sekundy. | Maksymalnie można skonfigurować kopie zapasowych dziennika transakcji co 15 minut.

### <a name="comparison-of-backup-types"></a>Porównanie typów kopii zapasowych

Zużycie magazynu, cel czasu odzyskiwania (RTO) i zużycie sieci różni się dla każdego typu kopii zapasowej. Na poniższej ilustracji przedstawiono porównanie typów kopii zapasowych:

- Źródło danych A składa się z 10 bloków pamięci masowej, A1-A10, które są archiwizowane co miesiąc.
- Bloki A2, A3, A4 i A9 zmieniają się w trakcie pierwszego miesiąca, a blok A5 zmienia się w następnym miesiącu.
- W przypadku różnicowych kopii zapasowych w drugim miesiącu wykonaną kopię zapasową zmienionych bloków A2, A3, A4 i A9. W trzecim miesiącu jest ponownie wykonywana kopia zapasowa tych samych bloków, a dodatkowo zmienionego bloku A5. Kopie zapasowe zmienionych bloków będą ciągle wykonywane aż do czasu wykonania kolejnej pełnej kopii zapasowej.
- W przypadku przyrostowych kopii zapasowych w drugim miesiącu bloki A2, A3, A4 i A9 są oznaczane jako zmienione i przeniesione. W trzecim miesiącu jest oznaczany i transferowany jedynie zmieniony blok A5.

![Obraz przedstawiający porównania metod tworzenia kopii zapasowych](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkcje tworzenia kopii zapasowych

W poniższej tabeli podsumowano obsługiwane funkcje dla różnych typów kopii zapasowych:

**Funkcja** | **Bezpośrednia kopia zapasowa plików i folderów (przy użyciu agenta MARS)** | **Kopia zapasowa maszyny wirtualnej platformy Azure** | **Maszyny lub aplikacje z programem DPM/MABS**
--- | --- | --- | ---
Śp. | ![Tak][green] | ![Tak][green] | ![Tak][green]
Aby przejść do dysku programu DPM/MABS, a następnie na platformie Azure | | | ![Tak][green]
Kompresowanie danych wysłanych do tworzenia kopii zapasowych | ![Tak][green] | Podczas przesyłania danych nie jest używana kompresja. Przechowywanie jest nieco zawyżone, ale przywrócenie jest szybsze.  | ![Tak][green]
Uruchamianie przyrostowej kopii zapasowej |![Tak][green] |![Tak][green] |![Tak][green]
Dzmów zduplikowanymi dyskami | | | ![Częściowo][yellow]<br/><br/> W przypadku serwerów programu DPM/MABS wdrożonych tylko lokalnie.

![Klucz tabeli](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Podstawowe informacje o polityce tworzenia kopii zapasowych

- Zasady tworzenia kopii zapasowych są tworzone dla przechowalni.
- Można utworzyć zasady tworzenia kopii zapasowych dla tworzenia kopii zapasowych następujących obciążeń
  - Maszyna wirtualna platformy Azure
  - SQL na maszynie Wirtualnej platformy Azure
  - Udział plików platformy Azure
- Zasady można przypisać do wielu zasobów. Zasady tworzenia kopii zapasowych maszyn wirtualnych platformy Azure mogą służyć do ochrony wielu maszyn wirtualnych platformy Azure.
- Zasada składa się z dwóch składników
  - Harmonogram: Kiedy wykonać kopię zapasową
  - Przechowywanie: Jak długo każda kopia zapasowa powinna być zachowywana.
- Harmonogram można zdefiniować jako "dzienny" lub "tygodniowy" z określonym punktem czasu.
- Przechowywanie można zdefiniować dla "dziennych", "tygodniowych", "miesięcznych", "500" punktów zapasowych.
- "co tydzień" odnosi się do kopii zapasowej w określonym dniu tygodnia, "miesięczny" oznacza kopię zapasową w określonym dniu miesiąca, a "roczny" odnosi się do kopii zapasowej w określonym dniu roku.
- Przechowywanie dla "miesięcznych", "rocznym" punktów kopii zapasowej jest określane jako "LongTermRetention".
- Podczas tworzenia magazynu tworzone są również zasady dotyczące kopii zapasowych maszyn wirtualnych platformy Azure o nazwie "DefaultPolicy" i mogą być używane do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.

## <a name="architecture-built-in-azure-vm-backup"></a>Architektura: wbudowana kopia zapasowa maszyny wirtualnej platformy Azure

1. Po włączeniu kopii zapasowej dla maszyny Wirtualnej platformy Azure kopia zapasowa jest uruchamiana zgodnie z określonym harmonogramem.
1. Podczas pierwszej kopii zapasowej rozszerzenie kopii zapasowej jest instalowane na maszynie Wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest zainstalowane rozszerzenie VMSnapshot.
    - W przypadku maszyn wirtualnych z systemem Linux jest zainstalowane rozszerzenie VMSnapshot Linux.
1. Rozszerzenie wykonuje migawkę na poziomie magazynu.
    - W przypadku uruchomionych maszyn wirtualnych z systemem Windows współrzędne kopii zapasowej z usługą kopiowania woluminów w tle systemu Windows (VSS) w celu wykonaniu spójnej dla aplikacji migawki maszyny wirtualnej. Domyślnie kopia zapasowa wykonuje pełne kopie zapasowe usługi VSS. Jeśli kopia zapasowa nie może wykonać migawki spójnej z aplikacją, wykonuje migawkę spójną z plikami.
    - W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa wykonuje migawkę spójną z plikami. W przypadku migawek spójnych z aplikacją należy ręcznie dostosować skrypty przed/post.
    - Kopia zapasowa jest optymalizowana przez równoległe tworzenie kopii zapasowych każdego dysku maszyny Wirtualnej. Dla każdego dysku, na który wykona się kopię zapasową, usługa Azure Backup odczytuje bloki na dysku i przechowuje tylko zmienione dane.
1. Po zrobienie migawki dane są przesyłane do magazynu.
    - Kopiowane są tylko bloki danych, które uległy zmianie od czasu ostatniej kopii zapasowej.
    - Dane nie są szyfrowane. Usługa Azure Backup może wykonać kopię zapasową maszyn wirtualnych platformy Azure, które zostały zaszyfrowane przy użyciu szyfrowania dysków platformy Azure.
    - Dane migawki mogą nie zostać natychmiast skopiowane do przechowalni. W godzinach szczytu kopia zapasowa może potrwać kilka godzin. Całkowity czas tworzenia kopii zapasowej maszyny Wirtualnej będzie krótszy niż 24 godziny w przypadku zasad dziennego tworzenia kopii zapasowych.
1. Po wysłaniu danych do repozytorium tworzony jest punkt odzyskiwania. Domyślnie migawki są zachowywane przez dwa dni przed ich usunięciem. Ta funkcja umożliwia przywracanie operacji z tych migawek, a tym samym wycinanie czasów przywracania. Skraca czas potrzebny do przekształcania i kopiowania danych z magazynu. Zobacz [Możliwości natychmiastowego przywracania kopii zapasowej platformy Azure](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

Nie trzeba jawnie zezwalać na łączność z Internetem, aby wykonać zapasową maszyn wirtualnych platformy Azure.

![Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: bezpośrednia kopia zapasowa lokalnych komputerów z systemem Windows Server lub plików lub folderów maszyn wirtualnych platformy Azure

1. Aby skonfigurować scenariusz, należy pobrać i zainstalować agenta MARS na komputerze. Następnie wybierz, co wykonać kopię zapasową, kiedy kopie zapasowe będą działać i jak długo będą przechowywane na platformie Azure.
1. Początkowa kopia zapasowa jest uruchamiana zgodnie z ustawieniami kopii zapasowej.
1. Agent MARS używa usługi VSS do wykonywania migawki w czasie woluminów wybranych do tworzenia kopii zapasowych.
    - Agent MARS używa tylko operacji zapisu systemu Windows do przechwytywania migawki.
    - Ponieważ agent nie używa żadnych modułów zapisu aplikacji VSS, nie przechwytuje migawek spójnych z aplikacjami.
1. Po wykonaniu migawki za pomocą usługi VSS agent MARS tworzy wirtualny dysk twardy (VHD) w folderze pamięci podręcznej określonym podczas konfigurowania kopii zapasowej. Agent przechowuje również sumy kontrolne dla każdego bloku danych.
1. Przyrostowe kopie zapasowe są uruchamiane zgodnie z określonym harmonogramem, chyba że zostanie uruchomiony kopia zapasowa na żądanie.
1. W przyrostowych kopiach zapasowych, zmienione pliki są identyfikowane i tworzony jest nowy VHD. Dysk VHD jest skompresowany i zaszyfrowany, a następnie jest wysyłany do magazynu.
1. Po zakończeniu przyrostowej kopii zapasowej nowy dysk VHD jest scalany z dyskiem VHD utworzonym po replikacji początkowej. Ten scalony dysk VHD zapewnia najnowszy stan, który ma być używany do porównania dla bieżącej kopii zapasowej.

![Tworzenie kopii zapasowych lokalnych komputerów z systemem Windows Server za pomocą agenta MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: Zapasowy do programu DPM/MABS

1. Należy zainstalować agent ochrony programu DPM lub MABS na komputerach, które chcesz chronić. Następnie należy dodać maszyny do grupy ochrony programu DPM.
    - Aby chronić maszyny lokalne, serwer programu DPM lub MABS musi znajdować się lokalnie.
    - Aby chronić maszyny wirtualne platformy Azure, serwer MABS musi znajdować się na platformie Azure, działającej jako maszyna wirtualna platformy Azure.
    - Za pomocą programu DPM/MABS można chronić woluminy kopii zapasowych, udziały, pliki i foldery. Można również chronić stan systemu komputera (gołe metale) i można chronić określone aplikacje za pomocą ustawień kopii zapasowych obsługujących aplikację.
1. Podczas konfigurowania ochrony dla komputera lub aplikacji w programach DPM/MABS, należy wybrać, aby uzyskać kopii zapasowej na dysku lokalnym MABS/DPM dla magazynu krótkoterminowego i platformy Azure w celu ochrony online. Należy również określić, kiedy kopia zapasowa do lokalnego magazynu programu DPM/MABS powinna zostać uruchomiony i kiedy kopia zapasowa online na platformie Azure powinna zostać uruchomiony.
1. Kopia zapasowa dysku chronionego obciążenia jest archiwizowana na lokalnych dyskach MABS/DPM zgodnie z określonym harmonogramem.
1. Kopie zapasowe dysków programu DPM/MABS są archiwizowane w magazynie przez agenta MARS działającego na serwerze DPM/MABS.

![Tworzenie kopii zapasowych maszyn i obciążeń chronionych przez program DPM lub MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Magazyn maszyn wirtualnych platformy Azure

Maszyny wirtualne platformy Azure używają dysków do przechowywania systemu operacyjnego, aplikacji i danych. Każda maszyna wirtualna platformy Azure ma co najmniej dwa dyski: dysk dla systemu operacyjnego i dysk tymczasowy. Maszyny wirtualne platformy Azure mogą również mieć dyski danych dla danych aplikacji. Dyski są przechowywane jako dyski VHD.

- Dyski VHD są przechowywane jako obiekty blob stron na kontach magazynu standardowego lub premium na platformie Azure:
  - **Standardowe przechowywanie:** Niezawodne, tanie wsparcie dysku dla maszyn wirtualnych z uruchomionymi obciążeniami, które nie są wrażliwe na opóźnienia. Standardowa pamięć masowa może używać standardowych dysków PÓŁPRZEWODNIKOWYCH (SSD) lub standardowych dysków twardych (HDD).
  - **Pamięć masowa w jakości Premium:** Obsługa dysków o wysokiej wydajności. Używa dysków SSD premium.
- Istnieją różne warstwy wydajności dla dysków:
  - **Standardowy dysk twardy:** Wspierane przez dyski twarde i używane do ekonomicznej pamięci masowej.
  - **Standardowy dysk SSD:** Łączy w sobie elementy dysków SSD premium i standardowych dysków twardych. Oferuje bardziej spójną wydajność i niezawodność niż dysk twardy, ale nadal opłacalny.
  - **Dysk SSD premium:** Wspierane przez ssd i zapewnia wysoką wydajność i małe opóźnienia dla maszyn wirtualnych, które są uruchomione we/wy obciążeń intensywnie korzystających z we/wy.
- Dyskami można zarządzać dyskami lub być niezarządzane:
  - **Dyski niezarządzane:** Tradycyjne typy dysków używanych przez maszyny wirtualne. Dla tych dysków należy utworzyć własne konto magazynu i określić je podczas tworzenia dysku. Następnie należy dowiedzieć się, jak zmaksymalizować zasoby magazynu dla maszyn wirtualnych.
  - **Dyski zarządzane:** Platforma Azure tworzy konta magazynu i zarządza nimi. Należy określić rozmiar dysku i warstwę wydajności, a platforma Azure tworzy dyski zarządzane dla Ciebie. Podczas dodawania dysków i skalowania maszyn wirtualnych platforma Azure obsługuje konta magazynu.

Aby uzyskać więcej informacji na temat magazynu dysków i dostępnych typów dysków dla maszyn wirtualnych, zobacz następujące artykuły:

- [Dyski zarządzane platformy Azure dla maszyn wirtualnych z systemem Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Dyski zarządzane platformy Azure dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Dostępne typy dysków dla maszyn wirtualnych](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Twoj kopię zapasową i przywracanie maszyn wirtualnych platformy Azure z magazynu w stanie Premium

Możesz wykonać kopię zapasową maszyn wirtualnych platformy Azure przy użyciu magazynu w stanie Premium za pomocą usługi Azure Backup:

- Podczas procesu tworzenia kopii zapasowych maszyn wirtualnych z magazynu w wersji premium usługa Kopia zapasowa tworzy tymczasową lokalizację przejściową o nazwie *AzureBackup-* na koncie magazynu. Rozmiar lokalizacji przemieszczania jest równy rozmiarowi migawki punktu odzyskiwania.
- Upewnij się, że konto magazynu w wersji premium ma odpowiednie wolne miejsce, aby pomieścić tymczasową lokalizację przejściową. Aby uzyskać więcej informacji, zobacz [Cele skalowalności dla kont magazynu obiektów blob strony premium](../storage/blobs/scalability-targets-premium-page-blobs.md). Nie modyfikuj lokalizacji tymczasowej.
- Po zakończeniu zadania tworzenia kopii zapasowej lokalizacja przemieszczania jest usuwana.
- Cena magazynu używanego dla lokalizacji tymczasowej jest zgodna z [cennikiem magazynu w wersji premium.](../virtual-machines/windows/disks-types.md#billing)

Po przywróceniu maszyn wirtualnych platformy Azure przy użyciu magazynu w stanie Premium, można przywrócić je do magazynu w wersji premium lub standardowej. Zazwyczaj można przywrócić je do magazynu w wersji premium. Ale jeśli potrzebujesz tylko podzbiór plików z maszyny Wirtualnej, może być opłacalne, aby przywrócić je do magazynu standardowego.

### <a name="back-up-and-restore-managed-disks"></a>Twoichnie i przywracanie dysków zarządzanych

Można wyw.

- Możesz wykonać kopii zapasowej maszyn wirtualnych z dyskami zarządzanymi w taki sam sposób, jak inne maszyny wirtualne platformy Azure. Maszynę wirtualną można wykonać bezpośrednio z ustawień maszyny wirtualnej lub włączyć tworzenie kopii zapasowych maszyn wirtualnych w magazynie usług odzyskiwania.
- Kopie zapasowe maszyn wirtualnych możesz tworzyć na dyskach zarządzanych za pomocą kolekcji RestorePoint tworzonych na tych dyskach.
- Usługa Azure Backup obsługuje również tworzenie kopii zapasowych maszyn wirtualnych z dyskami zarządzanymi, które były szyfrowane przy użyciu szyfrowania dysków platformy Azure.

Podczas przywracania maszyn wirtualnych z dyskami zarządzanymi można przywrócić pełną maszynę wirtualną z dyskami zarządzanymi lub konto magazynu:

- Podczas procesu przywracania platforma Azure obsługuje dyski zarządzane. Jeśli używasz opcji konta magazynu, zarządzasz kontem magazynu, które jest tworzone podczas procesu przywracania.
- Jeśli przywrócisz zarządzaną maszynę wirtualną, która jest szyfrowana, upewnij się, że klucze i wpisy tajne maszyny Wirtualnej istnieją w magazynie kluczy przed rozpoczęciem procesu przywracania.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj macierz pomocy technicznej, aby [dowiedzieć się więcej o obsługiwanych funkcjach i ograniczeniach dotyczących scenariuszy tworzenia kopii zapasowych](backup-support-matrix.md).
- Skonfiguruj kopię zapasową dla jednego z następujących scenariuszy:
  - [19.00.000.](backup-azure-arm-vms-prepare.md)
  - [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
  - [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
  - [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
