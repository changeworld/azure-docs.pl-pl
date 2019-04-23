---
title: Usługa Azure macierz obsługi kopii zapasowych dla kopii zapasowej maszyny Wirtualnej platformy Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczenia, podczas wykonywania kopii zapasowej maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: raynew
ms.openlocfilehash: aacfe725310b3c8e4785e24b80728f0e60694814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496099"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Macierz obsługi kopii zapasowej maszyny Wirtualnej platformy Azure
Możesz użyć [usługi Azure Backup](backup-overview.md) do tworzenia kopii zapasowych maszyn lokalnych i obciążeń i Azure virtual machines (VMs). Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia, podczas wykonywania kopii zapasowych maszyn wirtualnych platformy Azure z usługą Azure Backup.

Inne macierze pomocy technicznej:

- [Macierz obsługi ogólne](backup-support-matrix.md) dla usługi Azure Backup
- [Macierz obsługi](backup-support-matrix-mabs-dpm.md) dla usługi Azure Backup server/System Center Data Protection Manager (DPM) należy utworzyć kopię zapasową
- [Macierz obsługi](backup-support-matrix-mars-agent.md) do utworzenia kopii zapasowej za pomocą agenta usługi Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Oto, jak tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.

**Scenariusz** | **Tworzenie kopii zapasowych** | **Agent** |**Przywracanie**
--- | --- | --- | ---
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure  | Utwórz kopię zapasową całej maszyny Wirtualnej.  | Agent nie jest potrzebna na maszynie Wirtualnej platformy Azure. Usługa Azure Backup instaluje i używa rozszerzenia [agenta maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) uruchomionym na maszynie Wirtualnej. | Przywróć w następujący sposób:<br/><br/> - **Tworzenie podstawowej maszyny Wirtualnej**. Jest to przydatne, jeśli żadna specjalna konfiguracja, takich jak wiele adresów IP na maszynie Wirtualnej.<br/><br/> - **Przywracanie dysku maszyny Wirtualnej**. Przywróć dysk. Następnie dołącz je do istniejącej maszyny Wirtualnej lub Utwórz nową maszynę Wirtualną z dysku przy użyciu programu PowerShell.<br/><br/> - **Wymiana dysku maszyny Wirtualnej**. Jeśli maszyna wirtualna istnieje, a następnie używa dysków zarządzanych (bez szyfrowania), możesz przywrócić dysk i użyj go, aby zamienić istniejący dysk na maszynie Wirtualnej.<br/><br/> - **Przywracanie plików/folderów określonych**. Możesz przywrócić pliki i foldery z maszyny Wirtualnej zamiast z całej maszyny Wirtualnej.
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure (tylko Windows)  | Utwórz kopię zapasową określonych plików/folderów/woluminów. | Zainstaluj [agenta usług odzyskiwania Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Można uruchomić agenta usług MARS obok zapasowy numer wewnętrzny dla agenta maszyny Wirtualnej platformy Azure do tworzenia kopii zapasowych maszyn wirtualnych na poziomie plików/folderów. | Przywracanie plików/folderów określonych.
Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure do tworzenia kopii zapasowej serwera  | Tworzenie kopii zapasowej plików/folderów/woluminów; Stan/bez systemu operacyjnego pliki systemowe; dane aplikacji do programu System Center DPM lub do firmy Microsoft Azure Backup Server (MABS).<br/><br/> Program DPM/serwera usługi Mab następnie wykonuje kopię zapasową magazynu kopii zapasowych. | Zainstaluj agenta ochrony programu DPM/serwera usługi Mab na maszynie Wirtualnej. Agent usług MARS jest zainstalowany na program DPM/serwera usługi MAB.| Przywracanie plików/folderów/woluminów; Stan/bez systemu operacyjnego pliki systemowe; dane aplikacji.

Dowiedz się więcej o usłudze backup [przy użyciu kopii zapasowej serwera](backup-architecture.md#architecture-back-up-to-dpmmabs) i o [obsługi wymagań związanych z](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Obsługiwane operacje tworzenia kopii zapasowej

**Akcja** | **Pomoc techniczna**
--- | ---
Włącz wykonywanie kopii zapasowej po utworzeniu maszyny Wirtualnej platformy Azure z systemem Windows | Obsługiwane w przypadku:  System Windows Server 2019 r (rdzenie Datacenter/Datacenter), Windows Server 2016 (Datacenter/Datacenter-rdzeniowe); Windows Server 2012 R2 Datacenter; Windows Server 2008 R2 (RTM i z dodatkiem SP1)
Włącz wykonywanie kopii zapasowej po utworzeniu maszyny Wirtualnej z systemem Linux | Obsługiwane w przypadku:<br/><br/> — Ubuntu Server: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8 6,9, 7.2, 7.3, WERSJI 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> -Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Tworzenie kopii zapasowej maszyny Wirtualnej, która jest shutdown/offline maszyny Wirtualnej | Obsługiwane.<br/><br/> Migawka jest spójna w razie awarii tylko, nie spójny na poziomie aplikacji.
Wykonywanie kopii zapasowych dysków, po migracji do usługi managed disks | Obsługiwane.<br/><br/> Kopia zapasowa, będą nadal działać. Nie jest wymagana żadna akcja.
Tworzenie kopii zapasowych dysków zarządzanych, po włączeniu blokady grupy zasobów | Nieobsługiwane.<br/><br/> Usługa Azure Backup nie można usunąć starsze punkty zasobów i kopie zapasowe będą kończyć się niepowodzeniem, jeśli osiągnięto maksymalny limit punktów przywracania.
Modyfikowanie zasad tworzenia kopii zapasowej dla maszyny Wirtualnej | Obsługiwane.<br/><br/> Maszyna wirtualna zostanie skopiowana za pomocą ustawień harmonogram i okres przechowywania w nowych zasad. Jeśli ustawienia przechowywania są przedłużony, istniejące punkty odzyskiwania są oznaczone i przechowywane. One zostały obniżone, istniejące punkty odzyskiwania będą usuwane w ramach następnego zadania oczyszczania i usunięte po pewnym czasie.
Anuluj zadanie tworzenia kopii zapasowej | Obsługiwane, podczas procesu migawki.<br/><br/> Nieobsługiwane, gdy migawki są przesyłane do magazynu.
Tworzenie kopii zapasowej maszyny Wirtualnej do innego regionu lub subskrypcji |  Nieobsługiwane.
Kopie zapasowe dziennie (za pośrednictwem rozszerzenia maszyny Wirtualnej platformy Azure) | Jeden zaplanowane tworzenie kopii zapasowej na dzień.<br/><br/> Można wprowadzić do czterech kopii zapasowych na żądanie na dzień.
Kopie zapasowe dziennie (za pośrednictwem agenta usług MARS) | Trzy zaplanowanych kopii zapasowych dziennie.
Kopie zapasowe dziennie (przy użyciu programu DPM/MABS) | Dwa zaplanowane kopie zapasowe dziennie.
Kopia zapasowa co miesiąc/rok   | Nie jest obsługiwana podczas wykonywania kopii zapasowej za pomocą rozszerzenia maszyny Wirtualnej platformy Azure. Obsługiwane jest tylko dziennego i tygodniowego.<br/><br/> Można ustawić zasady przechowywania kopii zapasowych codziennie/co tydzień na okres przechowywania co miesiąc/rok.
Korekta automatyczna zegara | Nieobsługiwane.<br/><br/> Usługa Azure Backup nie automatycznie Dostosuj zmiany czasu letniego, podczas tworzenia kopii zapasowej maszyny Wirtualnej.<br/><br/>  Zmodyfikuj zasady ręcznego zgodnie z potrzebami.
[Funkcje zabezpieczeń dla hybrydowych kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Wyłączanie funkcji zabezpieczeń nie jest obsługiwane.

## <a name="operating-system-support-windows"></a>Obsługa systemu operacyjnego (Windows)

W poniższej tabeli przedstawiono obsługiwane systemy operacyjne, podczas wykonywania kopii zapasowych maszyn wirtualnych Windows.

**Scenariusz** | **Obsługa systemu operacyjnego**
--- | ---
Tworzenie kopii zapasowej za pomocą rozszerzenia agenta maszyny Wirtualnej platformy Azure | Klient Windows: Nieobsługiwane<br/><br/> System Windows Server 2019 r (rdzenie Datacenter/Datacenter), Windows Server 2016 (Datacenter/Datacenter-rdzeniowe); Windows Server 2012 R2 Datacenter; Windows Server 2008 R2 (RTM i z dodatkiem SP1)
Tworzenie kopii za pomocą agenta usług MARS | [Obsługiwane](backup-support-matrix-mars-agent.md#support-for-direct-backups) systemów operacyjnych.
Tworzenie kopii zapasowej z programu DPM/serwera usługi Mab | Obsługiwane systemy operacyjne dla kopii zapasowych z użyciem [serwera usługi Mab](backup-mabs-protection-matrix.md) i [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Obsługa kopia zapasowa systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Akcja** | **Pomoc techniczna**
--- | ---
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux z agentem maszyny Wirtualnej platformy Azure z systemem Linux | Spójne tworzenie kopii zapasowej plików.<br/><br/> Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Podczas przywracania można utworzyć nową maszynę Wirtualną, przywracanie dysku i użyć go do tworzenia maszyny Wirtualnej, lub przywrócić dysk i przy jego użyciu wymiana dysku, istniejącej maszyny wirtualnej. Możesz też przywrócić pojedyncze pliki i foldery.
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux za pomocą agenta usług MARS | Nieobsługiwane.<br/><br/> Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows.
Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux przy użyciu programu DPM/serwera usługi Mab | Nieobsługiwane.

## <a name="operating-system-support-linux"></a>Obsługa systemu operacyjnego (Linux)

Dla kopii zapasowych systemu Linux maszyny Wirtualnej platformy Azure, Azure Backup obsługuje systemu Linux [dystrybucje zalecane dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Pamiętaj o następujących kwestiach:

- Usługa Azure Backup nie obsługuje systemu operacyjnego Linux Core.
- Usługa Azure Backup nie obsługuje 32-bitowych systemach operacyjnych.
- Inne dystrybucje systemu Linux bring your own może działać tak długo, jak [agenta maszyny Wirtualnej platformy Azure dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) jest dostępny na maszynie Wirtualnej i tak długo, jak obsługuje język Python.
- Usługa Azure Backup nie obsługuje Maszynę wirtualną systemu Linux skonfigurowany serwer proxy, jeśli nie ma języka Python w wersji 2.7 zainstalowane.


## <a name="backup-frequency-and-retention"></a>Częstotliwość wykonywania kopii zapasowych i przechowywanie

**Ustawienie** | **Limity**
--- | ---
Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (machine/obciążenie) | 9999.
Maksymalny czas wygaśnięcia punktu odzyskiwania | Brak limitu.
Maksymalna częstotliwość wykonywania kopii zapasowych w magazynie (rozszerzenie maszyny Wirtualnej platformy Azure) | Raz dziennie.
Maksymalna częstotliwość wykonywania kopii zapasowych w magazynie (agenta usług MARS) | Trzy kopie zapasowe dziennie.
Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS | Co 15 minut dla programu SQL Server.<br/><br/> Co godzinę dla innych obciążeń.
Przechowywanie punktów odzyskiwania | Codziennie, cotygodniowych, comiesięcznych i corocznych.
Maksymalny okres przechowywania | Zależnie od częstotliwości wykonywania kopii zapasowych.
Punkty odzyskiwania na dysku programu DPM/usługi MABS | 64 dla serwerów plików i 448 dla serwerów aplikacji.<br/><br/> Punkty odzyskiwania na taśmie nie mają ograniczeń w przypadku lokalnego programu DPM.

## <a name="supported-restore-methods"></a>Obsługiwane metody

**Przywracanie — metoda** | **Szczegóły**
--- | ---
Utwórz nową maszynę Wirtualną | Możesz utworzyć Maszynę wirtualną podczas procesu przywracania. <br/><br/> Ta opcja pobiera podstawowej maszyny Wirtualnej, skonfigurowaniu i uruchomieniu. Można określić maszyny Wirtualnej nazwę, grupę zasobów, sieci wirtualnej, podsieci i magazynu.  
Przywracanie dysku | Można przywrócić dysk i użyć go do utworzenia maszyny Wirtualnej.<br/><br/> Po wybraniu tej opcji, kopia zapasowa Azure kopiuje dane z magazynu do wybranego konta magazynu. Zadania przywracania generuje szablon. Można pobrać tego szablonu, użyj go, aby określić niestandardowe ustawienia maszyny Wirtualnej i tworzenie maszyny Wirtualnej.<br/><br/> Ta opcja umożliwia określenie więcej ustawień, poprzedniej opcji, aby utworzyć Maszynę wirtualną.<br/><br/>
Zastąp istniejący dysk | Można przywrócić dysk, a następnie użyć przywróconego dysku, aby zastąpić dysk, który jest aktualnie na maszynie Wirtualnej.
Przywracanie plików | Można odzyskiwać pliki z wybranego punktu odzyskiwania. Możesz pobrać skrypt, aby zainstalować dysk maszyny Wirtualnej z punktu odzyskiwania. Następnie możesz przeglądać za pomocą woluminów dysku, aby znaleźć pliki/foldery, które chcesz odzyskać, a następnie odinstaluj dysk, gdy wszystko będzie gotowe.

## <a name="support-for-file-level-restore"></a>Obsługa przywracania na poziomie plików

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie plików w różnych systemach operacyjnych | Możesz przywrócić pliki na dowolnym komputerze, który ma ten sam (lub zgodny) systemu operacyjnego jako kopii zapasowej maszyny Wirtualnej. zobacz [tabeli zgodny system operacyjny](backup-azure-restore-files-from-vm.md#system-requirements).
Przywracanie plików na klasycznych maszyn wirtualnych | Nieobsługiwane.
Przywracanie plików z zaszyfrowanych maszyn wirtualnych | Nieobsługiwane.
Przywracanie plików z konta magazynu z ograniczeniami w sieci | Nieobsługiwane.
Przywracanie plików na maszynach wirtualnych przy użyciu funkcji miejsca do magazynowania systemu Windows | Przywracanie nie są obsługiwane w tej samej maszyny Wirtualnej.<br/><br/> Zamiast tego należy przywrócić pliki na zgodnej maszyny Wirtualnej.
Przywracanie plików na maszynie Wirtualnej systemu Linux za pomocą macierzy LVM/raid | Przywracanie nie są obsługiwane w tej samej maszyny Wirtualnej.<br/><br/> Przywracanie zgodnej maszyny Wirtualnej.
Przywracanie plików z ustawieniami sieci specjalne | Przywracanie nie są obsługiwane w tej samej maszyny Wirtualnej. <br/><br/> Przywracanie zgodnej maszyny Wirtualnej.

## <a name="support-for-vm-management"></a>Obsługa zarządzania maszyny Wirtualnej

W poniższej tabeli przedstawiono obsługę kopii zapasowych podczas zadania zarządzania maszyny Wirtualnej, takie jak dodawanie lub zastępowanie dysków maszyny Wirtualnej.

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie w subskrypcji lub regionu/strefy. | Nieobsługiwane.
Przywracanie do istniejącej maszyny Wirtualnej | Opcja Zastąp dysku.
Przywracanie dysku z kontem magazynu, włączone dla szyfrowania usługi Azure Storage (SSE) | Nieobsługiwane.<br/><br/> Przywróć do konta które nie zostały włączone SSE.
Przywracanie do kont magazynu mieszanych | Nieobsługiwane.<br/><br/> Oparte na typ konta magazynu, wszystkie przywróconych dysków będzie premium lub standardowa i nie mieszanych.
Przywracanie do konta magazynu przy użyciu magazynu strefowo nadmiarowego (ZRS) | Obsługiwane (dla maszyny Wirtualnej, który są kopie zapasowe po 2019 sty i gdzie [strefy dostępności](https://azure.microsoft.com/global-infrastructure/availability-zones/) są dostępne)
Przywracanie maszyny Wirtualnej bezpośrednio do zestawu dostępności | Za dyski zarządzane można przywrócić dysk i użyj opcji zestaw dostępności w szablonie.<br/><br/> Nie jest obsługiwane dla dysków niezarządzanych. W przypadku dysków niezarządzanych przywrócić dysk, a następnie utwórz Maszynę wirtualną w zestawie dostępności.
Przywracanie kopii zapasowej niezarządzanych maszyn wirtualnych, po uaktualnieniu do zarządzania maszyny Wirtualnej| Obsługiwane.<br/><br/> Przywróć dyski i następnie utworzyć zarządzanej maszyny Wirtualnej.
Przywracanie maszyny Wirtualnej, aby punkt przywracania, zanim maszyna wirtualna została zmigrowana do usługi managed disks | Obsługiwane.<br/><br/> Przywracanie do dysków niezarządzanych (ustawienie domyślne), Konwertuj przywróconych dysków na dyskach zarządzanych i Utwórz Maszynę wirtualną z dyskami zarządzanymi.
Przywracanie maszyny Wirtualnej, który jest usunięty. | Obsługiwane.<br/><br/> Możesz przywrócić maszynę Wirtualną z punktu odzyskiwania.
Przywracanie kontrolera domeny (DC) maszyny Wirtualnej, która jest częścią konfiguracji kontrolera domeny multi, za pośrednictwem portalu | Obsługiwane w przypadku przywracania dysku i tworzenie maszyny Wirtualnej przy użyciu programu PowerShell.
Przywróć maszynę Wirtualną w innej sieci wirtualnej |   Obsługiwane.<br/><br/> Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionu.

## <a name="vm-compute-support"></a>Obsługa obliczeń maszyny Wirtualnej

**Obliczanie** | **Pomoc techniczna**
--- | ---
Rozmiar maszyny wirtualnej |   Dowolnego rozmiaru maszyny Wirtualnej platformy Azure z co najmniej 2 rdzeni procesora CPU i 1 GB pamięci RAM.<br/><br/> [Dowiedz się więcej.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Tworzenie kopii zapasowych maszyn wirtualnych w [zestawów dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Obsługiwane.<br/><br/> Nie można przywrócić Maszynę wirtualną w zestawie dostępności za pomocą opcji, aby szybko utworzyć Maszynę wirtualną. Zamiast tego podczas przywracania maszyny Wirtualnej przywracania dysku i użyj go, aby wdrożyć Maszynę wirtualną, lub przywrócić dysk i użyć go do zastąpienia istniejącego dysku.
Tworzenie kopii zapasowych maszyn wirtualnych w [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Nieobsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych, które są wdrażane przy użyciu [korzyść użycia hybrydowego (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Obsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych, które są wdrożone w [zestawu skalowania](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |  Nieobsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych, które są wdrażane z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Opublikowanych przez firmę Microsoft i innych firm) |  Obsługiwane.<br/><br/> Maszyna wirtualna musi działać obsługiwany system operacyjny.<br/><br/> W przypadku odzyskiwania plików na maszynie Wirtualnej, można przywrócić tylko do zgodnych systemów operacyjnych (nie poprzedni system operacyjny).
Tworzenie kopii zapasowych maszyn wirtualnych, które są wdrażane na podstawie niestandardowego obrazu (innej firmy) |   Obsługiwane.<br/><br/> Maszyna wirtualna musi działać obsługiwany system operacyjny.<br/><br/> W przypadku odzyskiwania plików na maszynie Wirtualnej, można przywrócić tylko do zgodnych systemów operacyjnych (nie poprzedni system operacyjny).
Tworzenie kopii zapasowych maszyn wirtualnych, które są migrowane na platformie Azure  | Obsługiwane.<br/><br/> Aby utworzyć kopię zapasową maszyny Wirtualnej, musi być zainstalowany agent maszyny Wirtualnej, na zmigrowanej maszynie.
Tworzenie kopii zapasowych wielu maszyn wirtualnych | Usługa Azure Backup nie zapewnia spójności danych i aplikacji na wielu maszynach wirtualnych.


## <a name="vm-storage-support"></a>Obsługa magazynu maszyny Wirtualnej

**Składnik** | **Pomoc techniczna**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Utwórz kopię zapasową maszyny Wirtualnej z dyskami danych 16 lub mniej. <br/><br/> Obsługa dysków o rozmiarach do 4 TB.
Rozmiar dysku danych | Każdy dysk może mieć rozmiar do 4095 GB.<br/><br/> Jeśli Twoich magazynów jest najnowszą wersją programu Kopia zapasowa Azure (nazywanych, natychmiastowe przywracanie), dysków o rozmiarach do 4 TB są obsługiwane. [Dowiedz się więcej](backup-instant-restore-capability.md).  
Typ magazynu | Standardowa HDD i SSD w warstwie standardowa, premium SSD. <br/><br/> SSD w warstwie standardowa jest obsługiwany, jeśli Twoich magazynów zostaną uaktualnione do najnowszej wersji kopii zapasowej maszyny Wirtualnej platformy Azure (nazywane natychmiastowe przywracanie). [Dowiedz się więcej](backup-instant-restore-capability.md).
Dyski zarządzane | Obsługiwane.
Zaszyfrowanych dysków | Obsługiwane.<br/><br/> Maszyny wirtualne platformy Azure, włączyć za pomocą usługi Azure Disk Encryption utworzeniem kopii zapasowej (z lub bez aplikację usługi Azure AD).<br/><br/> Nie można odzyskać zaszyfrowanych maszyn wirtualnych na poziomie plików/folderów. Odzyskaj całą maszynę Wirtualną.<br/><br/> Użytkownik może włączyć szyfrowanie na maszynach wirtualnych, które są już chronione przez usługę Azure Backup.
Dysków z włączonym akceleratorem zapisu | Nieobsługiwane.<br/><br/> Jeśli używasz najnowszej wersji kopii zapasowej maszyny Wirtualnej platformy Azure (nazywane [natychmiastowe Przywracanie](backup-instant-restore-capability.md)), można wykluczyć dyski z akceleratorem zapisu włączone z kopii zapasowej.
Wykonywanie kopii zapasowych deduplikowanych dysków | Nieobsługiwane.
Dodaj dysk do chronionej maszyny Wirtualnej | Obsługiwane.
Zmiana rozmiaru dysku na chronionej maszynie Wirtualnej | Obsługiwane.
Magazyn udostępniony| Nie zaleca się tworzenie kopii zapasowych maszyn wirtualnych przy użyciu udostępnionego woluminu klastra (CSV) lub serwera plików skalowalnego w poziomie. Autorzy CSV są mogą kończyć się niepowodzeniem podczas wykonywania kopii zapasowej. Podczas przywracania dyski zawierające woluminów CSV może nie wróć w górę.

> [!NOTE]
> Usługa Azure Backup nie obsługuje dysków rozłożonych. Zmiana rozmiaru dysku nie jest zalecane przez usługę Azure Backup.


## <a name="vm-network-support"></a>Obsługa sieci maszyny Wirtualnej

**Składnik** | **Pomoc techniczna**
--- | ---
Liczba interfejsów sieciowych (NIC) | Maksymalnie maksymalna liczba kart sieciowych obsługiwanych dla określonego rozmiaru maszyny Wirtualnej platformy Azure.<br/><br/> Karty sieciowe są tworzone podczas tworzenia maszyny Wirtualnej podczas procesu przywracania.<br/><br/> Liczba kart sieciowych do przywróconej maszyny wirtualnej odzwierciedla liczbę kart sieciowych na maszynie Wirtualnej, po włączeniu ochrony. Usuwanie karty sieciowe, po włączeniu ochrony nie ma wpływu na liczbę.
Zewnętrzne/wewnętrznego modułu równoważenia obciążenia |   Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) dotyczące przywracania maszyn wirtualnych przy użyciu ustawień sieciowych.
Wielu zastrzeżonych adresów IP |    Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) dotyczące przywracania maszyn wirtualnych przy użyciu ustawień sieciowych.
Maszyny wirtualne z wieloma kartami sieciowymi  | Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) dotyczące przywracania maszyn wirtualnych przy użyciu ustawień sieciowych.
Maszyny wirtualne z publicznych adresów IP    | Obsługiwane.<br/><br/> Kojarzenie istniejącego publicznego adresu IP z karty Sieciowej, lub utworzyć adres i skojarzyć ją z kartą Sieciową, po zakończeniu przywracania.
Sieciowa grupa zabezpieczeń (NSG) w kart Sieciowych/podsieci. |   Obsługiwane.
Zastrzeżony adres IP (statyczne) | Nieobsługiwane.<br/><br/> Nie można utworzyć kopii zapasowej maszyny Wirtualnej za pomocą zastrzeżonego adresu IP i nie ma zdefiniowanego punktu końcowego.
Dynamiczny adres IP |    Obsługiwane.<br/><br/> Jeśli karta sieciowa na "source" maszyna wirtualna używa dynamicznego adresowania IP, domyślnie kartę Sieciową do przywróconej maszyny wirtualnej użyje on zbyt.
Azure Traffic Manager   | Obsługiwane.<br/><br/>Jeśli kopia zapasowa maszyny Wirtualnej znajduje się w usłudze Traffic Manager, należy ręcznie dodać przywróconej maszyny Wirtualnej do tego samego wystąpienia usługi Traffic Manager.
System DNS platformy Azure | Obsługiwane.
Niestandardowe DNS |    Obsługiwane.
Łączności wychodzącej za pośrednictwem serwera proxy HTTP | Obsługiwane.<br/><br/> Uwierzytelnionego serwera proxy nie jest obsługiwane.
Punkty końcowe usługi dla sieci wirtualnej   | Obsługiwane.<br/><br/> Zapory i ustawienia konta magazynu w sieci wirtualnej powinna zezwalać na dostęp ze wszystkich sieci.



## <a name="vm-security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania maszyny Wirtualnej

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych magazynowanych:

Ruch sieciowy do platformy Azure:

- Kopii zapasowej ruch z serwerów w magazynie usługi Recovery Services jest szyfrowany przy użyciu algorytmu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- W magazynie usługi Recovery Services dane kopii zapasowych są przechowywane w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

  > [!WARNING]
  > Po skonfigurowaniu magazynu tylko Ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

Bezpieczeństwo danych:

- Podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure, musisz skonfigurować szyfrowanie *w ramach* maszyny wirtualnej.
- Usługa Azure Backup obsługuje szyfrowania dysków Azure, która używa funkcji BitLocker na maszynach wirtualnych Windows i nam **dm-crypt** maszyn wirtualnych systemu Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md), które chroni dane magazynowane.


**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | ![Yes][green] | ![Yes][green]
Maszyny wirtualne platformy Azure | ![Yes][green] | ![Yes][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Yes][green] | ![Yes][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Yes][green] | ![Yes][green]



## <a name="vm-compression-support"></a>Obsługa kompresji maszyny Wirtualnej

Backup obsługuje kompresję kopii zapasowej ruchu, zgodnie z opisem w poniższej tabeli. Pamiętaj o następujących kwestiach:

- W przypadku maszyn wirtualnych platformy Azure z rozszerzenia maszyny Wirtualnej odczytuje dane bezpośrednio z konta usługi Azure storage za pośrednictwem sieci magazynowania. Nie jest konieczne Kompresja tego ruchu.
- Jeśli używasz programu DPM lub MABS, możesz oszczędzić przepustowość, kompresji danych, przed jej kopii zapasowej, aby program DPM/serwera usługi MAB.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresuj do magazynu (HTTPS)**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | Nie dotyczy | ![Yes][green]
Maszyny wirtualne platformy Azure | Nie dotyczy | Nie dotyczy
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Yes][green] | ![Yes][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Yes][green] | ![Yes][green]


## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md).
- [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
- [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
- [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
