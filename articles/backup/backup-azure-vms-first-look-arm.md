---
title: Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu ustawień maszyny Wirtualnej przy użyciu usługi Azure Backup
description: Dowiedz się, jak utworzyć kopię zapasową maszyny Wirtualnej platformy Azure przy użyciu usługi Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 2fe786d90612feff312983dbd25dc6d691be6e70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318825"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu ustawień maszyny Wirtualnej

W tym artykule wyjaśniono, jak utworzyć kopię zapasową maszyn wirtualnych platformy Azure za pomocą [kopia zapasowa Azure](backup-overview.md) usługi. Może tworzyć kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu kilku metod:

- Pojedyncza maszyna wirtualna platformy Azure: Instrukcje w tym artykule opisano sposób tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure bezpośrednio z poziomu ustawień maszyny Wirtualnej.
- Wiele maszyn wirtualnych platformy Azure: Można skonfigurować magazyn usługi Recovery Services i skonfiguruj kopię zapasową wielu maszyn wirtualnych platformy Azure. Postępuj zgodnie z instrukcjami w [w tym artykule](backup-azure-arm-vms-prepare.md) dla tego scenariusza.

 

## <a name="before-you-start"></a>Przed rozpoczęciem

1. [Dowiedz się,](backup-architecture.md#how-does-azure-backup-work) działania kopii zapasowej i [Sprawdź](backup-support-matrix.md#azure-vm-backup-support) wymagań. 
2. [Zapoznaj się z omówieniem](backup-azure-vms-introduction.md) kopii zapasowej maszyny Wirtualnej platformy Azure.

### <a name="azure-vm-agent-installation"></a>Instalacja agenta programu Azure VM

Aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure, usługi Azure Backup instaluje rozszerzenie na agenta maszyny Wirtualnej na maszynie. Jeśli maszyna wirtualna została utworzona z obrazu w witrynie Azure marketplace, agent będzie działać. W niektórych przypadkach, na przykład jeśli tworzysz niestandardową maszynę Wirtualną lub migrowania maszyny ze środowiska lokalnego. konieczne może być ręczna instalacja agenta. 

- Jeśli trzeba ręcznie zainstalować agenta maszyny Wirtualnej, postępuj zgodnie z instrukcjami dotyczącymi [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) maszyn wirtualnych. 
- Po zainstalowaniu agenta, po włączeniu kopii zapasowej, usługi Azure Backup instaluje rozszerzenie kopii zapasowej do agenta. Ona aktualizacje i poprawki rozszerzenia bez interwencji użytkownika.

## <a name="back-up-from-azure-vm-settings"></a>Tworzenie kopii zapasowych w ustawieniach maszyny Wirtualnej platformy Azure


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **wszystkich usług** i w polu filtru wpisz **maszyn wirtualnych**, a następnie kliknij przycisk **maszyn wirtualnych**. 
3. Z listy maszyn wirtualnych wybierz maszynę Wirtualną, aby utworzyć kopię zapasową.
4. W menu maszyny Wirtualnej kliknij **kopii zapasowej**. 
5. W **magazyn usługi Recovery Services**, wykonaj następujące czynności:
   - Jeśli masz już magazyn, kliknij przycisk **wybierz istniejący**i wybierz magazyn.
   - Jeśli nie masz magazynu, kliknij przycisk **Utwórz nową**. Określ nazwę dla magazynu. Jest on tworzony w tym samym regionie i grupie zasobów co maszyna wirtualna. Nie można zmodyfikować te ustawienia, po włączeniu kopii zapasowej bezpośrednio z poziomu ustawień maszyny Wirtualnej.

   ![Kreator włączania kopii zapasowej](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. W **wybierz zasady tworzenia kopii zapasowej**, wykonaj następujące czynności:

   - Pozostaw domyślne zasady. Tworzy kopię zapasową maszyny Wirtualnej raz dziennie o godzinie określonej i przechowuje kopie zapasowe w magazynie przez 30 dni.
   - Wybierz istniejące zasady tworzenia kopii zapasowej, jeśli nie masz.
   - Utwórz nowe zasady i zdefiniuj ustawienia zasad.  

   ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Kliknij przycisk **Włącz wykonywanie kopii zapasowej**. Skojarzenie zasad tworzenia kopii zapasowej z maszyną Wirtualną. 

    ![Przycisk Włącz wykonywanie kopii zapasowej](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Możesz śledzić postęp konfiguracji w powiadomieniach portalu.
9. Po ukończeniu zadania, w menu maszyny Wirtualnej, kliknij przycisk **kopii zapasowej**. Na stronie wyświetlane stan kopii zapasowej dla maszyny Wirtualnej, informacje dotyczące punktów odzyskiwania, zadania uruchomione i alerty wydane.

   ![Stan usługi Backup](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po włączeniu kopii zapasowej uruchamia tworzenie początkowej kopii zapasowej. Możesz natychmiast rozpocząć tworzenie początkowej kopii zapasowej lub poczekaj, aż zacznie zgodnie z harmonogramem tworzenia kopii zapasowej.
    - Dopiero po zakończeniu tworzenia początkowej kopii zapasowej, **stan ostatniej kopii zapasowej** jest wyświetlany jako **ostrzeżenie (początkowa kopia zapasowa oczekuje)**.
    - Aby wyświetlić podczas następnej zaplanowanej kopii zapasowej zostanie uruchomiony, kliknij nazwę zasad tworzenia kopii zapasowej.
    
   

> [!NOTE]
> Usługa Azure Backup tworzy oddzielnej grupy zasobów (innej niż grupa zasobów maszyny Wirtualnej) do przechowywania punktów przywracania, przy użyciu formatu nazewnictwa **AzureBackupRG_geography_number** (przykład: AzureBackupRG_northeurope_1). Nie należy zablokować tę grupę zasobów.



## <a name="run-a-backup-immediately"></a>Natychmiast wykonać kopię zapasową 

1. Aby natychmiast wykonać kopię zapasową w menu maszyny Wirtualnej, kliknij przycisk **kopii zapasowej** > **Utwórz teraz kopię zapasową**.

    ![Uruchom kopię zapasową](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. W **Utwórz teraz kopię zapasową** wybrać podczas zachowywania punktu odzyskiwania przy użyciu kontrolki kalendarza > i **OK**.
  
    ![Dni przechowywania kopii zapasowych](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Portal powiadomienia informują o tym, że zostało wyzwolone zadanie tworzenia kopii zapasowej. Aby monitorować postęp tworzenia kopii zapasowej, kliknij przycisk **Wyświetl wszystkie zadania**.




## <a name="back-up-from-the-recovery-services-vault"></a>Tworzenie kopii zapasowych z magazynu usługi Recovery Services

Postępuj zgodnie z instrukcjami w tym artykule włączyć tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w konfigurowaniu magazyn usługi Azure Backup Recovery Services, a opcja włączania kopii zapasowych w magazynie.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli masz trudności z dowolnej z procedur w tym artykule, zapoznaj się z [przewodnik rozwiązywania problemów z](backup-azure-vms-troubleshoot.md).
- [Dowiedz się więcej o](backup-azure-manage-vms.md) Zarządzanie kopii zapasowych.

