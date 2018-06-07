---
title: Tworzenie kopii zapasowej i przywracania szyfrowane maszyn wirtualnych przy użyciu usługi Kopia zapasowa Azure
description: Ten artykuł zawiera informacje o kopii zapasowej i przywracania środowiska dla maszyn wirtualnych za pomocą szyfrowania dysków Azure.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18a4b0f369ea7ed65945fd43a60cbf44589ca8f2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607290"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Kopie zapasowe i przywracanie zaszyfrowanych maszyn wirtualnych w usłudze Kopia zapasowa Azure
Ten artykuł zawiera informacje o kroki kopii zapasowej i przywracanie maszyn wirtualnych (VM) za pomocą usługi Kopia zapasowa Azure. Zapewnia także szczegółowe informacje o obsługiwanych scenariuszach wymagania wstępne i kroki rozwiązywania problemów w przypadku wystąpienia błędów.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

 * Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych jest obsługiwana tylko dla maszyn wirtualnych, które używają modelu wdrażania usługi Azure Resource Manager. Nie jest obsługiwane dla maszyn wirtualnych, które używają klasycznym modelu wdrażania. <br>
 * Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych jest obsługiwana dla systemów Windows i maszyn wirtualnych systemu Linux, używanego przez szyfrowanie dysków Azure. Szyfrowanie dysków używa branży funkcje BitLocker standardowych systemu Windows i dm-crypt systemu Linux w celu zapewnienia szyfrowania dysków. <br>
 
 W poniższej tabeli przedstawiono obsługiwane scenariusze dla klucza szyfrowania funkcją BitLocker (BEK) — tylko i kluczy szyfrowania klucza (KEK) - zaszyfrowane maszyn wirtualnych:
 
 
   |  | BEK + KEK maszyny wirtualne | Tylko do BEK maszyny wirtualne |
   | --- | --- | --- |
   | **Maszyny wirtualne w niezarządzanych**  | Yes | Yes  |
   | **Zarządzanych maszyn wirtualnych**  | Yes | Yes  |

## <a name="prerequisites"></a>Wymagania wstępne
* Maszyna wirtualna została zaszyfrowana przy użyciu [szyfrowania dysków Azure](../security/azure-security-disk-encryption.md).

* Magazyn usług odzyskiwania został utworzony i replikacji magazynu została ustawiona, wykonując kroki opisane w [przygotować swoje środowisko do tworzenia kopii zapasowej](backup-azure-arm-vms-prepare.md).

* Kopia zapasowa została podana [uprawnień dostępu do magazynu kluczy](#provide-permissions-to-backup) zawierający klucze i klucze tajne dla zaszyfrowanych maszyn wirtualnych.

## <a name="backup-encrypted-vm"></a>Zaszyfrowana kopia zapasowa maszyny Wirtualnej
Ustawienie celu kopii zapasowej, definiowania zasad, skonfiguruj elementów i Wyzwól kopię zapasową, wykonaj następujące kroki.

### <a name="configure-backup"></a>Konfigurowanie kopii zapasowych
1. Jeśli masz już magazyn usług odzyskiwania, Otwórz, przejdź do następnego kroku. Jeśli nie masz magazyn usług odzyskiwania, Otwórz, ale w portalu Azure, wybierz opcję **wszystkie usługi**.

   a. Na liście zasobów wpisz **Usługi odzyskiwania**.

   b. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Po wyświetleniu **Magazyny usług odzyskiwania**, zaznacz go.

      ![Magazyn usługi Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Zostanie wyświetlona lista magazynów Usług odzyskiwania. Wybierz magazyn z listy.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
2. Wybierz z listy elementów, który pojawia się w magazynie, **kopii zapasowej** można uruchomić tworzenia kopii zapasowej zaszyfrowanego maszyny Wirtualnej.

      ![Blok tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/select-backup.png)
3. Na **kopii zapasowej** kafelka, wybierz opcję **cel kopii zapasowej**.

      ![Scenariusz bloku](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. W obszarze **gdzie działa Twoje obciążenie?**, wybierz pozycję **Azure**. W obszarze **co chcesz utworzyć kopię zapasową?**, wybierz pozycję **maszyny wirtualnej**. Następnie wybierz przycisk **OK**.

   ![Otwarcie bloku scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. W obszarze **wybierz zasady tworzenia kopii zapasowej**, wybierz zasady tworzenia kopii zapasowej, które chcesz zastosować do magazynu. Następnie wybierz przycisk **OK**.

      ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Szczegóły domyślne zasady są wyświetlane. Jeśli chcesz utworzyć zasady, wybierz **Utwórz nowy** z listy rozwijanej. Po wybraniu **OK**, zasad tworzenia kopii zapasowej jest skojarzona z magazynem.

6. Wybierz zaszyfrowanych maszyn wirtualnych do skojarzenia z określonych zasad, a następnie wybierz **OK**.

      ![Wybierz zaszyfrowanych maszyny wirtualne](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Ta strona zawiera komunikat o magazynów kluczy powiązanych z zaszyfrowanego wybranych maszyn wirtualnych. Kopia zapasowa wymaga dostępu tylko do odczytu do kluczy i kluczy tajnych w magazynie kluczy. Aby utworzyć kopię zapasową kluczy i kluczy tajnych, oraz skojarzone maszyn wirtualnych używa tych uprawnień.<br>
Jeśli jesteś **użytkownika elementu członkowskiego**, Włącz wykonywania kopii zapasowej zostanie bezproblemowo uzyskać dostępu do magazynu kluczy do tworzenia kopii zapasowej szyfrowane maszyn wirtualnych bez interwencji użytkownika.

   ![Zaszyfrowanego komunikatu maszyny wirtualne](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Aby uzyskać **użytkownika gościa**, należy podać uprawnienia dostępu magazynu kluczy dla kopii zapasowych do pracy z usługą kopii zapasowej. Można podać te uprawnienia, wykonując [czynności wymienionych w poniższej sekcji](#provide-permissions-to-backup)

   ![Zaszyfrowanego komunikatu maszyny wirtualne](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Teraz, gdy zdefiniowano wszystkich ustawień dla magazynu, wybierz **włączenia kopii zapasowej** w dolnej części strony. **Włączenia kopii zapasowej** wdraża zasady dla magazynu i maszyn wirtualnych.
  
8. Kolejną fazą przygotowania jest instalowanie agenta maszyny Wirtualnej lub upewnić się, że Agent maszyny Wirtualnej jest zainstalowany. Taki sam, wykonaj czynności opisane w [przygotować swoje środowisko do tworzenia kopii zapasowej](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Wyzwalacz zadania tworzenia kopii zapasowej
Postępuj zgodnie z instrukcjami [kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usług odzyskiwania](backup-azure-arm-vms.md) do wyzwalania zadania tworzenia kopii zapasowej.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Kontynuuj tworzenie kopii zapasowych maszyn wirtualnych już utworzona kopia zapasowa jest włączone szyfrowanie  
Jeśli masz maszyny wirtualne już tworzona kopia zapasowa w magazynie usług odzyskiwania, które aktywowano na potrzeby szyfrowania później, musi mieć uprawnienia do tworzenia kopii zapasowej, aby uzyskać dostęp do klucza magazynu kopii zapasowych kontynuować. Można podać te uprawnienia, wykonując [kroki opisane w poniższej sekcji](#provide-permissions-to-azure-backup). Lub możesz wykonać kroki programu PowerShell w sekcji "Kopia zapasowa Enable" [dokumentacji programu PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Podaj uprawnienia do tworzenia kopii zapasowej
Wykonaj następujące kroki, aby zapewnić odpowiednie uprawnienia do tworzenia kopii zapasowej dostępu do magazynu kluczy i wykonanie kopii zapasowej zaszyfrowanego maszyn wirtualnych.
1. Wybierz **wszystkie usługi**i wyszukaj **klucza magazynów**.

    ![Magazyny kluczy](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Z listy magazynów kluczy wybierz magazyn kluczy, skojarzone z zaszyfrowanego maszyn wirtualnych, które należy wykonać kopię zapasową.

     ![Wybór magazynu kluczy](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Wybierz **zasady dostępu**, a następnie wybierz **Dodaj nowe**.

    ![Dodaj nowe](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Wybierz **Wybierz podmiot zabezpieczeń**, a następnie wpisz **usługi zarządzania kopii zapasowej** w polu wyszukiwania. 

    ![Wyszukiwania usługi tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Wybierz **usługi zarządzania kopii zapasowej**, a następnie wybierz **wybierz**.

    ![Wybór usługi tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. W obszarze **Konfiguruj z szablonu (opcjonalnie)**, wybierz pozycję **kopia zapasowa Azure**. Wymagane uprawnienia są wstępnie dla **klucza uprawnienia** i **tajny uprawnienia**. Jeśli maszyna wirtualna jest szyfrowany za pomocą **BEK tylko**, uprawnienia tylko do kluczy tajnych są wymagane, więc musisz usunąć wybór **klucza uprawnienia**.

    ![Wybranej kopii zapasowej systemu Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Kliknij przycisk **OK**. Zwróć uwagę, że **usługi zarządzania kopii zapasowej** pobiera dodane w **zasady dostępu**. 

    ![Zasady dostępu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Wybierz **zapisać** udzielenia wymaganych uprawnień do wykonania kopii zapasowej.

    ![Zasady tworzenia kopii zapasowej dostępu](./media/backup-azure-vms-encryption/save-access-policy.png)

Po pomyślnie zostały podane uprawnienia, może kontynuować Włączanie kopii zapasowej zaszyfrowanego maszyn wirtualnych.

## <a name="restore-an-encrypted-vm"></a>Przywracanie zaszyfrowanych maszyny Wirtualnej
Aby przywrócić zaszyfrowanych maszyny Wirtualnej, najpierw przywrócić dysków wykonując kroki opisane w sekcji "Przywracanie kopii zapasowej dysków" [wybierz konfigurację przywracania maszyny Wirtualnej](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Po tym można użyć jednej z następujących opcji:

* Wykonaj kroki programu PowerShell w [utworzyć Maszynę wirtualną z dysków przywróconej](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) tworzenie pełnej maszyny Wirtualnej z przywróconą dysków.
* Lub, [dostosować przywróconą maszyną Wirtualną za pomocą szablonów](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) do tworzenia maszyn wirtualnych z przywróconą dysków. Szablony może służyć tylko dla punktów odzyskiwania utworzonych po 26 kwietnia 2017 r.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Operacja | Szczegóły błędu | Rozwiązanie |
| --- | --- | --- |
|Backup | Kopia zapasowa nie ma wystarczających uprawnień do magazynu kluczy dla kopii zapasowej zaszyfrowanego maszyn wirtualnych. | Kopii zapasowej należy podawać te uprawnienia, postępując [kroki opisane w poprzedniej sekcji](#provide-permissions-to-azure-backup). Lub możesz wykonać kroki programu PowerShell w sekcji "Włącz ochronę" w dokumentacji programu PowerShell w [AzureRM.RecoveryServices.Backup użyj poleceń cmdlet narzędzia do tworzenia kopii zapasowych maszyn wirtualnych](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Przywracanie |Nie można przywrócić tej zaszyfrowanej maszyny Wirtualnej, ponieważ nie istnieje magazyn kluczy, skojarzone z tej maszyny Wirtualnej. |Tworzenie magazynu kluczy za pomocą [wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md). Zobacz [przywrócić klucz magazynu kluczy oraz klucz tajny przy użyciu usługi Kopia zapasowa Azure](backup-azure-restore-key-secret.md) do przywrócenia klucza i klucz tajny, jeśli nie są dostępne. |
| Przywracanie |Nie można przywrócić tej zaszyfrowanej maszyny Wirtualnej, ponieważ nie ma klucza i klucz tajny skojarzony z tej maszyny Wirtualnej. |Zobacz [przywrócić klucz magazynu kluczy oraz klucz tajny przy użyciu usługi Kopia zapasowa Azure](backup-azure-restore-key-secret.md) do przywrócenia klucza i klucz tajny, jeśli nie są dostępne. |
| Przywracanie |Kopia zapasowa nie ma autoryzacji do dostępu do zasobów w ramach subskrypcji. |Jak wspomniano wcześniej, Przywróć dysków najpierw wykonując kroki opisane w sekcji "Przywracanie kopii zapasowej dysków" [wybierz konfigurację przywracania maszyny Wirtualnej](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Po tym, za pomocą programu PowerShell do [utworzyć Maszynę wirtualną z dysków przywróconej](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
