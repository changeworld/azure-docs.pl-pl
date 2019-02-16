---
title: Usuwanie magazynu usługi Recovery Services na platformie Azure "
description: W tym artykule wyjaśniono, jak usunąć magazyn usługi Recovery Services. Artykuł zawiera kroki rozwiązywania problemów, podczas próby usunięcia magazynu, ale nie.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d15e3773a9b6e3dceb0799d206070730675c211d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310970"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

W tym artykule opisano, jak można usunąć wszystkich elementów z magazynu usługi Recovery Services, a następnie usuń ją. Nie można usunąć magazynu usługi Recovery Services, jeśli jest zarejestrowany na serwerze i przechowuje dane kopii zapasowej. Jeśli spróbuj usunąć magazynu, ale nie magazyn jest nadal skonfigurować do odbierania danych kopii zapasowej.

Aby dowiedzieć się, jak usunąć magazyn, zobacz sekcję [usuwanie magazynu w witrynie Azure portal](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Jeśli nie chcesz przechowywać żadnych danych w magazynie usługi Recovery Services i zechcesz usunąć magazynu, zobacz sekcję [usunąć magazyn przy wymuszonego](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Jeśli nie masz pewności, co znajduje się w magazynie, a następnie należy upewnić się, że można usunąć magazynu, zobacz sekcję [Usuń magazyn zależności i usuwania magazynu](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Usuwanie magazynu w witrynie Azure portal

Jeśli masz już magazyn usługi Recovery Services, Otwórz, przejdź do kroku drugim.

1. Otwórz witrynę Azure portal, a na pulpicie nawigacyjnym Otwórz magazyn, który chcesz usunąć.

   Jeśli nie masz magazyn usługi Recovery Services przypięte do pulpitu nawigacyjnego, w menu Centrum kliknij pozycję **wszystkich usług** i na liście zasobów wpisz **usługi Recovery Services**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Aby wyświetlić listę magazynów w ramach subskrypcji, kliknij **Magazyny usługi Recovery Services**.

   ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Zostanie wyświetlona lista magazynów usługi Recovery Services.

   ![Wybierz magazyn z listy](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. Z listy wybierz magazyn, który chcesz usunąć. Po wybraniu magazynu, zostanie otwarty pulpit nawigacyjny jego magazynu.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Aby usunąć magazynu, na pulpicie nawigacyjnym magazynu kliknij pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie usunięcia magazynu.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Jeśli **Błąd usuwania magazynu** pojawi się, możesz usunąć zależności z magazynu lub można użyć programu PowerShell, aby usunąć magazyn przy wymuszonego. W poniższych sekcjach opisano sposób wykonywania tych zadań.

    ![Błąd usuwania magazynu](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Usuwanie magazynu usługi Recovery Services przy wymuszonego

Aby usunąć magazyn usługi Recovery Services, przy wymuszonego, można użyć programu PowerShell. Przy wymuszonego oznacza, że magazyn usługi Recovery Services, a wszystkie skojarzone dane kopii zapasowej, zostaną trwale usunięte.

> [!Warning]
> Jeśli przy użyciu programu PowerShell, aby usunąć magazyn usługi Recovery Services, należy się upewnić, że chcesz trwale usunąć wszystkie dane kopii zapasowej w magazynie.
>

Aby usunąć magazyn usługi Recovery Services:

1. Zaloguj się do swojego konta platformy Azure.

   Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
    Connect-AzureRmAccount
   ```
   Przy pierwszym użyciu usługi Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji za pomocą polecenia [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Otwórz okno programu PowerShell z uprawnieniami administratora.

1. Użyj `Set-ExecutionPolicy Unrestricted` do usunięcia wszelkich ograniczeń.

1. Uruchom następujące polecenie, aby pobrać pakiet klienta usługi Resource Manager platformy Azure z chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. Użyj następującego polecenia, aby zainstalować klienta interfejsu API usługi Azure Resource Manager.

   `choco.exe install armclient`

1. W witrynie Azure portal Zbierz identyfikator subskrypcji i nazwę grupy skojarzonego zasobu dla magazynu usługi Recovery Services, które chcesz usunąć.

1. W programie PowerShell uruchom następujące polecenie, korzystając z Identyfikatora subskrypcji, nazwę grupy zasobów i nazwę magazynu usługi Recovery Services. Po uruchomieniu polecenia, usunięcie magazynu i wszystkich zależności.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   Magazyn może być pusty, aby można było usunąć. W przeciwnym razie wystąpi błąd zamieszczenie "Nie można usunąć magazynu, ponieważ istnieją zasoby w tym magazynie". Następujące polecenie pokazuje, jak usunąć kontener w ramach magazynu:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

1. Zaloguj się do Twojej subskrypcji w witrynie Azure portal i sprawdź, czy magazyn zostanie usunięty.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Usuń zależności magazynu i magazynu

Aby ręcznie usunąć zależności magazynu, należy usunąć konfigurację między każdego elementu lub serwera i magazyn usługi Recovery Services. Podczas wykonywania kroków poniższej procedury, należy użyć **elementy kopii zapasowej** menu (zobacz obraz) dla:

* Kopii zapasowych usługi Azure Storage (Azure Files)
* SQL Server podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure
* Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Użyj **infrastruktura zapasowa** menu (zobacz obraz) dla:

* Kopii zapasowych usługi Azure Backup Server
* Tworzenie kopii zapasowych programu System Center DPM

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. W menu pulpitu nawigacyjnego magazynu, przewiń w dół do sekcji chronionych elementów, a następnie kliknij przycisk **elementy kopii zapasowej**. W tym menu można zatrzymać i usunąć serwery plików platformy Azure, programów SQL Server na maszynie Wirtualnej platformy Azure i maszyn wirtualnych platformy Azure. W tym przykładzie usuniemy dane kopii zapasowej z serwera plików platformy Azure.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Wybierz typ kopii zapasowej, aby wyświetlić wszystkie elementy tego typu.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. Dla wszystkich elementów na liście, kliknij prawym przyciskiem myszy element i wybierz z menu kontekstowego **Zatrzymaj kopię zapasową**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-item.png)

    Zostanie otwarte menu Zatrzymaj kopię zapasową.

1. Na **Zatrzymaj tworzenie kopii zapasowej** menu z **wybierz jedną z opcji** menu, wybierz opcję **Usuń dane kopii zapasowej**, wpisz nazwę elementu i kliknij przycisk **Zatrzymaj kopię zapasową**.

    Wpisz nazwę elementu, aby sprawdzić, czy chcesz go usunąć. **Zatrzymaj tworzenie kopii zapasowej** przycisk aktywuje po upewnieniu się elementu. Aby zachować dane, nie można usunąć magazyn.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Jeśli chcesz, podaj powód, dlaczego one usuwania danych, a następnie dodaj komentarze. Aby sprawdzić, czy zadanie zostało ukończone, należy sprawdzić komunikaty Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po ukończeniu zadania, usługa wysyła komunikat: *procesu tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte*.

1. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** elementów w magazynie.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienko w menu magazynu usługi Recovery Services. Nie powinny istnieć dowolne **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście. Jeśli elementy nadal pojawia się w magazynie, wróć do kroku 3, a następnie wybierz z listy Typ innego elementu.  

1. Gdy istnieje więcej elementów na pasku narzędzi magazynu, kliknij przycisk **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Aby sprawdzić, czy chcesz usunąć magazynu, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a portal powróci do **New** menu usługi.

## <a name="removing-azure-backup-server-or-dpm"></a>Usuwanie usługi Azure Backup Server lub programu DPM

1. W menu pulpitu nawigacyjnego magazynu, przewiń w dół do sekcji Zarządzanie, a następnie kliknij przycisk **infrastruktura zapasowa**.

1. W podmenu, kliknij przycisk **serwery zarządzania kopiami zapasowymi** Aby wyświetlić serwer serwerom kopia zapasowa Azure i programu System Center DPM. można zatrzymać i usunąć serwery plików platformy Azure, programów SQL Server na maszynie Wirtualnej platformy Azure i maszyn wirtualnych platformy Azure.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Kliknij prawym przyciskiem myszy element, który chcesz usunąć, a w podmenu wybierz **Usuń**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Zostanie otwarte menu Zatrzymaj kopię zapasową.

1. Na **Zatrzymaj tworzenie kopii zapasowej** menu z **wybierz jedną z opcji** menu, wybierz opcję **Usuń dane kopii zapasowej**, wpisz nazwę elementu i kliknij przycisk **Zatrzymaj kopię zapasową**.

    Aby sprawdzić, czy chcesz usunąć, wpisz jego nazwę. **Zatrzymaj tworzenie kopii zapasowej** przycisk aktywuje po upewnieniu się elementu. Aby zachować dane, nie można usunąć magazynu.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Opcjonalnie możesz podać powód, dlaczego usuwasz je i Dodaj komentarze. Aby sprawdzić, czy zadanie jest ukończone, należy sprawdzić komunikaty Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po zakończeniu zadania, usługa wysyła komunikat: proces tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte.

1. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** pozostałych elementów w magazynie.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienko w menu magazynu usługi Recovery Services. Nie powinny istnieć dowolne **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście. Jeśli elementy nadal pojawia się w magazynie, wróć do kroku 3, a następnie wybierz z listy Typ innego elementu.  
1. W przypadku Brak elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Aby sprawdzić, czy chcesz usunąć magazynu, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a portal powróci do **New** menu usługi.


## <a name="removing-azure-backup-agent-recovery-points"></a>Usuwanie punktów odzyskiwania agenta usługi Kopia zapasowa Azure

1. W menu pulpitu nawigacyjnego magazynu, przewiń w dół do sekcji Zarządzanie, a następnie kliknij przycisk **infrastruktura zapasowa**.

1. W podmenu, kliknij przycisk **serwerów chronionych** Aby wyświetlić listę chronione typu serwera, w tym agent usługi Kopia zapasowa Azure.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. W **serwerów chronionych** kliknij agenta usługi Kopia zapasowa Azure.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Zostanie otwarta lista serwerów chronionych przy użyciu agenta usługi Azure Backup.

    ![Wybierz pozycję określonego chronionego serwera](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. Na liście serwerów kliknij przycisk, aby otworzyć jego menu.

    ![Wyświetl pulpit nawigacyjny wybranego serwera](./media/backup-azure-delete-vault/selected-protected-server.png)

1. W menu pulpitu nawigacyjnego wybranego serwera, kliknij przycisk **Usuń**.

    ![Usuń wybrany serwer](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. Na **Usuń** menu, wpisz nazwę elementu, a następnie kliknij przycisk **Usuń**.

    Wpisz nazwę elementu, aby sprawdzić, czy chcesz go usunąć. **Usuń** przycisk aktywuje po upewnieniu się elementu.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Opcjonalnie możesz podać powód, dlaczego usuwasz je i Dodaj komentarze. Aby sprawdzić, czy zadanie jest ukończone, należy sprawdzić komunikaty Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po zakończeniu zadania, usługa wysyła komunikat: proces tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte.

1. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** pozostałych elementów w magazynie.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienko w menu magazynu usługi Recovery Services. Nie powinny istnieć dowolne **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście. Jeśli elementy nadal pojawia się w magazynie, wróć do kroku 3, a następnie wybierz z listy Typ innego elementu.  
1. W przypadku Brak elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Aby sprawdzić, czy chcesz usunąć magazynu, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a portal powróci do **New** menu usługi.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co zrobić, jeśli Zatrzymaj proces tworzenia kopii zapasowej, ale pozostawisz dane?

Jeśli jednak przypadkowo Zatrzymaj proces tworzenia kopii zapasowej *zachować* danych, należy usunąć dane kopii zapasowej można było usunąć magazyn. Aby usunąć dane kopii zapasowej:

1. Na **elementów kopii zapasowych** menu, kliknij prawym przyciskiem myszy element i w menu kontekstowym kliknij **Usuń dane kopii zapasowej**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **Usuń dane kopii zapasowej** zostanie otwarte menu.
1. Na **Usuń dane kopii zapasowej** menu, wpisz nazwę elementu, a następnie kliknij przycisk **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Po usunięciu danych, wróć do kroku 4c i kontynuować proces.
