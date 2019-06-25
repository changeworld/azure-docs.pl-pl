---
title: Usuwanie magazynu usługi Recovery Services na platformie Azure
description: W tym artykule opisano, jak usunąć magazyn usługi Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: 8df4f17c9afbf10c6507e505c6540c3f66a42309
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275631"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

W tym artykule opisano sposób usuwania [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services. Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwanie magazynu i usuwanie magazynu przy wymuszonego.


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem, ważne jest zrozumienie, że nie można usunąć magazynu usługi Recovery Services, w którym znajdują się serwery zarejestrowane w nim lub przechowuje dane kopii zapasowej.

- Można bezpiecznie usunąć magazynu, Wyrejestrowywanie serwerów, które zawiera, usunięcie magazynu danych, a następnie usuń magazyn.
- Jeśli próbujesz usunąć magazyn, nadal ma zależności, komunikat o błędzie jest wystawiony i należy ręcznie usunąć zależności magazynu, w tym:
    - Kopie zapasowe elementów
    - Serwery chronione
    - Wykonaj kopię zapasową serwerów zarządzania (serwer usługi Azure Backup, program DPM) ![wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Jeśli nie chcesz przechowywać żadnych danych w magazynie usługi Recovery Services i zechcesz usunąć magazynu, należy usunąć magazyn przy wymuszonego.
- Jeśli spróbuj usunąć magazynu, ale nie magazyn jest nadal skonfigurować do odbierania danych kopii zapasowej.


## <a name="delete-a-vault-from-the-azure-portal"></a>Usuwanie magazynu w witrynie Azure portal

1. Otwórz pulpit nawigacyjny magazynu.  
2. Na pulpicie nawigacyjnym kliknij **Usuń**. Sprawdź, czy chcesz usunąć.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Jeśli otrzymasz komunikat o błędzie, Usuń [kopii zapasowych elementów](#remove-backup-items), [serwerów infrastruktury](#remove-azure-backup-management-servers), i [punktów odzyskiwania](#remove-azure-backup-agent-recovery-points), a następnie usuń magazyn.

![Usuń błąd magazynu](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Usuwanie magazynu usługi Recovery Services za pomocą klienta usługi Azure Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalowanie narzędzia chocolatey z [tutaj](https://chocolatey.org/) i zainstalować ARMClient, uruchom poniższe polecenie:

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. Zaloguj się do konta platformy Azure, a następnie uruchom następujące polecenie:

    ` ARMClient.exe login [environment name] `

3. W witrynie Azure portal Zbierz identyfikator i zasobów grupy nazwę subskrypcji dla magazynu, które chcesz usunąć.

Aby uzyskać więcej informacji na temat polecenia ARMClient, użyj tego [dokumentu](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services za pomocą klienta usługi Azure Resource Manager

1. Uruchom następujące polecenie, korzystając z Identyfikatora subskrypcji, nazwę grupy zasobów i nazwę magazynu. Po uruchomieniu polecenia usuwa magazynu, jeśli nie masz żadnych zależności.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Jeśli magazyn nie puste, zostanie wyświetlony błąd "Nie można usunąć magazynu, ponieważ istnieją zasoby w tym magazynie". Aby usunąć elementy chronione / kontenera w magazynie, wykonaj następujące czynności:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. W witrynie Azure portal Sprawdź, czy magazyn zostanie usunięty.


## <a name="remove-vault-items-and-delete-the-vault"></a>Usuń elementy magazynu, a następnie usuń Magazyn

Te procedury zawierają przykładowe usuwania danych kopii zapasowych i serwerów infrastruktury. Po wszystko, co zostanie usunięty z magazynu, możesz go usunąć.

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

5. Opcjonalnie Podaj powód, dlaczego jest usunięcie danych i Dodaj komentarze.
6. Aby sprawdzić, czy zadanie usuwania zakończone, sprawdź komunikaty platformy Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png).
7. Po ukończeniu zadania, usługa wysyła komunikat: **procesu tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte**.
8. Po usunięciu elementu na liście na **elementy kopii zapasowej** menu, kliknij przycisk **Odśwież** elementów w magazynie.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-azure-backup-management-servers"></a>Usuń serwery zarządzania usługi Azure Backup

1. W menu pulpitu nawigacyjnego magazynu kliknij **infrastruktura zapasowa**.
2. Kliknij przycisk **serwery zarządzania kopiami zapasowymi** do wyświetlenia serwerów.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kliknij prawym przyciskiem myszy element > **Usuń**.
4. Na **Usuń** menu, wpisz nazwę serwera, a następnie kliknij przycisk **Usuń**.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  Opcjonalnie Podaj powód, dlaczego jest usunięcie danych i Dodaj komentarze.

> [!NOTE]
> Aby usunąć elementy w konsoli serwera zarządzania lub konsoli MARS na serwerze chronionym, Zatrzymaj ochronę i usuwanie kopii zapasowych. Jeśli pozostają elementów kopii zapasowych, następujący błąd pojawi się podczas próby usunięcia i Wyrejestruj serwer:
> 
>![Usuwanie nie powiodło się](./media/backup-azure-delete-vault/deletion-failed.png)

6. Aby sprawdzić, czy zadanie usuwania zakończone, sprawdź komunikaty platformy Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png).
7. Po ukończeniu zadania, usługa wysyła komunikat: **procesu tworzenia kopii zapasowej została zatrzymana, a dane kopii zapasowej zostały usunięte**.
8. Po usunięciu elementu na liście na **infrastruktura zapasowa** menu, kliknij przycisk **Odśwież** elementów w magazynie.


### <a name="remove-azure-backup-agent-recovery-points"></a>Usuwanie punktów odzyskiwania agenta usługi Kopia zapasowa Azure

1. W menu pulpitu nawigacyjnego magazynu kliknij **infrastruktura zapasowa**.
2. Kliknij przycisk **serwerów chronionych** wyświetlać serwery infrastruktury.

    ![Wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. W **serwerów chronionych** kliknij agenta usługi Kopia zapasowa Azure.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Kliknij serwer, na liście serwerów chronionych przy użyciu agenta usługi Kopia zapasowa Azure.

    ![Wybierz pozycję określonego chronionego serwera](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Na pulpicie nawigacyjnym wybranego serwera kliknij **Usuń**.

    ![Usuń wybrany serwer](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Na **Usuń** menu, wpisz nazwę serwera, a następnie kliknij przycisk **Usuń**.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Opcjonalnie Podaj powód, dlaczego jest usunięcie danych i Dodaj komentarze.

> [!NOTE]
> Elementy kopii zapasowej skojarzony z serwerem Utwórz kopię zapasową serwera zarządzania lub agenta usługi Azure Backup muszą zostać usunięte przed usunięciem rejestracje tych serwerów. Aby usunąć elementy kopii zapasowej, przejdź do programu DPM SC, serwera usługi Mab lub konsoli zarządzania usług MARS na serwerze, jeśli ma to zastosowanie, a następnie wybierz odpowiednie opcje na zatrzymanie ochrony i usunięcie kopii zapasowych. Jeśli żadnych elementów kopii zapasowych są nadal skojarzone, zostanie wyświetlony następujący błąd:
> 
> 
>![Usuwanie nie powiodło się](./media/backup-azure-delete-vault/deletion-failed.png)

8. Aby sprawdzić, czy zadanie usuwania zakończone, sprawdź komunikaty platformy Azure ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png).
9. Po usunięciu elementu na liście na **infrastruktura zapasowa** menu, kliknij przycisk **Odśwież** elementów w magazynie.

### <a name="delete-the-vault-after-removing-dependencies"></a>Usuń magazyn po usunięciu zależności

1. Po usunięciu wszystkich zależności, przewiń do **Essentials** okienko w menu magazynu.
2. Sprawdź, czy nie ma **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście. Jeśli elementy nadal pojawia się w magazynie, można je usunąć.

3. W przypadku Brak elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Aby sprawdzić, czy chcesz usunąć magazynu, kliknij przycisk **tak**. Magazyn zostanie usunięty, a portal powróci do **New** menu usługi.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co zrobić, jeśli Zatrzymaj proces tworzenia kopii zapasowej, ale pozostawisz dane?

Jeśli Zatrzymaj proces tworzenia kopii zapasowej, ale przypadkowo przechowywania danych, możesz usunąć dane kopii zapasowej zgodnie z opisem w poprzedniej sekcji.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny usługi Recovery Services.
