---
title: Tabela obsługi dla maszyn wirtualnych platformy Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczeń podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389294"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Tabela obsługi dla maszyn wirtualnych platformy Azure

Za pomocą [usługi Azure Backup](backup-overview.md) można wykonać kopię zapasową maszyn i obciążeń lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule podsumowano ustawienia i ograniczenia pomocy technicznej podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup.

Inne macierze wsparcia:

- [Macierz obsługi ogólnej](backup-support-matrix.md) dla usługi Azure Backup
- [Macierz obsługi](backup-support-matrix-mabs-dpm.md) kopii zapasowej serwera/programu Windows Center (Program Data Protection Manager) usługi Azure Backup
- [Macierz obsługi](backup-support-matrix-mars-agent.md) kopii zapasowej za pomocą agenta usług odzyskiwania platformy Microsoft Azure (MARS)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Poniżej opisano, jak można wykonać kopię zapasową i przywrócić maszyny wirtualne platformy Azure za pomocą usługi Azure Backup.

**Scenariusz** | **Kopia zapasowa** | **Agent** |**Przywracanie**
--- | --- | --- | ---
Bezpośrednia kopia zapasowa maszyn wirtualnych platformy Azure  | Przeć całą maszynę wirtualną.  | Nie jest potrzebny żaden dodatkowy agent na maszynie Wirtualnej platformy Azure. Usługa Azure Backup instaluje i używa rozszerzenia [agenta maszyny Wirtualnej platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) który jest uruchomiony na maszynie Wirtualnej. | Przywróć w następujący sposób:<br/><br/> - **Utwórz podstawową maszynę wirtualną**. Jest to przydatne, jeśli maszyna wirtualna nie ma specjalnej konfiguracji, takiej jak wiele adresów IP.<br/><br/> - **Przywróć dysk maszyny Wirtualnej**. Przywróć dysk. Następnie dołącz ją do istniejącej maszyny Wirtualnej lub utwórz nową maszynę wirtualną z dysku przy użyciu programu PowerShell.<br/><br/> - **Zamień dysk maszyny Wirtualnej**. Jeśli maszyna wirtualna istnieje i używa dysków zarządzanych (niezaszyfrowanych), można przywrócić dysk i użyć go do zastąpienia istniejącego dysku na maszynie wirtualnej.<br/><br/> - **Przywracanie określonych plików/folderów**. Pliki/foldery można przywrócić z maszyny Wirtualnej, a nie z całej maszyny Wirtualnej.
Bezpośrednia kopia zapasowa maszyn wirtualnych platformy Azure (tylko system Windows)  | 19 ust. | Zainstaluj [agenta usług odzyskiwania platformy Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Agenta MARS można uruchomić wraz z rozszerzeniem kopii zapasowej dla agenta maszyny Wirtualnej platformy Azure, aby wykonać kopię zapasową maszyny Wirtualnej na poziomie pliku/folderu. | Przywracanie określonych folderów/plików.
Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure na serwerze kopii zapasowej  | 19 ust. stan systemu/pliki z gołym metalem; danych aplikacji do programu System Center DPM lub do programu Microsoft Azure Backup Server (MABS).<br/><br/> Następnie program DPM/MABS cofa się do magazynu kopii zapasowych. | Zainstaluj agenta ochrony programu DPM/MABS na maszynie wirtualnej. Agent MARS jest zainstalowany na programie DPM/MABS.| Przywracanie plików/folderów/woluminów; stan systemu/pliki z gołym metalem; danych aplikacji.

Dowiedz się więcej o kopii zapasowej [przy użyciu serwera kopii zapasowych](backup-architecture.md#architecture-back-up-to-dpmmabs) i o [wymaganiach dotyczących pomocy technicznej](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **Usługa Azure Backup obsługuje teraz selektywną kopię zapasową dysku i przywracanie przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.**
>
>Obecnie usługa Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (system operacyjny i dane) na maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej. Dzięki funkcji wykluczeń dysku można uzyskać opcję tworzenia kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie Wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb związanych z tworzeniem kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzbioru dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i z magazynu.
>
>**Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Obsługiwane akcje tworzenia kopii zapasowych

**Akcja** | **Pomoc techniczna**
--- | ---
Śp. | Obsługiwane.<br/><br/> Migawka jest tylko spójne w awariach, a nie app-spójne.
Tworzenie kopii zapasowych dysków po migracji na dyski zarządzane | Obsługiwane.<br/><br/> Kopia zapasowa będzie nadal działać. Nie jest wymagana żadna akcja.
Tworzenie kopii zapasowych dysków zarządzanych po włączeniu blokady grupy zasobów | Bez pomocy technicznej.<br/><br/> Usługa Azure Backup nie może usunąć starszych punktów przywracania, a kopie zapasowe zaczną się niepowodzeniem po osiągnięciu maksymalnego limitu punktów przywracania.
Modyfikowanie zasad tworzenia kopii zapasowych dla maszyny Wirtualnej | Obsługiwane.<br/><br/> Maszyna wirtualna będzie archiwiza pomocą ustawień harmonogramu i przechowywania w nowych zasadach. Jeśli ustawienia przechowywania są rozszerzone, istniejące punkty odzyskiwania są oznaczane i przechowywane. Jeśli zostaną one zmniejszone, istniejące punkty odzyskiwania zostaną przycięte w następnym zadaniu oczyszczania i ostatecznie usunięte.
Anulowanie zadania tworzenia kopii zapasowej| Obsługiwane podczas procesu migawki.<br/><br/> Nie jest obsługiwana, gdy migawka jest przenoszona do magazynu.
Dzmówiętanie zapasem maszyny wirtualnej w innym regionie lub subskrypcji |Bez pomocy technicznej.
Kopie zapasowe dziennie (za pośrednictwem rozszerzenia maszyny Wirtualnej platformy Azure) | Jedna zaplanowana kopia zapasowa dziennie.<br/><br/>Usługa Azure Backup obsługuje do dziewięciu kopii zapasowych na żądanie dziennie, ale firma Microsoft zaleca nie więcej niż cztery codzienne kopie zapasowe na żądanie, aby zapewnić najlepszą wydajność.
Kopie zapasowe dziennie (za pośrednictwem agenta MARS) | Trzy zaplanowane kopie zapasowe dziennie.
Kopie zapasowe dziennie (przez DPM/ MABS) | Dwa zaplanowane kopie zapasowe dziennie.
Miesięczna/roczna kopia zapasowa| Nie jest obsługiwana podczas tworzenia kopii zapasowej z rozszerzeniem maszyny Wirtualnej platformy Azure. Obsługiwane są tylko codziennie i co tydzień.<br/><br/> Można skonfigurować zasady przechowywania dziennych/tygodniowych kopii zapasowych dla miesięcznego /rocznego okresu przechowywania.
Automatyczna regulacja zegara | Bez pomocy technicznej.<br/><br/> Usługa Azure Backup nie dostosowuje się automatycznie do zmian czasu letniego podczas wykonywania kopii zapasowej maszyny Wirtualnej.<br/><br/>  W razie potrzeby ręcznie zmodyfikuj zasady.
[Funkcje zabezpieczeń dla hybrydowej kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Wyłączenie funkcji zabezpieczeń nie jest obsługiwane.
Wrócę zapasową maszyny wirtualnej, której czas pracy maszyny został zmieniony | Bez pomocy technicznej.<br/><br/> Jeśli czas pracy komputera zostanie zmieniony na przyszłą datę i godzinę po włączeniu kopii zapasowej dla tej maszyny wirtualnej; Jednak nawet jeśli zmiana czasu zostanie przywrócona, pomyślna kopia zapasowa nie jest gwarantowana.  

## <a name="operating-system-support-windows"></a>Obsługa systemu operacyjnego (Windows)

W poniższej tabeli podsumowano obsługiwane systemy operacyjne podczas tworzenia kopii zapasowej maszyn wirtualnych systemu Windows Azure.

**Scenariusz** | **Obsługa systemu operacyjnego**
--- | ---
Czepka z rozszerzeniem agenta maszyny Wirtualnej platformy Azure | - Windows 10 Client (tylko 64 bit) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Centrum danych/Core/Centrum danych Rdzeń/Standard) <br/><br/> - Windows Server 2012 R2 (centrum danych/standard) <br/><br/> - Windows Server 2008 R2 (RTM i SP1 Standard)  <br/><br/> - Windows Server 2008 (tylko 64-bitowe)
Czemięć zapasów za pomocą agenta MARS | [Obsługiwane](backup-support-matrix-mars-agent.md#supported-operating-systems) systemy operacyjne.
Czelonie zapasowe za pomocą programu DPM/MABS | Obsługiwane systemy operacyjne do tworzenia kopii zapasowych z [MABS](backup-mabs-protection-matrix.md) i [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.

## <a name="support-for-linux-backup"></a>Obsługa kopii zapasowej systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Akcja** | **Pomoc techniczna**
--- | ---
3 5 000 000 000 000 000 000 000 000 000 000 | Plik spójne kopii zapasowej.<br/><br/> Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Podczas przywracania można utworzyć nową maszynę wirtualną, przywrócić dysk i użyć jej do utworzenia maszyny Wirtualnej lub przywrócić dysk i użyć jej do zastąpienia dysku na istniejącej maszynie wirtualnej. Można również przywrócić poszczególne pliki i foldery.
Cześnięć zapasy maszyn wirtualnych z systemem Linux Azure za pomocą agenta MARS | Bez pomocy technicznej.<br/><br/> Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows.
Cześnięć zapasy maszyn wirtualnych platformy Azure z programem DPM/MABS | Bez pomocy technicznej.

## <a name="operating-system-support-linux"></a>Obsługa systemu operacyjnego (Linux)

W przypadku kopii zapasowych maszyn wirtualnych z systemem Azure w systemie Azure backup obsługuje listę dystrybucji systemu Linux [zatwierdzonych przez platformę Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Pamiętaj o następujących kwestiach:

- Usługa Azure Backup nie obsługuje systemu operacyjnego Core Linux.
- Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.
- Inne dystrybucje systemu Linux bring-your-own może działać tak długo, jak [agent maszyny Wirtualnej platformy Azure dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) jest dostępny na maszynie Wirtualnej i tak długo, jak Python jest obsługiwany.
- Usługa Azure Backup nie obsługuje maszyny Wirtualnej z systemem Linux skonfigurowaną przez serwer proxy, jeśli nie ma zainstalowanej wersji 2.7 języka Python.

## <a name="backup-frequency-and-retention"></a>Częstotliwość tworzenia kopii zapasowych i przechowywanie

**Ustawienie** | **Limity**
--- | ---
Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (komputer/obciążenie) | 9999.
Maksymalny czas wygaśnięcia punktu odzyskiwania | Bez limitu.
Maksymalna częstotliwość tworzenia kopii zapasowych do przechowalni (rozszerzenie maszyny Wirtualnej platformy Azure) | Raz dziennie.
Maksymalna częstotliwość tworzenia kopii zapasowych do przechowalni (agent MARS) | Trzy kopie zapasowe dziennie.
Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS | Co 15 minut dla programu SQL Server.<br/><br/> Raz na godzinę dla innych obciążeń.
Przechowywanie punktów odzyskiwania | Codziennie, co tydzień, co miesiąc i co roku.
Maksymalny okres przechowywania | Zależnie od częstotliwości wykonywania kopii zapasowych.
Punkty odzyskiwania na dysku programu DPM/usługi MABS | 64 dla serwerów plików i 448 dla serwerów aplikacji.<br/><br/> Punkty odzyskiwania na taśmie nie mają ograniczeń w przypadku lokalnego programu DPM.

## <a name="supported-restore-methods"></a>Obsługiwane metody przywracania

**Opcja Przywróć** | **Szczegóły**
--- | ---
**Utworzenie nowej maszyny wirtualnej.** | Szybko tworzy i pobiera podstawową maszynę wirtualną i działa z punktu przywracania.<br/><br/> Można określić nazwę maszyny Wirtualnej, wybrać grupę zasobów i sieć wirtualną (VNet), w której zostanie ona umieszczona, i określić konto magazynu dla przywróconej maszyny Wirtualnej. Nowa maszyna wirtualna musi zostać utworzona w tym samym regionie co źródłowy maszynę wirtualna.
**Przywracanie dysku** | Przywraca dysk maszyny Wirtualnej, który następnie może służyć do tworzenia nowej maszyny Wirtualnej.<br/><br/> Usługa Azure Backup udostępnia szablon ułatwiające dostosowywanie i tworzenie maszyny Wirtualnej. <br/><br> Zadanie przywracania generuje szablon, który można pobrać i użyć do określenia niestandardowych ustawień maszyny Wirtualnej i utworzenia maszyny Wirtualnej.<br/><br/> Dyski są kopiowane do określonej grupy zasobów.<br/><br/> Alternatywnie można dołączyć dysk do istniejącej maszyny Wirtualnej lub utworzyć nową maszynę wirtualną przy użyciu programu PowerShell.<br/><br/> Ta opcja jest przydatna, jeśli chcesz dostosować maszynę wirtualną, dodać ustawienia konfiguracji, których nie było w czasie tworzenia kopii zapasowej, lub dodać ustawienia, które muszą być skonfigurowane przy użyciu szablonu lub programu PowerShell.
**Zamiana istniejącego** | Można przywrócić dysk i użyć go do zastąpienia dysku na istniejącej maszynie wirtualnej.<br/><br/> Bieżąca maszyna wirtualna musi istnieć. Jeśli ta opcja została usunięta, nie można jej użyć.<br/><br/> Usługa Azure Backup wykonuje migawkę istniejącej maszyny Wirtualnej przed wymianą dysku i przechowuje ją w określonej lokalizacji przejściowej. Istniejące dyski podłączone do maszyny Wirtualnej są zastępowane wybranym punktem przywracania.<br/><br/> Migawka jest kopiowana do magazynu i zachowywana zgodnie z zasadami przechowywania. <br/><br/> Po operacji wymiany dysku oryginalny dysk jest zachowywany w grupie zasobów. Można ręcznie usunąć oryginalne dyski, jeśli nie są one potrzebne. <br/><br/>Zastępowanie istniejących jest obsługiwane dla niezaszyfrowanych zarządzanych maszyn wirtualnych. Nie jest obsługiwany dla dysków niezarządzanych, [uogólnionych maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)ani maszyn wirtualnych [utworzonych przy użyciu obrazów niestandardowych.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)<br/><br/> Jeśli punkt przywracania ma więcej lub mniej dysków niż bieżąca maszyna wirtualna, liczba dysków w punkcie przywracania będzie odzwierciedlać tylko konfigurację maszyny Wirtualnej.<br><br> Zastąp istniejące nie jest obsługiwane dla maszyn wirtualnych z połączonych zasobów (takich jak [przypisane przez użytkownika tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub [Magazynu kluczy),](https://docs.microsoft.com/azure/key-vault/key-vault-overview)ponieważ kopia zapasowa klient-aplikacja nie ma uprawnień do tych zasobów podczas wykonywania przywracania.
**Region krzyżowy (region pomocniczy)** | Przywracanie między regionem może służyć do przywracania maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest [regionem sparowanym na platformie Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Można przywrócić wszystkie maszyny wirtualne platformy Azure dla wybranego punktu odzyskiwania, jeśli kopia zapasowa jest wykonywana w regionie pomocniczym.<br><br> Ta funkcja jest dostępna dla poniższych opcji:<br> * [Tworzenie maszyny Wirtualnej](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Przywracanie dysków](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Obecnie nie obsługujemy opcji [Zamień istniejące dyski.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Uprawnienia<br> Operację przywracania w regionie pomocniczym mogą wykonywać administratorzy kopii zapasowych i administratorzy aplikacji.

## <a name="support-for-file-level-restore"></a>Obsługa przywracania na poziomie pliku

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie plików w systemach operacyjnych | Pliki można przywrócić na dowolnym komputerze, który ma ten sam (lub zgodny) system operacyjny jako kopia zapasowa maszyny Wirtualnej. Zobacz [tabelę Kompatybilny system operacyjny](backup-azure-restore-files-from-vm.md#system-requirements).
Przywracanie plików z zaszyfrowanych maszyn wirtualnych | Bez pomocy technicznej.
Przywracanie plików z kont magazynu z ograniczeniami sieciowymi | Bez pomocy technicznej.
Przywracanie plików na maszynach wirtualnych przy użyciu miejsc do magazynowania systemu Windows | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Zamiast tego przywróć pliki na zgodnej maszynie Wirtualnej.
Przywracanie plików na maszynie wirtualnej systemu Linux przy użyciu macierzy LVM/raid | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej.<br/><br/> Przywróć na zgodnej maszynie Wirtualnej.
Przywracanie plików ze specjalnymi ustawieniami sieciowymi | Przywracanie nie jest obsługiwane na tej samej maszynie wirtualnej. <br/><br/> Przywróć na zgodnej maszynie Wirtualnej.

## <a name="support-for-vm-management"></a>Obsługa zarządzania maszynami wirtualnymi

W poniższej tabeli podsumowano obsługę kopii zapasowej podczas zadań zarządzania maszyną wirtualną, takich jak dodawanie lub zastępowanie dysków maszyn wirtualnych.

**Przywracanie** | **Obsługiwane**
--- | ---
Przywracanie w całej subskrypcji/region/strefa. | Bez pomocy technicznej.
Przywracanie istniejącej maszyny Wirtualnej | Użyj opcji Zamień dysk.
Przywracanie dysku z kontem magazynu włączonym dla szyfrowania usługi Azure Storage Service (SSE) | Bez pomocy technicznej.<br/><br/> Przywróć konto, które nie ma włączonej SSE.
Przywracanie na mieszanych kontach magazynu |Bez pomocy technicznej.<br/><br/> Na podstawie typu konta magazynu wszystkie przywrócone dyski będą premium lub standardowe i nie zostaną zmieszane.
Przywracanie maszyny wirtualnej bezpośrednio do zestawu dostępności | W przypadku dysków zarządzanych można przywrócić dysk i użyć opcji zestawu dostępności w szablonie.<br/><br/> Nie jest obsługiwana dla dysków niezarządzanych. W przypadku dysków niezarządzanych przywróć dysk, a następnie utwórz maszynę wirtualną w zestawie dostępności.
Przywracanie kopii zapasowej niezarządzanych maszyn wirtualnych po uaktualnieniu do zarządzanej maszyny wirtualnej| Obsługiwane.<br/><br/> Można przywrócić dyski, a następnie utworzyć zarządzaną maszynę wirtualną.
Przywracanie maszyny Wirtualnej do punktu przywracania przed migracją maszyny wirtualnej na dyski zarządzane | Obsługiwane.<br/><br/> Przywracanie na dyski niezarządzane (domyślnie), konwertowanie przywróconych dysków na dysk zarządzany i tworzenie maszyny wirtualnej z dyskami zarządzanymi.
Przywróć maszynę wirtualną, która została usunięta. | Obsługiwane.<br/><br/> Maszynę Wirtualną można przywrócić z punktu odzyskiwania.
Przywracanie maszyny Wirtualnej kontrolera domeny, która jest częścią konfiguracji wielu kontrolerów domeny za pośrednictwem portalu | Obsługiwane w przypadku przywrócenia dysku i utworzenia maszyny Wirtualnej przy użyciu programu PowerShell.
Przywracanie maszyny Wirtualnej w innej sieci wirtualnej |Obsługiwane.<br/><br/> Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie.

## <a name="vm-compute-support"></a>Obsługa obliczeń maszyny Wirtualnej

**Obliczanie** | **Pomoc techniczna**
--- | ---
Rozmiar maszyny wirtualnej |Dowolny rozmiar maszyny Wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora CPU i 1 GB pamięci RAM.<br/><br/> [Dowiedz się więcej.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Twórz zapas maszyn wirtualnych w [zestawach dostępności](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Obsługiwane.<br/><br/> Nie można przywrócić maszyny Wirtualnej w dostępnym zestawie przy użyciu opcji, aby szybko utworzyć maszynę wirtualną. Zamiast tego podczas przywracania maszyny Wirtualnej, przywrócić dysk i użyć go do wdrożenia maszyny Wirtualnej lub przywrócić dysk i użyć go do zastąpienia istniejącego dysku.
Czepuje zapas maszyn wirtualnych wdrożonych z [korzyścią dla użycia hybrydowego (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Obsługiwane.
Śp. [scale set](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Bez pomocy technicznej.
3.00.2019 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Opublikowano przez firmę Microsoft, osoby trzecie) |Obsługiwane.<br/><br/> Maszyna wirtualna musi być uruchomiona obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko do zgodnego systemu operacyjnego (nie wcześniej lub później systemu operacyjnego). Nie przywracamy maszyn wirtualnych usługi Azure Marketplace kopii zapasowej jako maszyny wirtualne, ponieważ wymagają one informacji o zakupie, ale tylko jako dyski.
Konfigurowanie kopii zapasowych maszyn wirtualnych wdrożonych z obrazu niestandardowego (innej firmy) |Obsługiwane.<br/><br/> Maszyna wirtualna musi być uruchomiona obsługiwany system operacyjny.<br/><br/> Podczas odzyskiwania plików na maszynie wirtualnej można przywrócić tylko do zgodnego systemu operacyjnego (nie wcześniej lub później systemu operacyjnego).
3.| Obsługiwane.<br/><br/> Aby można było wywklić kopii zapasowej maszyny Wirtualnej, agent maszyny Wirtualnej musi być zainstalowany na zmigrowanym komputerze.
Łańko chylić spójność wielu maszyn wirtualnych | Usługa Azure Backup nie zapewnia spójności danych i aplikacji na wielu maszynach wirtualnych.
Kopia zapasowa z [ustawieniami diagnostycznymi](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Nieobsługiwane. <br/><br/> Jeśli przywracanie maszyny Wirtualnej platformy Azure z ustawieniami diagnostycznymi jest wyzwalane przy użyciu opcji [Utwórz nowy,](backup-azure-arm-restore-vms.md#create-a-vm) przywracanie nie powiedzie się.
Przywracanie maszyn wirtualnych przypiętych do strefy | Obsługiwane (dla maszyny Wirtualnej, która jest archiwizowana po styczniu 2019 r. i gdzie [strefa dostępności](https://azure.microsoft.com/global-infrastructure/availability-zones/) są dostępne).<br/><br/>Obecnie obsługujemy przywracanie do tej samej strefy, która jest przypięta w maszynach wirtualnych. Jeśli jednak strefa jest niedostępna, przywracanie nie powiedzie się.
Maszyny wirtualne Gen2 | Obsługiwane <br> Usługa Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie [maszyn wirtualnych gen2.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Gdy te maszyny wirtualne są przywracane z punktu odzyskiwania, są przywracane jako [maszyny wirtualne Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Obsługa pamięci masowej maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Instaluj zapasową maszyny Wirtualnej z 16 lub mniej dysków danych.<BR> Aby skorzystać z prywatnej wersji zapoznawczej maszyn wirtualnych z ponad 16 dyskami (do 32 dysków), wyślij wiadomość na adres AskAzureBackupTeam@microsoft.com
Rozmiar dysku danych | Rozmiar dysku indywidualnego może wynosić do 32 TB i maksymalnie 256 TB łącznie dla wszystkich dysków na maszynie wirtualnej.
Typ magazynu | Standardowy dysk twardy, standardowy dysk SSD, dyskI SSD premium.
Dyski zarządzane | Obsługiwane.
Zaszyfrowane dyski | Obsługiwane.<br/><br/> Można wyw.<br/><br/> Zaszyfrowanych maszyn wirtualnych nie można odzyskać na poziomie pliku/folderu. Należy odzyskać całą maszynę wirtualną.<br/><br/> Można włączyć szyfrowanie na maszynach wirtualnych, które są już chronione przez usługę Azure Backup.
Dyski z włączonym akceleratorem zapisu | Bez pomocy technicznej.<br/><br/> Kopia zapasowa platformy Azure automatycznie wyklucza dyski z włączonym akceleratorem zapisu podczas wykonywania kopii zapasowej. Ponieważ nie są one archiwizowane, nie będzie można przywrócić tych dysków z punktów odzyskiwania maszyny Wirtualnej.
Kopię zapasową & przywracanie deduplikowanych maszyn wirtualnych/dysków | Usługa Azure Backup nie obsługuje deduplikacji. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  — usługa Azure Backup nie deduplikuje między maszynami wirtualnymi w magazynie usług odzyskiwania <br/> <br/>  - Jeśli istnieją maszyny wirtualne w stanie deduplikacji podczas przywracania, pliki nie można przywrócić, ponieważ przechowalnia nie rozumie formatu. Jednak będzie można pomyślnie wykonać pełne przywracanie maszyny Wirtualnej.
Dodawanie dysku do chronionej maszyny Wirtualnej | Obsługiwane.
Ponowne rozmiary dysku na chronionej maszynie Wirtualnej | Obsługiwane.
Magazyn udostępniony| Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu udostępnionego woluminu klastra (CSV) lub skalowalny w poziomie serwera plików nie jest obsługiwane. Autorzy csv mogą zakończyć się niepowodzeniem podczas tworzenia kopii zapasowej. Podczas przywracania dyski zawierające woluminy CSV mogą nie zostać utworzone.
[Dyski udostępnione](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Bez pomocy technicznej.

## <a name="vm-network-support"></a>Obsługa sieci maszyn wirtualnych

**Składnik** | **Pomoc techniczna**
--- | ---
Liczba interfejsów sieciowych (KART SIECIOWYCH) | Maksymalnie maksymalna liczba kart sieciowych obsługiwanych dla określonego rozmiaru maszyny wirtualnej platformy Azure.<br/><br/> Karty sieciowe są tworzone podczas tworzenia maszyny wirtualnej podczas procesu przywracania.<br/><br/> Liczba kart sieciowych na przywróconej maszynie wirtualnej odzwierciedla liczbę kart sieciowych na maszynie Wirtualnej po włączeniu ochrony. Usunięcie kart sieciowych po włączeniu ochrony nie wpływa na liczbę.
Zewnętrzny/wewnętrzny moduł równoważenia obciążenia |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych ze specjalnymi ustawieniami sieciowymi.
Wiele zastrzeżonych adresów IP |Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych ze specjalnymi ustawieniami sieciowymi.
Maszyny wirtualne z wieloma kartami sieciowymi| Obsługiwane. <br/><br/> [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o przywracaniu maszyn wirtualnych ze specjalnymi ustawieniami sieciowymi.
Maszyny wirtualne z publicznymi adresami IP| Obsługiwane.<br/><br/> Skojarz istniejący publiczny adres IP z kartą sieciową lub utwórz adres i skojarz go z kartą sieciową po zakończeniu przywracania.
Sieciowa grupa zabezpieczeń (NSG) w sieciowej/podsieci. |Obsługiwane.
Statyczny adres IP | Bez pomocy technicznej.<br/><br/> Nowej maszynie wirtualnej utworzonej z punktu przywracania jest przypisywany dynamiczny adres IP.<br/><br/> W przypadku klasycznych maszyn wirtualnych nie można wyw.
Dynamiczny adres IP |Obsługiwane.<br/><br/> Jeśli karta sieciowa na źródłowej maszynie wirtualnej używa dynamicznego adresowania IP, domyślnie karta sieciowa na przywróconej maszynie wirtualnej będzie jej używać.
Azure Traffic Manager| Obsługiwane.<br/><br/>Jeśli kopia zapasowa maszyny Wirtualnej znajduje się w usłudze Traffic Manager, ręcznie dodaj przywróconą maszynę wirtualną do tego samego wystąpienia programu Traffic Manager.
System DNS platformy Azure |Obsługiwane.
Niestandardowe DNS |Obsługiwane.
Łączność wychodząca za pośrednictwem serwera proxy HTTP | Obsługiwane.<br/><br/> Uwierzytelniony serwer proxy nie jest obsługiwany.
Punkty końcowe usługi dla sieci wirtualnej| Obsługiwane.<br/><br/> Ustawienia konta zapory i magazynu sieci wirtualnej powinny zezwalać na dostęp ze wszystkich sieci.

## <a name="vm-security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania maszyn wirtualnych

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych magazynowanych:

Ruch sieciowy do platformy Azure:

- Ruch kopii zapasowych z serwerów do magazynu usług odzyskiwania jest szyfrowany przy użyciu standardu zaawansowanego szyfrowania 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- W magazynie usługi Recovery Services dane kopii zapasowych są przechowywane w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

  > [!WARNING]
  > Po skonfigurowaniu przechowalni tylko ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

Bezpieczeństwo danych:

- Podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *na* maszynie wirtualnej.
- Usługa Azure Backup obsługuje szyfrowanie dysków platformy Azure, które używa funkcji BitLocker na maszynach wirtualnych systemu Windows i nas **dm-crypt** na maszynach wirtualnych systemu Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md), które chroni dane magazynowane.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | ![Tak][green] | ![Tak][green]
Maszyny wirtualne platformy Azure | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="vm-compression-support"></a>Obsługa kompresji maszyn wirtualnych

Kopia zapasowa obsługuje kompresję ruchu kopii zapasowej, jak podsumowano w poniższej tabeli. Pamiętaj o następujących kwestiach:

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny Wirtualnej odczytuje dane bezpośrednio z konta magazynu platformy Azure za pośrednictwem sieci magazynu. Nie jest konieczne kompresowanie tego ruchu.
- Jeśli używasz programu DPM lub MABS, możesz zaoszczędzić przepustowość, ściskając dane przed utworzeniem kopii zapasowej w programach DPM/MABS.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresowanie do przechowalni (HTTPS)**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | Nie dotyczy | ![Tak][green]
Maszyny wirtualne platformy Azure | Nie dotyczy | Nie dotyczy
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Tak][green] | ![Tak][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Tak][green] | ![Tak][green]

## <a name="next-steps"></a>Następne kroki

- [19.00.000.](backup-azure-arm-vms-prepare.md)
- [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
- [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
- [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
