---
title: Usuwanie magazynu usług odzyskiwania platformy Azure "
description: W tym artykule opisano sposób usuwania magazynu usług odzyskiwania. Artykuł zawiera kroki rozwiązywania problemów, spróbuj usunąć magazynu, ale nie.
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 6/21/2018
ms.author: markgal
ms.openlocfilehash: d8169eba6790e49a85d69434663faabe7430942e
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937604"
---
# <a name="delete-a-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

W tym artykule wyjaśniono, jak usunąć wszystkie elementy z magazynu usług odzyskiwania, a następnie usuń ją. Nie można usunąć magazynu usług odzyskiwania, jeśli jest zarejestrowany na serwerze i przechowuje dane kopii zapasowej. Jeśli spróbuj usunąć magazynu, ale nie magazyn nadal jest skonfigurowana do odbierania danych kopii zapasowej.

Aby dowiedzieć się, jak usunąć magazynu, zobacz sekcję [usuwanie magazynu z portalu Azure](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Jeśli nie chcesz zachować wszystkie dane w magazynie usług odzyskiwania i chcesz usunąć magazyn, zobacz sekcję [Usuń magazyn wymuszone](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Jeśli nie masz pewności, co znajduje się w magazynie i należy upewnić się, że można usunąć magazynu, zobacz sekcję [Usuń magazyn zależności i usuwania magazynu](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Usuwanie magazynu z portalu Azure

Jeśli masz już magazyn usług odzyskiwania, Otwórz, przejdź do kroku drugim.

1. Otwórz Azure portal, a z poziomu pulpitu nawigacyjnego magazynu, które chcesz usunąć.

   Jeśli nie masz magazyn usług odzyskiwania przypięty do pulpitu nawigacyjnego, w menu centralnym kliknij **wszystkie usługi** i na liście zasobów wpisz **usług odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Aby wyświetlić listę magazynów w ramach subskrypcji, kliknij przycisk **Magazyny usług odzyskiwania**.

   ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Zostanie wyświetlona lista magazynów usług odzyskiwania. 

   ![Wybierz magazyn z listy](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

2. Z listy wybierz magazyn, który chcesz usunąć. Po wybraniu magazyn zostanie otwarty jego pulpitem nawigacyjnym magazynu.

    ![Wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

3. Aby usunąć magazynu, na pulpicie nawigacyjnym magazynu, kliknij przycisk **usunąć**. Użytkownik zostanie zapytany, aby sprawdzić, czy chcesz usunąć magazyn.

    ![Wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Jeśli **Błąd usuwania magazynu** pojawi się, można usunąć zależności z magazynu lub środowiska PowerShell umożliwia usunięcie magazynu wymuszone. W poniższych sekcjach opisano sposób wykonywania tych zadań.

    ![Błąd usuwania magazynu](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Usuwanie magazynu usług odzyskiwania wymuszone

Za pomocą programu PowerShell można usunąć magazynu usług odzyskiwania wymuszone. W sposób życie magazyn usług odzyskiwania i wszystkie skojarzone dane kopii zapasowej, zostaje trwale usunięty. 

> [!Warning]
> Usuwanie magazynu usług odzyskiwania za pomocą programu PowerShell, można czy na pewno chcesz trwale usunąć wszystkie dane kopii zapasowej w magazynie.
>

Aby usunąć magazyn usług odzyskiwania:

1. Zaloguj się do konta platformy Azure.

   Zaloguj się do Twojej subskrypcji platformy Azure z `Connect-AzureRmAccount` poleceń i postępuj zgodnie z wyświetlanymi instrukcjami.

   ```powershell
    Connect-AzureRmAccount
   ```
   Przy pierwszym użyciu usługi Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji za pomocą polecenia [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

2. Otwórz okno programu PowerShell z uprawnieniami administratora.

3. Użyj `Set-ExecutionPolicy Unrestricted` do usunięcia ograniczeń.

4. Uruchom następujące polecenie, aby pobrać pakiet klienta Menedżera zasobów Azure z chocolately.org.

    `iex ((New-Object System.Net.WebClient) DownloadString('https://chocolatey.org/install.ps1))`

5. Użyj następującego polecenia, aby zainstalować klienta interfejsu API Azure Resource Manager.

   `choco.exe install armclient`

6. W portalu Azure Zbierz identyfikator subskrypcji i nazwa grupy zasobów dla magazynu usług odzyskiwania, które chcesz usunąć.

7. W programie PowerShell uruchom następujące polecenie, przy użyciu Identyfikatora subskrypcji, nazwa grupy zasobów i nazwa magazynu usług odzyskiwania. Po uruchomieniu polecenia usuwa magazynu oraz wszystkie zależności.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
8. Zaloguj się do subskrypcji w portalu Azure i sprawdź, czy magazyn zostanie usunięty.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Usuń zależności magazynu i magazynu

Aby ręcznie usunąć zależności magazynu, należy usunąć konfigurację między każdego elementu lub serwera i magazyn usług odzyskiwania. Podczas wykonywania kroków poniższej procedury, użyj **kopii zapasowej elementów** menu (zobacz obrazu) do:

* Kopii zapasowych Azure magazynu (pliki Azure)
* SQL Server podczas tworzenia kopii zapasowych maszyny Wirtualnej Azure
* Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure
* Kopie zapasowe agenta usług odzyskiwania Microsoft Azure

Użyj **infrastruktura kopii zapasowej** menu (zobacz obrazu) do:

* Kopii zapasowych serwera kopia zapasowa Azure
* Tworzenie kopii zapasowych programu System Center DPM

    ![Wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. W menu nawigacyjnym magazynu, przewiń w dół do sekcji chronionych elementów, a następnie kliknij przycisk **kopii zapasowej elementów**. W tym menu można zatrzymać i usunąć serwery plików Azure, serwerów SQL w maszynie Wirtualnej platformy Azure i maszyn wirtualnych platformy Azure. W tym przykładzie usuniemy dane kopii zapasowej z serwera plików Azure.

    ![Wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego](./media/backup-azure-delete-vault/selected-backup-items.png)

2. Wybierz typ kopii zapasowej, aby wyświetlić wszystkie elementy tego typu.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. Dla wszystkich elementów na liście, kliknij element prawym przyciskiem myszy i wybierz z menu kontekstowego **kopii zapasowej Zatrzymaj**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-item.png) 

    Zostanie otwarte menu zatrzymać kopii zapasowej.

4. Na **zatrzymać kopii zapasowej** menu z **wybierz opcję** menu, wybierz opcję **usunąć danych kopii zapasowej**, wpisz nazwę elementu i kliknij przycisk **kopii zapasowej Zatrzymaj**.

    Wpisz nazwę elementu, aby sprawdzić, czy chcesz go usunąć. **Zatrzymać kopii zapasowej** aktywuje przycisk po zweryfikowaniu elementu. Aby zachować dane, nie można usunąć magazyn.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Należy podać przyczynę, dlaczego jest usunięcie danych, a następnie dodaj komentarze. Aby zweryfikować zadanie zostało zakończone, sprawdź komunikaty Azure ![usunąć danych kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po zakończeniu zadania, usługa wysyła komunikat: *zatrzymano proces tworzenia kopii zapasowej i dane kopii zapasowej zostały usunięte*.

5. Po usunięciu elementu z listy na **kopii zapasowej elementów** menu, kliknij przycisk **Odśwież** elementów w magazynie.

      ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienka w menu magazynu usług odzyskiwania. Nie powinny istnieć żadnego **kopii zapasowej elementów**, **kopii zapasowej serwerów zarządzania**, lub **elementy replikowane** na liście. Jeśli nadal wyświetlane w magazynie, wróć do kroku 3 i wybierz typ innego elementu listy.  

6. Jeśli nie ma więcej elementów na pasku narzędzi magazynu, kliknij przycisk **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a następnie przywraca portalu **nowy** menu usługi.

## <a name="removing-azure-backup-server-or-dpm"></a>Usuwanie serwera kopii zapasowej Azure lub programu DPM

1. W menu nawigacyjnym magazynu, przewiń w dół do sekcji Zarządzaj, a następnie kliknij przycisk **infrastruktura kopii zapasowej**. 

2. W menu, kliknij przycisk **serwerów zarządzania kopiami zapasowymi** Aby wyświetlić serwer serwery kopia zapasowa Azure i programu System Center DPM. można zatrzymać i usunąć serwery plików Azure, serwerów SQL w maszynie Wirtualnej platformy Azure i maszyn wirtualnych platformy Azure. 

    ![Wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kliknij prawym przyciskiem myszy element, który chcesz usunąć, a podmenu, wybierz **usunąć**.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Zostanie otwarte menu zatrzymać kopii zapasowej.

4. Na **zatrzymać kopii zapasowej** menu z **wybierz opcję** menu, wybierz opcję **usunąć danych kopii zapasowej**, wpisz nazwę elementu i kliknij przycisk **kopii zapasowej Zatrzymaj**.

    Aby sprawdzić, czy chcesz usunąć, wpisz jego nazwę. **Zatrzymać kopii zapasowej** aktywuje przycisk po zweryfikowaniu elementu. Aby zachować dane, nie można usunąć magazynu.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Opcjonalnie możesz podać przyczyny, dlaczego usuwasz danych i Dodaj komentarze. Aby sprawdzić, czy zadanie zostało zakończone, sprawdź komunikaty Azure ![usunąć danych kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po zakończeniu zadania, usługa wysyła komunikat: Zatrzymano proces tworzenia kopii zapasowej i dane kopii zapasowej zostały usunięte.

5. Po usunięciu elementu z listy na **kopii zapasowej elementów** menu, kliknij przycisk **Odśwież** pozostałych elementów w magazynie.

      ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienka w menu magazynu usług odzyskiwania. Nie powinny istnieć żadnego **kopii zapasowej elementów**, **kopii zapasowej serwerów zarządzania**, lub **elementy replikowane** na liście. Jeśli nadal wyświetlane w magazynie, wróć do kroku 3 i wybierz typ innego elementu listy.  
6. Gdy nie ma więcej elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a następnie przywraca portalu **nowy** menu usługi.


## <a name="removing-azure-backup-agent-recovery-points"></a>Usuwanie punktów odzyskiwania agenta usługi Kopia zapasowa Azure

1. W menu nawigacyjnym magazynu, przewiń w dół do sekcji Zarządzaj, a następnie kliknij przycisk **infrastruktura kopii zapasowej**.

2. W podmenu, kliknij przycisk **chronione serwery** Aby wyświetlić listę chronione typu serwera, w tym agenta usługi Kopia zapasowa Azure.

    ![Wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. W **chronione serwery** kliknij agenta usługi Kopia zapasowa Azure.

    ![Wybierz typ kopii zapasowej](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Zostanie otwarty na liście serwerów chronionych przy użyciu agenta usługi Kopia zapasowa Azure.

    ![Wybierz serwer chroniony określonych](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

4. Na liście serwerów kliknij przycisk, aby otworzyć menu.

    ![wyświetlić pulpit nawigacyjny wybranego serwera](./media/backup-azure-delete-vault/selected-protected-server.png)

5. W menu nawigacyjnym wybranego serwera, kliknij przycisk **usunąć**.

    ![usunąć wybrany serwer](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Na **usunąć** menu, wpisz nazwę elementu, a następnie kliknij przycisk **usunąć**.

    Wpisz nazwę elementu, aby sprawdzić, czy chcesz go usunąć. **Usunąć** aktywuje przycisk po zweryfikowaniu elementu.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Opcjonalnie możesz podać przyczyny, dlaczego usuwasz danych i Dodaj komentarze. Aby sprawdzić, czy zadanie zostało zakończone, sprawdź komunikaty Azure ![usunąć danych kopii zapasowej](./media/backup-azure-delete-vault/messages.png). <br/>
    Po zakończeniu zadania, usługa wysyła komunikat: Zatrzymano proces tworzenia kopii zapasowej i dane kopii zapasowej zostały usunięte.

7. Po usunięciu elementu z listy na **kopii zapasowej elementów** menu, kliknij przycisk **Odśwież** pozostałych elementów w magazynie.

      ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/empty-items-list.png)

      Jeśli nie ma żadnych elementów na liście, przewiń do **Essentials** okienka w menu magazynu usług odzyskiwania. Nie powinny istnieć żadnego **kopii zapasowej elementów**, **kopii zapasowej serwerów zarządzania**, lub **elementy replikowane** na liście. Jeśli nadal wyświetlane w magazynie, wróć do kroku 3 i wybierz typ innego elementu listy.  
8. Gdy nie ma więcej elementów w magazynie, na pulpicie nawigacyjnym magazynu kliknij **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

9. Aby sprawdzić, czy chcesz usunąć magazyn, kliknij przycisk **tak**.

    Magazyn zostanie usunięty, a następnie przywraca portalu **nowy** menu usługi.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co zrobić, jeśli zatrzymać proces tworzenia kopii zapasowej, ale zachować dane?

Jeśli jednak przypadkowo zatrzymać proces tworzenia kopii zapasowej *zachować* danych, należy usunąć dane kopii zapasowej przed usunięciem magazynu. Aby usunąć dane kopii zapasowej:

1. Na **elementów kopii zapasowych** menu, kliknij element prawym przyciskiem myszy, a następnie w menu kontekstowym kliknij **usunąć danych kopii zapasowej**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **Usunąć dane z kopii zapasowej** zostanie otwarte menu.
2. Na **usunąć dane z kopii zapasowej** menu, wpisz nazwę elementu, a następnie kliknij przycisk **usunąć**.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Po usunięciu danych, wróć do kroku 4c i kontynuować proces.
