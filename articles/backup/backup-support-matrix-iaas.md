---
title: Tabela obsługi dla maszyn wirtualnych platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 8475e90e247aa2a0c081d25486c4ada0854a9e89
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705398"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Tabela obsługi dla maszyn wirtualnych platformy Azure

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe maszyn i obciążeń lokalnych oraz maszyn wirtualnych platformy Azure. Ten artykuł zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu Azure Backup.

Inne macierze pomocy technicznej:

- [Macierz obsługi ogólnej](backup-support-matrix.md) dla Azure Backup
- [Macierz obsługi](backup-support-matrix-mabs-dpm.md) dla Azure Backup serwera/programu System Center Data Protection Manager (DPM)
- [Macierz obsługi](backup-support-matrix-mars-agent.md) kopii zapasowej za pomocą agenta Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Oto jak można tworzyć kopie zapasowe maszyn wirtualnych platformy Azure i przywracać je za pomocą usługi Azure Backup.

**Scenariusz** | **Tworzenie kopii zapasowych** | **Agent** |**Przywracanie**
--- | --- | --- | ---
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure  | Wykonaj kopię zapasową całej maszyny wirtualnej.  | Na maszynie wirtualnej platformy Azure nie jest wymagany żaden Agent. Azure Backup instaluje i używa rozszerzenia [agenta maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) uruchomionego na maszynie wirtualnej. | Przywróć w następujący sposób:<br/><br/> - **utworzyć podstawową maszynę wirtualną**. Jest to przydatne, jeśli maszyna wirtualna nie ma specjalnej konfiguracji, takiej jak wiele adresów IP.<br/><br/> - **przywrócić dysku maszyny wirtualnej**. Przywróć dysk. Następnie Dołącz ją do istniejącej maszyny wirtualnej lub Utwórz nową maszynę wirtualną z dysku przy użyciu programu PowerShell.<br/><br/> - **zastąpić dysk maszyny wirtualnej**. Jeśli maszyna wirtualna istnieje i korzysta z dysków zarządzanych (nieszyfrowanych), można przywrócić dysk i użyć go w celu zastąpienia istniejącego dysku na maszynie wirtualnej.<br/><br/> - **przywrócić określonych plików/folderów**. Pliki/foldery można przywrócić z maszyny wirtualnej, a nie z całej maszyny wirtualnej.
Bezpośrednie tworzenie kopii zapasowych maszyn wirtualnych platformy Azure (tylko system Windows)  | Utwórz kopię zapasową określonych plików/folderów/woluminów. | Zainstaluj [agenta Recovery Services platformy Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Agenta MARS można uruchomić wraz z rozszerzeniem kopii zapasowej agenta maszyny wirtualnej platformy Azure, aby utworzyć kopię zapasową maszyny wirtualnej na poziomie pliku/folderu. | Przywracanie określonych folderów/plików.
Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure na serwerze kopii zapasowej  | Tworzenie kopii zapasowej plików/folderów/woluminów; pliki stanu systemu/systemu operacyjnego; dane aplikacji do programu System Center DPM lub do Microsoft Azure Backup Server (serwera usługi MAB).<br/><br/> Program DPM/serwera usługi MAB następnie tworzy kopię zapasową magazynu z usługą Backup. | Zainstaluj agenta ochrony programu DPM/serwera usługi MAB na maszynie wirtualnej. Agent MARS jest instalowany na serwerze DPM/serwera usługi MAB.| Przywracanie plików/folderów/woluminów; pliki stanu systemu/systemu operacyjnego; dane aplikacji.

Dowiedz się więcej o usłudze Backup [przy użyciu serwera zapasowego](backup-architecture.md#architecture-back-up-to-dpmmabs) i o [wymaganiach dotyczących pomocy technicznej](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **Azure Backup teraz obsługuje selektywne tworzenie kopii zapasowych i przywracanie dysków przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.**
>
>Obecnie Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (systemu operacyjnego i danych) w maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowej maszyny wirtualnej. Funkcja wykluczania dysku umożliwia utworzenie kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb tworzenia kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dotyczące dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzestawu dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i magazynu.
>
>**Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas na AskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Obsługiwane akcje tworzenia kopii zapasowych

**Akcja** | **Pomoc techniczna**
--- | ---
Włącz tworzenie kopii zapasowej podczas tworzenia maszyny wirtualnej z systemem Windows Azure | Obsługiwane dla: <br/><br/> -Windows Server 2019 (Datacenter/Datacenter/standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter/standard) <br/><br/> — Windows Server 2012 R2 (Datacenter/standard) <br/><br/> -Windows Server 2008 R2 (wersja RTM i SP1 standard)
Włączanie tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej z systemem Linux | Obsługiwane dla:<br/><br/> -Ubuntu Server: 18,04, 17,10, 17,04, 16,04 (LTS), 14,04 (LTS)<br/><br/> -Red Hat: RHEL 6,7, 6,8, 6,9, 7,2, 7,3, 7,4<br/><br/> -SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> -Debian: 8, 9<br/><br/> -CentOS: 6,9, 7,3<br/><br/> -Oracle Linux: 6,7, 6,8, 6,9, 7,2, 7,3
Tworzenie kopii zapasowej maszyny wirtualnej, która jest zamykana/maszyną wirtualną w trybie offline | Obsługiwane.<br/><br/> Migawka jest tylko w przypadku awarii, a nie spójna z aplikacją.
Tworzenie kopii zapasowych dysków po migracji na dyski zarządzane | Obsługiwane.<br/><br/> Kopia zapasowa będzie nadal działała. Nie jest wymagana żadna akcja.
Tworzenie kopii zapasowych dysków zarządzanych po włączeniu blokady grupy zasobów | Bez pomocy technicznej.<br/><br/> Azure Backup nie może usunąć starszych punktów przywracania, a kopie zapasowe rozpoczną się niepowodzeniem po osiągnięciu maksymalnego limitu punktów przywracania.
Modyfikowanie zasad tworzenia kopii zapasowej dla maszyny wirtualnej | Obsługiwane.<br/><br/> Kopia zapasowa maszyny wirtualnej zostanie utworzona przy użyciu ustawień harmonogram i przechowywanie w obszarze nowe zasady. Jeśli ustawienia przechowywania są rozszerzone, istniejące punkty odzyskiwania zostaną oznaczone i zachowane. Jeśli są one zmniejszone, istniejące punkty odzyskiwania zostaną oczyszczone w następnym zadaniu oczyszczania i ostatecznie usunięte.
Anulowanie zadania tworzenia kopii zapasowej| Obsługiwane podczas procesu tworzenia migawek.<br/><br/> Nieobsługiwane w przypadku przesyłania migawki do magazynu.
Tworzenie kopii zapasowej maszyny wirtualnej w innym regionie lub subskrypcji |Bez pomocy technicznej.
Kopie zapasowe dziennie (za pomocą rozszerzenia maszyny wirtualnej platformy Azure) | Jedna zaplanowana kopia zapasowa dziennie.<br/><br/>Usługa Azure Backup obsługuje do dziewięciu kopii zapasowych na żądanie dziennie, ale firma Microsoft nie zaleca więcej niż czterech codziennych kopii zapasowych na żądanie w celu zapewnienia najlepszej wydajności.
Kopie zapasowe dziennie (za pośrednictwem agenta MARS) | Trzy zaplanowane kopie zapasowe dziennie.
Kopie zapasowe dziennie (za pośrednictwem programu DPM/serwera usługi MAB) | Dwa zaplanowane kopie zapasowe dziennie.
Kopia zapasowa co miesiąc/co rok| Nieobsługiwane podczas tworzenia kopii zapasowej przy użyciu rozszerzenia maszyny wirtualnej platformy Azure. Obsługiwane są tylko codziennie i co tydzień.<br/><br/> Można skonfigurować zasady, aby zachować codzienne/cotygodniowe kopie zapasowe w okresie przechowywania miesięcznego/rocznego.
Automatyczne dostosowanie zegara | Bez pomocy technicznej.<br/><br/> Podczas tworzenia kopii zapasowej maszyny wirtualnej Azure Backup nie jest automatycznie dostosowywana do zmian czasu letniego.<br/><br/>  Zmodyfikuj zasady ręcznie w razie konieczności.
[Funkcje zabezpieczeń hybrydowej kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Wyłączenie funkcji zabezpieczeń nie jest obsługiwane.
Tworzenie kopii zapasowej maszyny wirtualnej, której czas maszyny został zmieniony | Bez pomocy technicznej.<br/><br/> Jeśli czas komputera zostanie zmieniony na przyszłą datę i godzinę po włączeniu kopii zapasowej dla tej maszyny wirtualnej; Jednak nawet jeśli zmiana czasu zostanie wycofana, powodzenie tworzenia kopii zapasowej nie jest gwarantowane.  

## <a name="operating-system-support-windows"></a>Obsługa systemu operacyjnego (Windows)

Poniższa tabela zawiera podsumowanie obsługiwanych systemów operacyjnych podczas tworzenia kopii zapasowych maszyn wirtualnych z systemem Windows Azure.

**Scenariusz** | **Obsługa systemu operacyjnego**
--- | ---
Tworzenie kopii zapasowej przy użyciu rozszerzenia agenta maszyny wirtualnej platformy Azure | -Klient z systemem Windows 10 (tylko 64-bitowy) <br/><br/>-Windows Server 2019 (Datacenter/Datacenter/standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter/standard) <br/><br/> — Windows Server 2012 R2 (Datacenter/standard) <br/><br/> -Windows Server 2008 R2 (wersja RTM i SP1 standard)
Tworzenie kopii zapasowej przy użyciu agenta MARS | [Obsługiwane](backup-support-matrix-mars-agent.md#support-for-direct-backups) systemy operacyjne.
Tworzenie kopii zapasowej za pomocą programu DPM/serwera usługi MAB | Obsługiwane systemy operacyjne na potrzeby tworzenia kopii zapasowych za pomocą [serwera usługi MAB](backup-mabs-protection-matrix.md) i [programu DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.

## <a name="support-for-linux-backup"></a>Obsługa kopii zapasowych systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Akcja** | **Pomoc techniczna**
--- | ---
Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux Azure przy użyciu agenta maszyny wirtualnej systemu Linux | Kopia zapasowa spójna z plikiem.<br/><br/> Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Podczas przywracania można utworzyć nową maszynę wirtualną, przywrócić dysk i użyć jej do utworzenia maszyny wirtualnej lub przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej. Można również przywrócić poszczególne pliki i foldery.
Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux przy użyciu agenta MARS | Bez pomocy technicznej.<br/><br/> Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows.
Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux platformy Azure przy użyciu programu DPM/serwera usługi MAB | Bez pomocy technicznej.

## <a name="operating-system-support-linux"></a>Obsługa systemu operacyjnego (Linux)

W przypadku kopii zapasowych w systemie Linux dla maszyny wirtualnej platformy Azure Azure Backup obsługuje listę [dystrybucji systemu Linux potwierdzonych przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Pamiętaj o następujących kwestiach:

- Azure Backup nie obsługuje podstawowego systemu operacyjnego Linux.
- Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.
- Inne dystrybucje systemu Linux mogą współdziałać, o ile [Agent maszyny wirtualnej platformy Azure dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) jest dostępny na maszynie wirtualnej, o ile Język Python jest obsługiwany.
- Azure Backup nie obsługuje maszyny wirtualnej z systemem Linux skonfigurowanej przez serwer proxy, jeśli nie ma zainstalowanej wersji 2,7 języka Python.

## <a name="backup-frequency-and-retention"></a>Częstotliwość i przechowywanie kopii zapasowych

**Ustawienie** | **Limity**
--- | ---
Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (maszyna/obciążenie) | 9999.
Maksymalny czas wygaśnięcia dla punktu odzyskiwania | Brak limitu.
Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie (rozszerzenie maszyny wirtualnej platformy Azure) | Raz dziennie.
Maksymalna częstotliwość tworzenia kopii zapasowych w magazynie (Agent MARS) | Trzy kopie zapasowe dziennie.
Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS | Co 15 minut dla SQL Server.<br/><br/> Raz na godzinę dla innych obciążeń.
Przechowywanie punktów odzyskiwania | Codziennie, co tydzień, co miesiąc i co rok.
Maksymalny okres przechowywania | Zależnie od częstotliwości wykonywania kopii zapasowych.
Punkty odzyskiwania na dysku programu DPM/usługi MABS | 64 dla serwerów plików i 448 dla serwerów aplikacji.<br/><br/> Punkty odzyskiwania na taśmie nie mają ograniczeń w przypadku lokalnego programu DPM.

## <a name="supported-restore-methods"></a>Obsługiwane metody przywracania

**Restore — Metoda** | **Szczegóły**
--- | ---
Utworzenie nowej maszyny wirtualnej. | Podczas procesu przywracania można utworzyć maszynę wirtualną. <br/><br/> Ta opcja pobiera podstawową maszynę wirtualną w górę i w działaniu. Możesz określić nazwę maszyny wirtualnej, grupę zasobów, sieć wirtualną, podsieć i magazyn.  
Przywracanie dysku | Można przywrócić dysk i użyć go do utworzenia maszyny wirtualnej.<br/><br/> Po wybraniu tej opcji Azure Backup kopiuje dane z magazynu na wybrane konto magazynu. Zadanie przywracania generuje szablon. Możesz pobrać ten szablon, użyć go do określenia niestandardowych ustawień maszyny wirtualnej i utworzyć maszynę wirtualną.<br/><br/> Ta opcja pozwala określić więcej ustawień, które poprzednią opcję tworzenia maszyny wirtualnej.<br/><br/>
Zastępowanie istniejącego dysku | Można przywrócić dysk, a następnie użyć przywróconego dysku, aby zastąpić dysk, który znajduje się obecnie na maszynie wirtualnej.
Przywróć pliki | Można odzyskać pliki z wybranego punktu odzyskiwania. Pobierz skrypt, aby zainstalować dysk maszyny wirtualnej z punktu odzyskiwania. Następnie przejrzyj woluminy dysków, aby znaleźć pliki/foldery, które chcesz odzyskać, i Odinstaluj dysk po zakończeniu.

## <a name="support-for-file-level-restore"></a>Obsługa przywracania na poziomie plików

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie plików w różnych systemach operacyjnych | Można przywracać pliki na dowolnym komputerze, który ma ten sam (lub zgodny) system operacyjny, co maszyna wirtualna z kopią zapasową. Zobacz [tabelę zgodnych systemów operacyjnych](backup-azure-restore-files-from-vm.md#system-requirements).
Przywracanie plików na klasycznych maszynach wirtualnych | Bez pomocy technicznej.
Przywracanie plików z szyfrowanych maszyn wirtualnych | Bez pomocy technicznej.
Przywracanie plików z kont magazynu z ograniczeniami w sieci | Bez pomocy technicznej.
Przywracanie plików na maszynach wirtualnych przy użyciu funkcji miejsca do magazynowania systemu Windows | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Zamiast tego należy przywrócić pliki na zgodnej maszynie wirtualnej.
Przywracanie plików na maszynie wirtualnej z systemem Linux przy użyciu tablic LVM/RAID | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Przywracanie na zgodnej maszynie wirtualnej.
Przywracanie plików przy użyciu specjalnych ustawień sieci | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej. <br/><br/> Przywracanie na zgodnej maszynie wirtualnej.

## <a name="support-for-vm-management"></a>Obsługa zarządzania maszyną wirtualną

Poniższa tabela zawiera podsumowanie obsługi kopii zapasowych podczas zadań zarządzania maszyną wirtualną, takich jak dodawanie lub zastępowanie dysków maszyn wirtualnych.

**Przywracanie** | **Obsługiwane**
--- | ---
Przywróć w ramach subskrypcji/regionu/strefy. | Bez pomocy technicznej.
Przywracanie do istniejącej maszyny wirtualnej | Użyj opcji Zamień dysk.
Przywróć dysk z włączonym kontem magazynu dla usługi Azure szyfrowanie usługi Storage (SSE) | Bez pomocy technicznej.<br/><br/> Przywracanie do konta, które nie ma włączonej funkcji SSE.
Przywracanie do mieszanych kont magazynu |Bez pomocy technicznej.<br/><br/> Na podstawie typu konta magazynu wszystkie przywrócone dyski będą w warstwie Premium lub standardowa, a nie mieszane.
Przywracanie maszyny wirtualnej bezpośrednio do zestawu dostępności | W przypadku dysków zarządzanych można przywrócić dysk i użyć opcji zestawu dostępności w szablonie.<br/><br/> Nieobsługiwane w przypadku dysków niezarządzanych. W przypadku dysków niezarządzanych Przywróć dysk, a następnie utwórz maszynę wirtualną w zestawie dostępności.
Przywróć kopię zapasową niezarządzanych maszyn wirtualnych po uaktualnieniu do zarządzanej maszyny wirtualnej| Obsługiwane.<br/><br/> Można przywrócić dyski, a następnie utworzyć zarządzaną maszynę wirtualną.
Przywróć maszynę wirtualną do punktu przywracania przed przeprowadzeniem migracji maszyny wirtualnej na dyski zarządzane | Obsługiwane.<br/><br/> Przywracasz do dysków niezarządzanych (domyślnie), przekonwertuj przywrócone dyski na dysk zarządzany i Utwórz maszynę wirtualną z dyskami zarządzanymi.
Przywróć maszynę wirtualną, która została usunięta. | Obsługiwane.<br/><br/> Można przywrócić maszynę wirtualną z punktu odzyskiwania.
Przywracanie maszyny wirtualnej kontrolera domeny (DC), która jest częścią konfiguracji z obsługą kilku kontrolerów domeny za pomocą portalu | Obsługiwane w przypadku przywracania dysku i tworzenia maszyny wirtualnej przy użyciu programu PowerShell.
Przywróć MASZYNę wirtualną w innej sieci wirtualnej |Obsługiwane.<br/><br/> Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie.

## <a name="vm-compute-support"></a>Obsługa obliczeń maszyn wirtualnych

**Obliczanie** | **Pomoc techniczna**
--- | ---
Rozmiar maszyny wirtualnej |Rozmiar maszyny wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora i 1 GB pamięci RAM.<br/><br/> [Dowiedz się więcej.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Tworzenie kopii zapasowych maszyn wirtualnych w [zestawach dostępności](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Obsługiwane.<br/><br/> Nie można przywrócić maszyny wirtualnej w dostępnym zestawie przy użyciu opcji, aby szybko utworzyć maszynę wirtualną. Zamiast tego, podczas przywracania maszyny wirtualnej Przywróć dysk i użyj go do wdrożenia maszyny wirtualnej lub Przywróć dysk i użyj go, aby zastąpić istniejący dysk.
Tworzenie kopii zapasowych maszyn wirtualnych wdrożonych przy użyciu [korzyści z używania hybrydowego (centrum)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Obsługiwane.
Tworzenie kopii zapasowych maszyn wirtualnych wdrożonych w [zestawie skalowania](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Bez pomocy technicznej.
Tworzenie kopii zapasowych maszyn wirtualnych wdrażanych z poziomu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Opublikowane przez firmę Microsoft, inna firma) |Obsługiwane.<br/><br/> Na maszynie wirtualnej musi być uruchomiony obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko zgodny system operacyjny (nie wcześniejszy niż starszy lub nowszy). Nie przywracamy maszyn wirtualnych portalu Azure Marketplace jako maszyn wirtualnych, ponieważ wymagają one informacji o zakupie, ale tylko w postaci dysków.
Tworzenie kopii zapasowych maszyn wirtualnych wdrożonych z obrazu niestandardowego (innych firm) |Obsługiwane.<br/><br/> Na maszynie wirtualnej musi być uruchomiony obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko zgodny system operacyjny (nie wcześniejszy niż starszy lub nowszy).
Tworzenie kopii zapasowych maszyn wirtualnych migrowanych do platformy Azure| Obsługiwane.<br/><br/> Aby można było utworzyć kopię zapasową maszyny wirtualnej, Agent maszyny wirtualnej musi być zainstalowany na migrowanej maszynie.
Tworzenie kopii zapasowej spójności między MASZYNami wirtualnymi | Azure Backup nie zapewnia spójności danych i aplikacji na wielu maszynach wirtualnych.
Tworzenie kopii zapasowej przy użyciu [ustawień diagnostycznych](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Nieobsługiwane. <br/><br/> Jeśli przywracanie maszyny wirtualnej platformy Azure z ustawieniami diagnostycznymi jest wyzwalane przy użyciu opcji [Utwórz nową](backup-azure-arm-restore-vms.md#create-a-vm) , przywracanie nie powiedzie się.
Przywracanie przypiętych do strefy maszyn wirtualnych | Obsługiwane (w przypadku maszyny wirtualnej, której kopia zapasowa jest wykonywana po 2019 Jan i [strefa dostępności](https://azure.microsoft.com/global-infrastructure/availability-zones/) jest dostępna).<br/><br/>Obecnie obsługujemy przywracanie do tej samej strefy, która jest przypięta na maszynach wirtualnych. Jeśli jednak strefa jest niedostępna, przywracanie nie powiedzie się.
Maszyny wirtualne Gen2 | Obsługiwane <br> Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie [maszyn wirtualnych Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Po przywróceniu tych maszyn wirtualnych z punktu odzyskiwania są one przywracane jako [maszyny wirtualne Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Obsługa magazynu maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Utwórz kopię zapasową maszyny wirtualnej z 16 lub mniej dyskami danych.<BR> Aby skorzystać z prywatnej wersji zapoznawczej maszyn wirtualnych z ponad 16 dyskami (do 32 dysków), wyślij wiadomość na adres AskAzureBackupTeam@microsoft.com
Rozmiar dysku danych | Rozmiar poszczególnych dysków może należeć do 32 TB i maksymalnie 256 TB łączny dla wszystkich dysków w maszynie wirtualnej.
Typ usługi Storage | HDD w warstwie Standardowa, SSD w warstwie Standardowa, SSD w warstwie Premium.
Dyski zarządzane | Obsługiwane.
Zaszyfrowane dyski | Obsługiwane.<br/><br/> Można utworzyć kopię zapasową maszyn wirtualnych platformy Azure z włączonym Azure Disk Encryption (z aplikacją usługi Azure AD lub bez niej).<br/><br/> Zaszyfrowane maszyny wirtualne nie mogą zostać odzyskane na poziomie plików/folderów. Należy odzyskać całą maszynę wirtualną.<br/><br/> Możesz włączyć szyfrowanie na maszynach wirtualnych, które są już chronione przez Azure Backup.
Dyski z włączonym akcelerator zapisu | Bez pomocy technicznej.<br/><br/> Usługa Azure Backup automatycznie wyklucza dyski z akcelerator zapisu włączony podczas tworzenia kopii zapasowej. Ponieważ nie są one tworzone w ramach kopii zapasowej, nie będzie można przywrócić tych dysków z punktów odzyskiwania maszyny wirtualnej.
Tworzenie kopii zapasowej & Przywracanie deduplikowanych maszyn wirtualnych/dysków | Azure Backup nie obsługuje deduplikacji. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  -Azure Backup nie deduplikowania między maszynami wirtualnymi w magazynie Recovery Services <br/> <br/>  — Jeśli podczas przywracania istnieją maszyny wirtualne w stanie deduplikacji, nie można przywrócić plików, ponieważ magazyn nie rozumie tego formatu. Jednak będzie można pomyślnie wykonać pełne przywracanie maszyny wirtualnej.
Dodawanie dysku do chronionej maszyny wirtualnej | Obsługiwane.
Zmień rozmiar dysku na chronionej maszynie wirtualnej | Obsługiwane.
Magazyn udostępniony| Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu udostępniony wolumin klastra (CSV) lub Serwer plików skalowalny w poziomie nie jest obsługiwane. Podczas tworzenia kopii zapasowej mogą wystąpić błędy autorów woluminów. W przypadku przywracania dyski zawierające woluminy woluminów CSV mogą nie zostać dołączone.

## <a name="vm-network-support"></a>Obsługa sieci maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Liczba interfejsów sieciowych (nic) | Maksymalna liczba kart sieciowych obsługiwanych przez określony rozmiar maszyny wirtualnej platformy Azure.<br/><br/> Karty sieciowe są tworzone podczas tworzenia maszyny wirtualnej podczas procesu przywracania.<br/><br/> Liczba kart sieciowych na przywracanej maszynie wirtualnej odzwierciedla liczbę kart sieciowych w maszynie wirtualnej po włączeniu ochrony. Usuwanie kart sieciowych po włączeniu ochrony nie ma wpływu na liczbę.
Zewnętrzny/wewnętrzny moduł równoważenia obciążenia |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych przy użyciu specjalnych ustawień sieci.
Wiele zarezerwowanych adresów IP |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych przy użyciu specjalnych ustawień sieci.
Maszyny wirtualne z wieloma kartami sieciowymi| Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych przy użyciu specjalnych ustawień sieci.
Maszyny wirtualne z publicznymi adresami IP| Obsługiwane.<br/><br/> Skojarz istniejący publiczny adres IP z kartą sieciową lub Utwórz adres i skojarz go z kartą sieciową po zakończeniu przywracania.
Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) na karcie sieciowej/podsieci. |Obsługiwane.
Statyczny adres IP | Bez pomocy technicznej.<br/><br/> Nowa maszyna wirtualna utworzona na podstawie punktu przywracania ma przypisany dynamiczny adres IP.<br/><br/> W przypadku klasycznych maszyn wirtualnych nie można utworzyć kopii zapasowej maszyny wirtualnej z zastrzeżonym adresem IP i bez zdefiniowanego punktu końcowego.
Dynamiczny adres IP |Obsługiwane.<br/><br/> Jeśli karta sieciowa na źródłowej maszynie wirtualnej używa dynamicznego adresowania IP, domyślnie karta sieciowa na przywróconej maszynie wirtualnej będzie używać tego programu.
Azure Traffic Manager| Obsługiwane.<br/><br/>Jeśli kopia zapasowa maszyny wirtualnej znajduje się w Traffic Manager, ręcznie Dodaj przywróconą maszynę wirtualną do tego samego wystąpienia Traffic Manager.
System DNS Azure |Obsługiwane.
Niestandardowe DNS |Obsługiwane.
Łączność wychodząca za pośrednictwem serwera proxy HTTP | Obsługiwane.<br/><br/> Uwierzytelniony serwer proxy nie jest obsługiwany.
Punkty końcowe usługi dla sieci wirtualnej| Obsługiwane.<br/><br/> Ustawienia zapory i konta magazynu sieci wirtualnej powinny zezwalać na dostęp ze wszystkich sieci.

## <a name="vm-security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania maszyn wirtualnych

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych magazynowanych:

Ruch sieciowy do platformy Azure:

- Ruch kopii zapasowych z serwerów do magazynu Recovery Services jest szyfrowany przy użyciu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- W magazynie usługi Recovery Services dane kopii zapasowych są przechowywane w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

  > [!WARNING]
  > Po skonfigurowaniu magazynu tylko ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

Bezpieczeństwo danych:

- Podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *w ramach* maszyny wirtualnej.
- Azure Backup obsługuje Azure Disk Encryption, który używa funkcji BitLocker na maszynach wirtualnych z systemem Windows oraz zarządzania urządzeniami **Crypt** na komputerach z systemem Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md), które chroni dane magazynowane.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | ![Tak][green] | ![Tak][green]
Maszyny wirtualne platformy Azure | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="vm-compression-support"></a>Obsługa kompresji maszyny wirtualnej

Funkcja Backup obsługuje kompresję ruchu kopii zapasowej, jak przedstawiono w poniższej tabeli. Pamiętaj o następujących kwestiach:

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta usługi Azure Storage w sieci magazynowania. Nie jest konieczne kompresowanie tego ruchu.
- Jeśli używasz programu DPM lub serwera usługi MAB, możesz zaoszczędzić przepustowość, kompresując dane przed utworzeniem ich kopii zapasowej w programie DPM/serwera usługi MAB.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresuj do magazynu (HTTPS)**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | Nie dotyczy | ![Tak][green]
Maszyny wirtualne platformy Azure | Nie dotyczy | Nie dotyczy
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md).
- [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
- [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
- [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
