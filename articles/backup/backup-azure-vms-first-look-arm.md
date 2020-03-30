---
title: Dzmówięknienie maszyn wirtualnych platformy Azure z ustawień maszyny Wirtualnej
description: W tym artykule dowiesz się, jak wykonać kopię zapasową pojedynczej maszyny wirtualnej platformy Azure lub wielu maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705449"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Dzmówięknienie maszyn wirtualnych platformy Azure z ustawień maszyny Wirtualnej

W tym artykule wyjaśniono, jak wykonać kopię zapasową maszyn wirtualnych platformy Azure za pomocą usługi [Azure Backup.](backup-overview.md) Można wyw kopii zapasowej maszyn wirtualnych platformy Azure przy użyciu kilku metod:

- Single Azure VM: Instrukcje w tym artykule opisują sposób utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure bezpośrednio z ustawień maszyny Wirtualnej.
- Wiele maszyn wirtualnych platformy Azure: można skonfigurować magazyn usług odzyskiwania i skonfigurować kopię zapasową dla wielu maszyn wirtualnych platformy Azure. Postępuj zgodnie z instrukcjami w [tym artykule](backup-azure-arm-vms-prepare.md) dla tego scenariusza.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. [Dowiedz się,](backup-architecture.md#how-does-azure-backup-work) jak działa kopia zapasowa, i [sprawdź](backup-support-matrix.md#azure-vm-backup-support) wymagania dotyczące pomocy technicznej.
2. [Omówienie](backup-azure-vms-introduction.md) kopii zapasowej maszyny wirtualnej platformy Azure.

### <a name="azure-vm-agent-installation"></a>Instalacja agenta maszyny wirtualnej platformy Azure

Aby wykonać kopię zapasową maszyn wirtualnych platformy Azure, usługa Azure Backup instaluje rozszerzenie na agencie maszyny Wirtualnej uruchomionym na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure marketplace, agent będzie uruchomiony. W niektórych przypadkach, na przykład w przypadku utworzenia niestandardowej maszyny Wirtualnej lub migracji komputera z lokalnego. może być konieczne zainstalowanie agenta ręcznie.

- Jeśli musisz ręcznie zainstalować agenta maszyny Wirtualnej, postępuj zgodnie z instrukcjami dotyczącymi maszyn wirtualnych z [systemem Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux.](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
- Po zainstalowaniu agenta po włączeniu kopii zapasowej usługa Azure Backup instaluje rozszerzenie kopii zapasowej agentowi. Aktualizuje i poprawia rozszerzenie bez interwencji użytkownika.

## <a name="back-up-from-azure-vm-settings"></a>Parcie zapasowe z ustawień maszyny Wirtualnej platformy Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Kliknij **pozycję Wszystkie usługi** i w filtrze wpisz polecenie Maszyny **wirtualne**, a następnie kliknij pozycję **Maszyny wirtualne**.
3. Z listy maszyn wirtualnych wybierz maszynę wirtualną, której chcesz poprzeć.
4. W menu maszyny Wirtualnej kliknij polecenie **Kopia zapasowa**.
5. W **magazynie usług odzyskiwania**wykonaj następujące czynności:
   - Jeśli masz już przechowalnię, kliknij przycisk **Zaznacz istniejący**i wybierz przechowalnię.
   - Jeśli nie masz przechowalni, kliknij przycisk **Utwórz nowy**. Określ nazwę przechowalni. Jest tworzony w tym samym regionie i grupy zasobów co maszyna wirtualna. Nie można zmodyfikować tych ustawień po włączeniu tworzenia kopii zapasowej bezpośrednio z ustawień maszyny Wirtualnej.

   ![Kreator włączania kopii zapasowej](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. W **obszarze Wybierz zasady tworzenia kopii zapasowych**wykonaj następujące czynności:

   - Pozostaw domyślną zasadę. Spowoduje to utworzenie kopii zapasowej maszyny Wirtualnej raz dziennie o określonej godzinie i zachowuje kopie zapasowe w magazynie przez 30 dni.
   - Wybierz istniejącą zasadę tworzenia kopii zapasowych, jeśli jej masz.
   - Utwórz nową zasadę i zdefiniuj ustawienia zasad.  

   ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Kliknij **pozycję Włącz tworzenie kopii zapasowej**. Spowoduje to skojarzenie zasad tworzenia kopii zapasowych z maszyną wirtualną.

    ![Przycisk Włącz wykonywanie kopii zapasowej](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Postęp konfiguracji można śledzić w powiadomieniach portalu.
9. Po zakończeniu zadania w menu maszyny Wirtualnej kliknij polecenie **Kopia zapasowa**. Strona zawiera stan kopii zapasowej maszyny Wirtualnej, informacje o punktach odzyskiwania, uruchomionych zadaniach i wystawionych alertach.

   ![Stan kopii zapasowej](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po włączeniu kopii zapasowej uruchamia się początkowa kopia zapasowa. Początkową kopię zapasową można uruchomić natychmiast lub poczekać, aż rozpocznie się zgodnie z harmonogramem tworzenia kopii zapasowych.
    - Dopóki początkowa kopia zapasowa nie zostanie ukończona, **stan ostatniej kopii zapasowej** jest wyświetlany jako ostrzeżenie **(początkowa kopia zapasowa oczekująca)**.
    - Aby zobaczyć, kiedy zostanie uruchomiony następny zaplanowana kopia zapasowa, kliknij nazwę zasad kopii zapasowej.

## <a name="run-a-backup-immediately"></a>Natychmiastowe uruchamianie kopii zapasowej

1. Aby natychmiast uruchomić kopię zapasową, w menu maszyny Wirtualnej kliknij polecenie **Kopia zapasowa kopia zapasowa** > **teraz**.

    ![Uruchamianie kopii zapasowej](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. W **obszarze Kopia zapasowa teraz** użyj formantu kalendarza, aby wybrać, aż punkt odzyskiwania zostanie zachowany > i **OK**.

    ![Dzień przechowywania kopii zapasowej](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Powiadomienia portalu informujące o wyzwoleniu zadania tworzenia kopii zapasowej. Aby monitorować postęp tworzenia kopii zapasowej, kliknij pozycję **Wyświetl wszystkie zadania**.

## <a name="back-up-from-the-recovery-services-vault"></a>Generdyzuje zapasy z magazynu usług odzyskiwania

Postępuj zgodnie z instrukcjami w tym artykule, aby włączyć tworzenie kopii zapasowych dla maszyn wirtualnych platformy Azure, konfigurując magazyn usług Azure Backup Recovery Services i włączając tworzenie kopii zapasowych w magazynie.

>[!NOTE]
> **Usługa Azure Backup obsługuje teraz selektywną kopię zapasową dysku i przywracanie przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.**
>
>Obecnie usługa Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (system operacyjny i dane) na maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej. Dzięki funkcji wykluczeń dysku można uzyskać opcję tworzenia kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie Wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb związanych z tworzeniem kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzbioru dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i z magazynu.
>
>**Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Następne kroki

- Jeśli masz trudności z którąkolwiek z procedur w tym artykule, zapoznaj się z [przewodnikiem rozwiązywania problemów](backup-azure-vms-troubleshoot.md).
- [Dowiedz się więcej o](backup-azure-manage-vms.md) zarządzaniu kopiami zapasowymi.
