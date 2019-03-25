---
title: Często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z usługą Azure Backup
description: Odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z usługą Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sogup
ms.openlocfilehash: ef46c37fec3e5438aeb4f9309201d45365a96fdc
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402069"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowej maszyn wirtualnych platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure za pomocą [kopia zapasowa Azure](backup-introduction-to-azure-backup.md) usługi.


## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Obrazy maszyn wirtualnych, które mogą być włączone dla kopii zapasowej, po ich utworzeniu?
Podczas tworzenia maszyny Wirtualnej można włączyć kopii zapasowej dla maszyn wirtualnych uruchomionych [obsługiwane systemy operacyjne](backup-support-matrix-iaas.md#supported-backup-actions)
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Jest koszt kopii zapasowej wliczane w koszt maszyny Wirtualnej? 

Nie. Koszty tworzenia kopii zapasowej są niezależne od koszty maszyn wirtualnych. Dowiedz się więcej o [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Jakie uprawnienia są wymagane do włączenia kopii zapasowej dla maszyny Wirtualnej? 

Jeśli jesteś współautorem maszyny Wirtualnej, aby umożliwić kopii zapasowej na maszynie Wirtualnej. Jeśli używasz roli niestandardowej potrzebne są następujące uprawnienia w celu włączenia kopii zapasowej na maszynie Wirtualnej: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Jeśli maszyn wirtualnych i magazynu usługi Recovery Services różnych grupach zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów dla magazynu usługi Recovery Services.  


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Jakie maszyny wirtualne platformy Azure można można tworzyć kopie zapasowe przy użyciu usługi Azure Backup?

Przegląd [macierz obsługi](backup-support-matrix-iaas.md) ograniczenia i szczegóły pomocy technicznej.

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Zadanie tworzenia kopii zapasowej na żądanie używa ten sam harmonogram przechowywania co zaplanowane kopie zapasowe?
Nie. Określ zakres przechowywania dla zadania tworzenia kopii zapasowej na żądanie. Domyślnie jest przechowywane przez 30 dni po wyzwoleniu z portalu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ostatnio na niektórych maszynach wirtualnych została włączona usługa Azure Disk Encryption. Czy operacje tworzenia kopii zapasowych będą nadal działać?
Udostępnić uprawnienia dla usługi Azure Backup na dostęp do usługi Key Vault. Określ uprawnienia w programie PowerShell, zgodnie z opisem w **Włącz kopię zapasową** sekcji [kopii zapasowej programu Azure PowerShell](backup-azure-vms-automation.md) dokumentacji.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Dyski maszyny Wirtualnej zostały zmigrowane do dysków zarządzanych. Czy operacje tworzenia kopii zapasowych będą nadal działać?
Tak, kopie zapasowe działają bezproblemowo. Nie ma potrzeby ponowne skonfigurowanie niczego.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Dlaczego nie widzę swojej maszyny Wirtualnej w Kreatorze konfigurowania kopii zapasowej?
Kreator wyszczególnia tylko maszyny wirtualne w tym samym regionie co magazyn, a które nie są już trwa wykonywanie kopii zapasowej.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Moja maszyna wirtualna jest zamknięta. Będą na żądanie lub zaplanowanych zadań tworzenia kopii zapasowej?
Tak. Wykonywane kopie zapasowe, gdy maszyna zostanie zamknięta. Punkt odzyskiwania jest oznaczana awarii spójne.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Czy można anulować zadania tworzenia kopii zapasowej w toku?
Tak. Możesz anulować zadanie tworzenia kopii zapasowej w **Trwa tworzenie zrzutu ekranu** stanu. Nie można anulować zadania, jeśli przesyłanie danych z migawki jest w toku.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie--azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Czy włączono blokadę dla grupy zasobów utworzonej w usłudze Kopia zapasowa Azure (tj.) ` AzureBackupRG_<geo>_<number>`), Moje kopie zapasowe będą pracować?
Jeśli zablokujesz grupy zasobów utworzonej w usłudze Azure Backup, kopie zapasowe będą kończyć się niepowodzeniem, ponieważ maksymalny limit punktów przywracania 18.

Użytkownik musi usunąć blokadę i wyczyścić kolekcję punktów przywracania z danej grupy zasobów, aby upewnić się powiedzie, wykonanie kolejnych kopii zapasowych [wykonaj następujące kroki](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) usunąć kolekcję punktów przywracania.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>Zasad tworzenia kopii zapasowej należy wziąć pod uwagę czasu letniego (DST)?
Nie. Data i godzina na komputerze lokalnym jest lokalny przy użyciu bieżącego letniego stosowane. Czas zaplanowanego tworzenia kopii zapasowych mogą się różnić od lokalnego czasu z powodu czasu letniego.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Liczba dysków z danymi można dołączyć do maszyny Wirtualnej z kopii zapasowej przez usługę Azure Backup?
Usługa Azure Backup można utworzyć kopii maszyn wirtualnych z maksymalnie 16 dysków. Obsługa 16 dysków znajduje się w [natychmiastowe Przywracanie](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Czy obsługę kopii zapasowych platformy Azure SSD zarządzanego dysku w warstwie standardowa?
Usługa Azure Backup obsługuje [SSD w warstwie standardowa usługi managed disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Dyski zarządzane dyski SSD zapewniają nowy typ magazynu trwałego maszyn wirtualnych platformy Azure. Obsługa dysków SSD zarządzane znajduje się w [natychmiastowe Przywracanie](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Możemy utworzyć kopię zapasową maszyny Wirtualnej z dyskiem z obsługą WA akcelerator zapisu?
Nie można pobrać migawek na dysku włączony (Waszyngton). Jednak usługa Azure Backup można wykluczyć dysk z obsługą WA z kopii zapasowej. Wykluczenie dysku dla maszyn wirtualnych z dyskami z obsługą WA jest obsługiwana tylko dla subskrypcji, uaktualnić do natychmiastowe przywracanie.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mam Maszynę wirtualną z dyskami akcelerator zapisu (Waszyngton) i zainstalować oprogramowanie SAP HANA. Jak wykonać kopię zapasową?
Usługa Azure Backup nie można utworzyć kopii zapasowych dysków komputerów z obsługą WA, ale można wykluczyć z kopii zapasowej. Jednak kopii zapasowej nie zawiera spójność bazy danych, ponieważ na dysku włączony (Waszyngton) nie ma kopii zapasowej. Jeśli dysk systemu operacyjnego kopii zapasowych i kopii zapasowych dysków, które nie są włączone w stanie Waszyngton, można wykonywanie kopii zapasowych dysków przy użyciu tej konfiguracji.

Prywatna wersja zapoznawcza do tworzenia kopii zapasowych oprogramowania SAP HANA jest uruchomiony przy użyciu RPO o wartości 15 minut. Jest wbudowana w sposób podobny do kopii zapasowej bazy danych SQL i korzysta z interfejsu backInt rozwiązań innych firm, certyfikowanych przez oprogramowanie SAP HANA. Jeśli chcesz, Wyślij wiadomość e-mail ` AskAzureBackupTeam@microsoft.com ` z tematem **Zarejestruj się w prywatnej wersji zapoznawczej do utworzenia kopii zapasowej danych SAP HANA na maszynach wirtualnych platformy Azure**.


## <a name="restore"></a>Przywracanie

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Jak zdecydować, czy można przywrócić tylko dysków lub pełnej maszyny Wirtualnej?
Pomyśl o przywracanie maszyny Wirtualnej, jak opcję szybkiego tworzenia maszyny wirtualnej platformy Azure. Opcja ta umożliwia zmianę nazw dysków, kontenerów używanych przez dyski, publicznych adresów IP i nazw interfejsów sieciowych. Zmiana zachowuje unikatowy zasobów po utworzeniu maszyny Wirtualnej. Maszyna wirtualna nie została dodana do zestawu dostępności.

Można użyć opcji przywracania dysków, jeśli chcesz:
  * Dostosowanie utworzonej maszyny Wirtualnej. Na przykład zmienić rozmiar.
  * Dodaj ustawienia konfiguracji, które nie były dostępne w czasie wykonywania kopii zapasowej.
  * Kontrolowanie konwencji nazewnictwa dla zasobów, które są tworzone.
  * Dodaj maszynę Wirtualną do zestawu dostępności.
  * Dodaj wszystkie ustawienia, która musi być skonfigurowana przy użyciu programu PowerShell lub w szablonie.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Tworzenie kopii zapasowych niezarządzanych dysków maszyn wirtualnych można przywrócić po uaktualnieniu do usługi managed disks?
Tak, można użyć kopii zapasowych wykonanych przed dyski zostały zmigrowane z niezarządzanych do zarządzanych.
- Domyślnie zadanie przywracania maszyny Wirtualnej powoduje utworzenie niezarządzanej maszyny Wirtualnej.
- Można jednak Przywróć dyski i używać ich do tworzenia zarządzanych maszyn wirtualnych.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak przywrócić Maszynę wirtualną do punktu przywracania, zanim maszyna wirtualna została zmigrowana do usługi managed disks?
Domyślnie zadanie przywracania maszyn wirtualnych tworzy Maszynę wirtualną z dyskami niezarządzanymi. Aby utworzyć Maszynę wirtualną z dyskami zarządzanymi:
1. [Przywracanie do dysków niezarządzanych](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Konwertuj przywróconych dysków do usługi managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Tworzenie maszyny Wirtualnej z dyskami zarządzanymi](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Dowiedz się więcej](backup-azure-vms-automation.md#restore-an-azure-vm) dotyczące tego w programie PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Czy można przywrócić maszynę Wirtualną, która jest usunięty?
Tak. Nawet wtedy, gdy usuniesz maszynę Wirtualną, możesz przejść do odpowiedniej kopii zapasowej elementu w magazynie i Przywróć z punktu odzyskiwania.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Jak przywrócić Maszynę wirtualną do tego samego zbioru dostępności?
Zarządzane dysku maszyny wirtualnej platformy Azure Przywracanie do zestawów dostępności jest włączona, podając opcję w szablonie podczas przywracania jako dysków zarządzanych. Ten szablon zawiera parametr wejściowy o nazwie **zestawy dostępności**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak uzyskać szybsze wykonań przywracania?
Aby zwiększyć wydajność przywracania, firma Microsoft przechodzenia do [natychmiastowe Przywracanie](backup-instant-restore-capability.md) możliwości.

## <a name="manage-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co się stanie, jeśli zmodyfikuję zasad tworzenia kopii zapasowej?
Maszyna wirtualna jest wykonywana kopia zapasowa przy użyciu ustawień harmonogram i okres przechowywania w zmodyfikowany lub nowych zasad.

- Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania są oznaczone i przechowywane zgodnie z nowych zasad.
- W przypadku przechowywania punktów odzyskiwania są oznaczone do oczyszczenia w ramach następnego zadania oczyszczania i usuwane.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Jak przenieść kopii zapasowej przez usługę Azure Backup do innej grupy zasobów maszyny Wirtualnej?

1. Tymczasowo Zatrzymaj tworzenie kopii zapasowej i Zachowaj dane kopii zapasowej.
2. Przenieś maszynę Wirtualną do docelowej grupy zasobów.
3. Wykonywanie kopii zapasowej ponownie włączone w tej samej lub nowego magazynu.

Możesz przywrócić maszynę Wirtualną z dostępnych punktów przywracania utworzonych przed wykonaniem operacji przenoszenia.
