---
title: Usuwanie magazynu usługi Recovery Services na platformie Azure
description: W tym artykule opisano, jak usunąć magazyn usługi Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: raynew
ms.openlocfilehash: 1cc86470b9e45469d633d47121869b3c2dc1b052
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439009"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

W tym artykule opisano sposób usuwania [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services. Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwanie magazynu i usuwanie magazynu przy wymuszonego.




## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem, ważne jest zrozumienie, że nie można usunąć magazynu usługi Recovery Services, w którym znajdują się serwery zarejestrowane w nim lub przechowuje dane kopii zapasowej.


- Można bezpiecznie usunąć magazynu, Wyrejestrowywanie serwerów w nim, a następnie usuń magazyn danych.
- Jeśli nie chcesz przechowywać żadnych danych w magazynie usługi Recovery Services i zechcesz usunąć magazynu, należy usunąć magazyn przy wymuszonego.
- Jeśli spróbuj usunąć magazynu, ale nie magazyn jest nadal skonfigurować do odbierania danych kopii zapasowej.

Aby dowiedzieć się, jak usunąć magazyn, zobacz sekcję [usuwanie magazynu w witrynie Azure portal](#delete-a-vault-from-the-azure-portal). Jeśli sekcja [usunąć magazyn przy wymuszonego](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Jeśli nie masz pewności, co znajduje się w magazynie, a następnie należy upewnić się, że można usunąć magazynu, zobacz sekcję [Usuń magazyn zależności i usuwania magazynu](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-the-azure-portal"></a>Usuwanie magazynu w witrynie Azure portal

1. Otwórz listę magazynów usługi Recovery Services w portalu.
2. Z listy wybierz magazyn, który chcesz usunąć. Zostanie otwarty na pulpicie nawigacyjnym magazynu.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Na pulpicie nawigacyjnym magazynu kliknij **Usuń**. Sprawdź, czy chcesz usunąć.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

2. Jeśli istnieją zależności magazynu **Błąd usuwania magazynu** pojawia się: 

    ![Błąd usuwania magazynu](./media/backup-azure-delete-vault/vault-delete-error.png)

    - Wykonaj te instrukcje, aby usunąć zależności przed usunięciem magazynu, zapoznaj się
    - [Wykonaj te instrukcje](#delete-the-recovery-services-vault-by-force) usunąć magazyn przy wymuszonego przy użyciu programu PowerShell. 

## <a name="delete-the-recovery-services-vault-by-force"></a>Usuwanie magazynu usługi Recovery Services przy wymuszonego

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby usunąć magazyn usługi Recovery Services, przy wymuszonego, można użyć programu PowerShell. Oznacza to, że magazyn i wszystkie skojarzone dane kopii zapasowej zostaną trwale usunięte. 

> [!Warning]
> W przypadku przy użyciu programu PowerShell, aby usunąć magazyn usługi Recovery Services, należy sprawdzić, czy chcesz trwale usunąć wszystkie dane kopii zapasowej w magazynie.
>

Aby usunąć magazyn usługi Recovery Services:

1. Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami.

   ```powershell
    Connect-AzAccount
   ```
2. Przy pierwszym użyciu usługi Azure Backup, należy zarejestrować dostawcę usługi Azure Recovery Service w subskrypcji przy użyciu [AzResourceProvider rejestru](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Otwórz okno programu PowerShell z uprawnieniami administratora.
4. Użyj `Set-ExecutionPolicy Unrestricted` do usunięcia wszelkich ograniczeń.
5. Uruchom następujące polecenie, aby pobrać pakiet klienta usługi Resource Manager platformy Azure z chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Użyj następującego polecenia, aby zainstalować klienta interfejsu API usługi Azure Resource Manager.

   `choco.exe install armclient`

7. W witrynie Azure portal Zbierz identyfikator i zasobów grupy nazwę subskrypcji dla magazynu, które chcesz usunąć.
8. W programie PowerShell uruchom następujące polecenie, korzystając z Identyfikatora subskrypcji, nazwę grupy zasobów i nazwę magazynu. Po uruchomieniu polecenia, usunięcie magazynu i wszystkich zależności.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Jeśli magazyn nie puste, zostanie wyświetlony błąd "Nie można usunąć magazynu, ponieważ istnieją zasoby w tym magazynie". Aby usunąć kontener w ramach magazynu, wykonaj następujące czynności:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Zaloguj się do Twojej subskrypcji w witrynie Azure portal i sprawdź, czy magazyn zostanie usunięty.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Usuń zależności magazynu i magazynu

Można ręcznie usunąć zależności magazynu, w następujący sposób:

- W **elementy kopii zapasowej** menu, usuń zależności:
    - Kopii zapasowych usługi Azure Storage (Azure Files)
    - SQL Server podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure
    - Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure
- W **infrastruktura zapasowa** menu, usuń zależności:
    - Tworzenie kopii zapasowych Microsoft Azure Backup Server (MABS)
    - Tworzenie kopii zapasowych programu System Center DPM

![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

### <a name="remove-backup-items"></a>Usuń elementy kopii zapasowej

Niniejsza procedura zawiera przykład pokazujący, jak usunąć dane kopii zapasowej z usługi Azure Files.

1. Kliknij przycisk **kopii zapasowych elementów** > **usługi Azure Storage (Azure Files)**

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Kliknij prawym przyciskiem myszy każdy element usługi Azure Files do usunięcia, a następnie kliknij przycisk **Zatrzymaj kopię zapasową**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-item.png)


3. W **Zatrzymaj tworzenie kopii zapasowej** > **wybierz jedną z opcji**, wybierz opcję **Usuń dane kopii zapasowej**.
4. Wpisz nazwę elementu, a następnie kliknij przycisk **Zatrzymaj kopię zapasową**. 
   - Sprawdza, czy chcesz usunąć element.
   - **Zatrzymaj tworzenie kopii zapasowej** przycisk aktywuje się po zweryfikowaniu.
   - Jeśli zachowania i nie należy usuwać dane, nie można usunąć magazyn.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Opcjonalnie Podaj powód, dlaczego one usuwania danych, a następnie dodaj komentarze.
6. Aby sprawdzić, czy zadanie usuwania zakończone, sprawdź komunikaty platformy Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png).
7. Po ukończeniu zadania, usługa wysyła komunikat: **procesu tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte**.
8. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** elementów w magazynie.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Usuń serwery infrastruktury kopii zapasowych

1. W menu pulpitu nawigacyjnego magazynu kliknij **infrastruktura zapasowa**.
2. Kliknij przycisk **serwery zarządzania kopiami zapasowymi** do wyświetlenia serwerów. 

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Kliknij prawym przyciskiem myszy element > **Usuń**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . W **Zatrzymaj tworzenie kopii zapasowej** > **wybierz jedną z opcji**, wybierz opcję **Usuń dane kopii zapasowej**.
4. Wpisz nazwę elementu, a następnie kliknij przycisk **Zatrzymaj kopię zapasową**. 
   - Sprawdza, czy chcesz usunąć element.
   - **Zatrzymaj tworzenie kopii zapasowej** przycisk aktywuje się po zweryfikowaniu.
   - Jeśli zachowania i nie należy usuwać dane, nie można usunąć magazyn.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Opcjonalnie Podaj powód, dlaczego one usuwania danych, a następnie dodaj komentarze.
6. Aby sprawdzić, czy zadanie usuwania zakończone, sprawdź komunikaty platformy Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png).
7. Po ukończeniu zadania, usługa wysyła komunikat: **procesu tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte**.
8. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** elementów w magazynie.


### <a name="remove-azure-backup-agent-recovery-points"></a>Usuwanie punktów odzyskiwania agenta usługi Kopia zapasowa Azure

1. W menu pulpitu nawigacyjnego magazynu kliknij **infrastruktura zapasowa**.
2. Kliknij przycisk **serwery zarządzania kopiami zapasowymi** wyświetlać serwery infrastruktury.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. W **serwerów chronionych** kliknij agenta usługi Kopia zapasowa Azure.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Kliknij serwer, na liście serwerów chronionych przy użyciu agenta usługi Kopia zapasowa Azure.

    ![Wybierz pozycję określonego chronionego serwera](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Na pulpicie nawigacyjnym wybranego serwera kliknij **Usuń**.

    ![Usuń wybrany serwer](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Na **Usuń** menu, wpisz nazwę elementu, a następnie kliknij przycisk **Usuń**.
   - Sprawdza, czy chcesz usunąć element.
   - **Zatrzymaj tworzenie kopii zapasowej** przycisk aktywuje się po zweryfikowaniu.
   - Jeśli zachowania i nie należy usuwać dane, nie można usunąć magazyn.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Opcjonalnie Podaj powód, dlaczego one usuwania danych, a następnie dodaj komentarze.
8. Aby sprawdzić, czy zadanie usuwania zakończone, sprawdź komunikaty platformy Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png).
7. Po ukończeniu zadania, usługa wysyła komunikat: **procesu tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte**.
8. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** elementów w magazynie.



### <a name="delete-the-vault-after-removing-dependencies"></a>Usuń magazyn po usunięciu zależności

1. Po usunięciu wszystkich zależności, przewiń do **Essentials** okienko w menu magazynu.

    - Nie powinny istnieć dowolne **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście.
    - Jeśli elementy nadal pojawia się w magazynie, można je usunąć.

2. W przypadku Brak elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Aby sprawdzić, czy chcesz usunąć magazynu, kliknij przycisk **tak**. Magazyn zostanie usunięty, a portal powróci do **New** menu usługi.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co zrobić, jeśli Zatrzymaj proces tworzenia kopii zapasowej, ale pozostawisz dane?

Jeśli Zatrzymaj proces tworzenia kopii zapasowej, ale przypadkowo przechowywania danych, możesz usunąć dane kopii zapasowej zgodnie z opisem w poprzedniej sekcji.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny usługi Recovery Services.
