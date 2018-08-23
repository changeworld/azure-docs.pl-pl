---
title: Kopia zapasowa maszyny Wirtualnej platformy Azure — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące działania funkcji tworzenia kopii zapasowej maszyny wirtualnej platformy Azure, ograniczeń i konsekwencji zmian zasad
services: backup
author: trinadhk
manager: shreeshd
keywords: azure vm backup, azure vm restore, backup policy
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 58b0622da2ef617e652c8bb9dacbf7daa2d79966
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054393"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Pytania dotyczące usługi tworzenia kopii zapasowej maszyny wirtualnej platformy Azure
W tym artykule znajdują się odpowiedzi na często zadawane pytania pomagające w szybkim poznaniu składników usługi tworzenia kopii zapasowej maszyny wirtualnej platformy Azure. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Pytania dotyczące usługi Azure Backup można również zadawać na [forum dyskusyjnym](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Czy magazyny usługi Recovery Services obsługują klasyczne maszyny wirtualne, czy maszyny wirtualne oparte na usłudze Resource Manager? <br/>
Magazyny usługi Recovery Services obsługują oba modele.  Kopię zapasową można wykonywać klasycznej maszyny Wirtualnej lub maszyny Wirtualnej usługi Resource Manager do magazynu usługi Recovery Services.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Jakie konfiguracje nie są obsługiwane przez kopię zapasową maszyny Wirtualnej platformy Azure?
Zapoznaj się z artykułem [obsługiwane systemy operacyjne](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) i [kopii zapasowych ograniczenia maszyn wirtualnych](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Dlaczego nie widzę swojej maszyny wirtualnej w kreatorze konfigurowania kopii zapasowych?
W Kreatorze konfigurowania kopii zapasowych kopia zapasowa Azure znajduje się tylko maszyny wirtualne, które są:
  * Nie jest już chroniony można sprawdzić stan kopii zapasowej maszyny wirtualnej, przechodząc do bloku maszyny Wirtualnej i sprawdzając stan kopia zapasowa w Menu ustawienia. Dowiedz się więcej na temat [sprawdzania stanu tworzenia kopii zapasowych maszyny wirtualnej](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu)
  * Przynależność do tego samego regionu co maszyna wirtualna

## <a name="backup"></a>Tworzenie kopii zapasowych
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Czy dla zadań tworzenia kopii zapasowej na żądanie będzie stosowany ten sam harmonogram przechowywania co dla zaplanowanych zadań tworzenia kopii zapasowej?
Nie. Należy określić zakres przechowywania dla zadania tworzenia kopii zapasowej na żądanie. Domyślnie jest przechowywane przez 30 dni po wyzwoleniu z portalu. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ostatnio na niektórych maszynach wirtualnych została włączona usługa Azure Disk Encryption. Czy operacje tworzenia kopii zapasowych będą nadal działać?
Musisz nadać usłudze Azure Backup uprawnienia umożliwiające jej uzyskiwanie dostępu do usługi Key Vault. Te uprawnienia możesz podać w programie PowerShell przy użyciu czynności wymienionych w sekcji *Włączenie kopii zapasowej* dokumentacji programu [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Dyski maszyny wirtualnej zostały zmigrowane do dysków zarządzanych. Czy operacje tworzenia kopii zapasowych będą nadal działać?
Tak, kopie zapasowe działają bezproblemowo oraz nie konieczności konfigurowania kopii zapasowej. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Moja maszyna wirtualna jest zamknięta. Będą na żądanie lub zaplanowanych zadań tworzenia kopii zapasowej?
Tak. Nawet w przypadku, gdy maszyna zostanie zamknięta kopie zapasowe działają, a punkt odzyskiwania jest oznaczony jako awarii spójne. Aby uzyskać więcej informacji, zobacz sekcję spójności danych w [w tym artykule](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Czy można anulować zadania tworzenia kopii zapasowej w toku?
Tak. Możesz anulować zadanie tworzenia kopii zapasowej, jeśli jest w fazie "Utworzenia migawki". **Nie można anulować zadania, jeśli przesyłanie danych z migawki jest w toku**. 

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Zablokuj grupy zasobów I włączone na Moje kopie zapasowe maszyn wirtualnych dysku zarządzanego. Czy operacje tworzenia kopii zapasowych będą nadal działać?
Użytkownik zablokuje grupy zasobów, usługi Kopia zapasowa nie jest można usunąć starsze punkty przywracania. Ze względu na to nowych kopii zapasowych będzie kończyć się niepowodzeniem, ponieważ istnieje limit maksymalnego 18 punktów przywracania narzucone z wewnętrznej bazy danych. Jeśli tworzenie kopii zapasowych zakończą się niepowodzeniem z powodu błędu wewnętrznego po blokady RG, postępuj zgodnie z tymi [kolekcję punktów kroki, aby usunąć przywracania](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>Zasady tworzenia kopii zapasowych podąża czasu letniego Time(DST) zapisywanie do konta?
Nie. Należy pamiętać, że data i godzina na komputerze lokalnym jest wyświetlana w formacie czasu lokalnego, jak i z uwzględnieniem bieżącego czasu. Dlatego skonfigurowany czas zaplanowanego tworzenia kopii zapasowych może różnić się od czasu lokalnego z powodu czasu letniego.

## <a name="restore"></a>Przywracanie
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Jak wybrać między przywróceniem dysków a pełnym przywróceniem maszyny wirtualnej?
Azure pełne przywracanie maszyny Wirtualnej można traktować jak opcję szybkiego tworzenia. Przywracanie maszyny Wirtualnej opcja zmiany nazw dysków, kontenerów używanych przez te dyski, publicznych adresów IP i nazw interfejsów sieciowych. Zmiana jest wymagane do obsługi zapewnić unikatowość zasobów utworzonych podczas tworzenia maszyny Wirtualnej. Ale nie spowoduje dodanie maszyny Wirtualnej do zestawu dostępności. 

Opcji przywracania dysków można używać w następujących celach:
  * Dostosowywanie maszyny Wirtualnej, która zostanie utworzona od punktu w czasie konfiguracji takich jak zmiana rozmiaru
  * Dodawanie konfiguracji, które nie są obecne w momencie tworzenia kopii zapasowej 
  * Kontrolowanie konwencji nazewnictwa tworzonych zasobów
  * Dodawanie maszyny wirtualnej do zestawu dostępności
  * Dla innych konfiguracji, którą można uzyskać tylko przy użyciu programu PowerShell/definicji deklaracyjnego szablonu
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Kopie zapasowe niezarządzanego dysku maszyny Wirtualnej mogą służyć do przywrócenia po uaktualnieniu do usługi managed disks Moje dyski?
Tak, można użyć kopie zapasowe wykonane przed migrowaniem dysków z niezarządzane do zarządzanego. Domyślnie zadania maszyny Wirtualnej przywracania utworzy Maszynę wirtualną z dyskami niezarządzanymi. Przywróć dyski funkcji umożliwia przywracanie dysków i używanie ich do tworzenia maszyny Wirtualnej na dyskach zarządzanych. 

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Co to jest procedurą, aby przywrócić Maszynę wirtualną do punktu przywracania przed przeprowadzono konwersję z niezarządzanych do dysków zarządzanych dla maszyny Wirtualnej?
W tym scenariuszu domyślnie przywracania maszyny Wirtualnej zadania spowoduje utworzenie maszyny Wirtualnej z dyskami niezarządzanymi. Aby utworzyć Maszynę wirtualną z dyskami zarządzanymi:
1. [Przywracanie do dysków niezarządzanych](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Konwertuj przywróconych dysków do usługi managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Tworzenie maszyny Wirtualnej z dyskami zarządzanymi](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
W przypadku poleceń cmdlet programu Powershell można znaleźć [tutaj](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Czy można przywrócić maszynę Wirtualną, jeśli Moja maszyna wirtualna zostanie usunięta?
Tak. Cykl życia maszyn wirtualnych i jego odpowiedniego elementu kopii zapasowej są różne. Dlatego nawet wtedy, gdy usuniesz maszynę Wirtualną, możesz wykorzystać odpowiadającego tworzenia kopii zapasowej elementu w magazynie usługi Recovery Services i wyzwalanie przywracania, przy użyciu jednego z punktów odzyskiwania. 

## <a name="manage-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Co się stanie po zmianie zasad kopii zapasowych na maszynach wirtualnych?
Po zastosowaniu nowych zasad na maszynach wirtualnych, harmonogram i okres przechowywania nowych zasad jest zakończony. Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte. 

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Jak mogę przenieść Maszynę wirtualną zarejestrowane w usłudze Azure backup między grupami zasobów?
Wykonaj poniższe kroki, aby pomyślnie przenieść maszynę Wirtualną z kopii zapasowej do docelowej grupy zasobów 
1. Tymczasowo Zatrzymaj kopię zapasową i Zachowaj dane kopii zapasowej
2. Przenieś maszynę Wirtualną do docelowej grupy zasobów
3. Włącz ponownie ochronę za pomocą tego samego/nowy magazyn

Użytkownicy mogą przywracać z dostępnych punktów przywracania utworzonych przed operacji przenoszenia.


