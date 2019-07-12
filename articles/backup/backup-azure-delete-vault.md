---
title: Usuwanie magazynu usługi Recovery Services na platformie Azure
description: W tym artykule opisano, jak usunąć magazyn usługi Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827868"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

W tym artykule opisano sposób usuwania [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services. Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwając magazynu.


## <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu usługi Recovery Services, które ma zależności, takich jak serwery chronione lub serwery zarządzania kopiami zapasowymi skojarzonego z magazynem.<br/>
Nie można usunąć magazynu, zawierających dane kopii zapasowej, (oznacza to, nawet jeśli masz zatrzymywania ochrony, ale przechowywane dane kopii zapasowej).

Jeśli usuniesz magazynu, która zawiera zależności, jej spowoduje wyświetlenie następującego błędu:

![Usuń błąd magazynu](./media/backup-azure-delete-vault/error.png)

Można bezpiecznie usunąć magazynu, wykonaj kroki opisane w poniższej kolejności:
- Zatrzymanie ochrony i usunięcie danych kopii zapasowej
- Usuń chronione serwery lub serwery zarządzania kopiami zapasowymi
- Usuń magazyn.


## <a name="delete-backup-data-and-backup-items"></a>Usuń dane kopii zapasowych i kopii zapasowych elementów

Przed przystąpieniem do dalszych odczytu **[to ](#before-you-start)** sekcji, aby zrozumieć zależności i proces usuwania magazynu.

### <a name="for-protected-items-in-cloud"></a>Dla elementów chronionych w chmurze

Aby zatrzymać ochronę i Usuń dane kopii zapasowej, wykonaj poniższe:

1. Z witryny portal > magazyn usługi Recovery Services > elementy kopii zapasowej wybierz chronione elementy w chmurze.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Dla każdego elementu, należy kliknąć prawym przyciskiem myszy i wybierz polecenie **Zatrzymaj kopię zapasową**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-item.png)

3. W **Zatrzymaj tworzenie kopii zapasowej** > **wybierz jedną z opcji**, wybierz opcję **Usuń dane kopii zapasowej**.
4. Wpisz nazwę elementu, a następnie kliknij przycisk **Zatrzymaj kopię zapasową**.
   - Sprawdza, czy chcesz usunąć element.
   - **Zatrzymaj tworzenie kopii zapasowej** przycisk aktywuje się po zweryfikowaniu.
   - Jeśli zachowania i nie należy usuwać dane, nie można usunąć magazyn.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Sprawdź **powiadomień** ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png). Po zakończeniu usługa wyświetlany jest komunikat: **Zatrzymywanie tworzenia kopii zapasowej i usuwanie tworzyć kopie zapasowe danych dla "*elementu kopii zapasowej*"** . **Operacja została ukończona pomyślnie**.
6. Kliknij przycisk **Odśwież** na **elementy kopii zapasowej** menu, aby sprawdzić, jeśli element kopii zapasowej zostaną usunięte.

      ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Dla agenta usług MARS

Aby zatrzymać ochronę i Usuń dane kopii zapasowej, wykonaj kroki w podanej kolejności:

- [Krok 1. Usuń elementy kopii zapasowej z poziomu konsoli zarządzania usług MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Krok 2. W portalu należy usunąć agenta usługi Azure Backup](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Krok 1: Usuń elementy kopii zapasowej z poziomu konsoli zarządzania usług MARS

Jeśli nie możesz wykonać ten krok, ze względu na niedostępność serwera, skontaktuj się z pomocą techniczną firmy Microsoft.

- Uruchom konsolę zarządzania MARS, przejdź do **akcje** okienka i wybierz polecenie **Zaplanuj wykonywanie kopii zapasowej**.
- Z **modyfikowanie lub zatrzymywanie zaplanowanych kopii zapasowych** kreatora, wybierz opcję **zaprzestać korzystania z tego harmonogramu tworzenia kopii zapasowych i usunięcie wszystkich kopii zapasowych przechowywanych** i kliknij przycisk **dalej**.

    ![Modyfikowanie lub zatrzymywanie zaplanowane tworzenie kopii zapasowej](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Z **Zatrzymaj kopię zapasową zaplanowane** kreatora, kliknij przycisk **Zakończ**.

    ![Zatrzymaj zaplanowane tworzenie kopii zapasowej](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Monit o podanie zabezpieczający numer Pin. Aby wygenerować kod PIN, wykonaj następujące czynności:
  - Zaloguj się do Portalu Azure.
  - Przejdź do **magazyn usługi Recovery Services** > **ustawienia** > **właściwości**.
  - W obszarze **zabezpieczający numer PIN**, kliknij przycisk **Generuj**. Skopiuj ten numer PIN. (Ten numer PIN jest prawidłowy tylko pięć minut)
- W konsoli zarządzania (aplikacja kliencka) Wklej kod PIN, a następnie kliknij przycisk **Ok**.

  ![Zabezpieczający numer Pin](./media/backup-azure-delete-vault/security-pin.png)

- W **zmodyfikować postęp kopii zapasowej** zostanie wyświetlony Kreator *usunięte dane kopii zapasowych będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuwanie infrastruktury kopii zapasowych](./media/backup-azure-delete-vault/deleted-backup-data.png)

Teraz, gdy elementy kopii zapasowej została usunięta ze środowiska lokalnego, należy wykonać poniższe czynności w portalu.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Krok 2: W portalu należy usunąć agenta usługi Azure Backup

Upewnij się, [kroku 1](#step-1-delete-backup-items-from-mars-management-console) zostało zakończone przed kontynuacją:

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

> [!NOTE]
> Jeśli widzisz poniższy błąd, a następnie wykonaj czynności opisane w pierwszej [usuwania elementów kopii zapasowej z poziomu konsoli zarządzania](#step-1-delete-backup-items-from-mars-management-console).
>
>![Usuwanie nie powiodło się](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Jeśli nie możesz wykonać czynności, aby usunąć kopie zapasowe z poziomu konsoli zarządzania na przykład z powodu niedostępności serwera za pomocą konsoli zarządzania skontaktuj się z działem pomocy technicznej firmy Microsoft.

7. Sprawdź **powiadomień** ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png). Po zakończeniu usługa wyświetlany jest komunikat: **Zatrzymywanie tworzenia kopii zapasowej i usuwanie tworzyć kopie zapasowe danych dla "*elementu kopii zapasowej*"** . **Operacja została ukończona pomyślnie**.
8. Kliknij przycisk **Odśwież** na **elementy kopii zapasowej** menu, aby sprawdzić, jeśli element kopii zapasowej zostaną usunięte.


### <a name="for-mabs-agent"></a>Dla serwera usługi Mab agenta

Aby zatrzymać ochronę i Usuń dane kopii zapasowej, wykonaj kroki w podanej kolejności:

- [Krok 1. Usuń elementy kopii zapasowej z poziomu konsoli zarządzania serwera usługi Mab](#step-1-delete-backup-items-from-mabs-management-console)
- [Krok 2. W portalu Usuń serwery zarządzania usługi Azure Backup](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Krok 1: Usuń elementy kopii zapasowej z poziomu konsoli zarządzania serwera usługi Mab

Jeśli nie możesz wykonać ten krok, ze względu na niedostępność serwera, skontaktuj się z pomocą techniczną firmy Microsoft.

**Metoda 1** na zatrzymanie ochrony i usunięcie danych kopii zapasowej, należy wykonać następujące czynności:

1.  W konsoli administratora programu DPM kliknij **ochrony** na pasku nawigacyjnym.
2.  W okienku wyświetlania wybierz członka grupy ochrony, który chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wybrać **zatrzymać z elementami członkowskimi grupy ochrony** opcji.
3.  Z **Zatrzymaj ochronę** okno dialogowe, wybierz opcję **Usuń chronione dane** > **usunąć magazyn w tryb online** pole wyboru, a następnie kliknij przycisk **Stop Ochrona**.

    ![Usuń magazyn w tryb online](./media/backup-azure-delete-vault/delete-storage-online.png)

Stan chroniony element członkowski został zmieniony na **dostępna nieaktywna replika**.

5. Kliknij prawym przyciskiem myszy nieaktywnych grup ochrony i wybierz **Usuń nieaktywną ochronę**.

    ![Usuń nieaktywną ochronę](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Z **usuwanie nieaktywnej ochrony** wybierz **usunąć magazyn online** i kliknij przycisk **Ok**.

    ![Usuwanie repliki na dysku i w trybie online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metoda 2** Uruchom **zarządzania serwera usługi Mab** konsoli. W **wybierz metodę ochrony danych** sekcji, odznacz **chcę uzyskać ochronę online**.

  ![Wybierz metodę ochrony danych](./media/backup-azure-delete-vault/data-protection-method.png)

Teraz, gdy elementy kopii zapasowej została usunięta ze środowiska lokalnego, należy wykonać poniższe czynności w portalu.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Krok 2: W portalu Usuń serwery zarządzania usługi Azure Backup

Upewnij się, [kroku 1](#step-1-delete-backup-items-from-mabs-management-console) zostało zakończone przed kontynuacją:

1. W menu pulpitu nawigacyjnego magazynu kliknij **infrastruktura zapasowa**.
2. Kliknij przycisk **serwery zarządzania kopiami zapasowymi** do wyświetlenia serwerów.

    ![Wybierz magazyn, aby otworzyć jego pulpit nawigacyjny](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kliknij prawym przyciskiem myszy element > **Usuń**.
4. Na **Usuń** menu, wpisz nazwę serwera i kliknij przycisk **Usuń**.

     ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Jeśli widzisz poniższy błąd, a następnie wykonaj czynności opisane w pierwszej [usuwania elementów kopii zapasowej z poziomu konsoli zarządzania](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![Usuwanie nie powiodło się](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Jeśli nie możesz wykonać czynności, aby usunąć kopie zapasowe z poziomu konsoli zarządzania na przykład z powodu niedostępności serwera za pomocą konsoli zarządzania skontaktuj się z działem pomocy technicznej firmy Microsoft.

5. Sprawdź **powiadomień** ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/messages.png). Po zakończeniu usługa wyświetlany jest komunikat: **Zatrzymywanie tworzenia kopii zapasowej i usuwanie tworzyć kopie zapasowe danych dla "*elementu kopii zapasowej*"** . **Operacja została ukończona pomyślnie**.
6. Kliknij przycisk **Odśwież** na **elementy kopii zapasowej** menu, aby sprawdzić, jeśli element kopii zapasowej zostaną usunięte.


## <a name="delete-the-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

1. Po usunięciu wszystkich zależności, przewiń do **Essentials** okienko w menu magazynu.
2. Sprawdź, czy nie ma **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście. Jeśli elementy nadal pojawiają się w magazynie [je usunąć](#delete-backup-data-and-backup-items).

3. W przypadku Brak elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **Usuń**.

    ![Usuń dane kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Aby sprawdzić, czy chcesz usunąć magazynu, kliknij przycisk **tak**. Magazyn zostanie usunięty, a portal powróci do **New** menu usługi.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Usuwanie magazynu usługi Recovery Services za pomocą klienta usługi Azure Resource Manager

Tę opcję, aby usunąć magazyn usługi Recovery Services jest zalecane tylko, gdy wszystkie zależności są usuwane, a nadal otrzymujesz *Błąd usuwania magazynu*.



- Z **Essentials** okienko w menu magazynu, sprawdź, czy nie ma **kopii zapasowych elementów**, **kopii zapasowych serwerów zarządzania**, lub **zreplikowane elementy** na liście. W przypadku elementów kopii zapasowych, a następnie wykonaj kroki opisane w [usunięcie danych kopii zapasowych i kopii zapasowej elementów](#delete-backup-data-and-backup-items).
- Ponów próbę wykonania [usunięcia magazynu z portalu](#delete-the-recovery-services-vault).
- Jeśli wszystkie zależności są usuwane, a nadal otrzymujesz *Błąd usuwania magazynu* następnie użyć narzędzia ARMClient wykonać czynności podane poniżej;

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalowanie narzędzia chocolatey z [tutaj](https://chocolatey.org/) i zainstalować ARMClient, uruchom poniższe polecenie:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Zaloguj się do konta platformy Azure, a następnie uruchom następujące polecenie:

    `ARMClient.exe login [environment name]`

3. W witrynie Azure portal Zbierz identyfikator i zasobów grupy nazwę subskrypcji dla magazynu, które chcesz usunąć.

Aby uzyskać więcej informacji na temat polecenia ARMClient, użyj tego [dokumentu](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services za pomocą klienta usługi Azure Resource Manager

1. Uruchom następujące polecenie, korzystając z Identyfikatora subskrypcji, nazwę grupy zasobów i nazwę magazynu. Po uruchomieniu polecenia, usuwa magazynu, jeśli nie masz żadnych zależności.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Jeśli magazyn nie jest pusta, zostanie wyświetlony błąd "Nie można usunąć magazynu, ponieważ istnieją zasoby w tym magazynie". Aby usunąć elementy chronione / kontenera w magazynie, wykonaj następujące czynności:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. W witrynie Azure portal Sprawdź, czy magazyn zostanie usunięty.


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny usługi Recovery Services.<br/>
[Dowiedz się więcej o](backup-azure-manage-windows-server.md) monitorowanie i Zarządzanie magazynami usługi Recovery Services.
