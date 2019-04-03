---
title: Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup
description: Ten artykuł zawiera informacje o kopii zapasowej i przywracania środowiska dla maszyny wirtualne szyfrowane przy użyciu usługi Azure Disk Encryption.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: geetha
ms.openlocfilehash: 28126df0dfd9a03e93a76fa5071331603c4819a4
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851021"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup
Ten artykuł zawiera informacje o kroki tworzenia kopii zapasowej i przywracanie maszyn wirtualnych (VM) przy użyciu usługi Azure Backup. Zapewnia także szczegółowe informacje o obsługiwanych scenariuszach, wymagania wstępne i kroki rozwiązywania problemów w przypadku wystąpienia błędów.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

 Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych jest obsługiwana tylko dla maszyn wirtualnych, które używają modelu wdrażania usługi Azure Resource Manager. Nie jest obsługiwana dla maszyn wirtualnych, które używają klasycznego modelu wdrażania. Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych jest obsługiwana dla Windows i maszyn wirtualnych systemu Linux, używanego przez usługi Azure Disk Encryption. Disk Encryption korzysta z branży standardowych funkcji BitLocker Windows oraz funkcji dm-crypt systemu Linux, aby zapewnić szyfrowanie dysków. W poniższej tabeli przedstawiono typ szyfrowania i pomocy technicznej dla maszyn wirtualnych.

   |  | BEK i KEK maszyn wirtualnych | Klucz szyfrowania bloków — tylko do maszyn wirtualnych |
   | --- | --- | --- |
   | **Niezarządzanych maszyn wirtualnych**  | Yes | Yes  |
   | **Zarządzane maszyny wirtualne**  | Yes | Yes  |

   > [!NOTE]
   > Usługa Azure Backup obsługuje maszyny wirtualne szyfrowane przy użyciu kluczy autonomicznych. Dowolny klawisz, który jest częścią certyfikat używany do szyfrowania maszyny Wirtualnej nie jest obecnie obsługiwane.
   >

## <a name="prerequisites"></a>Wymagania wstępne
* Maszyny Wirtualnej został zaszyfrowany za pomocą [usługi Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Magazyn usługi Recovery Services został utworzony, a replikacja magazynu została ustawiona, wykonując kroki opisane w [przygotowania środowiska do tworzenia kopii zapasowych](backup-azure-arm-vms-prepare.md).

* Kopia zapasowa podano uprawnień dostępu magazynu kluczy zawierającego kluczy i wpisów tajnych dla szyfrowanych maszyn wirtualnych.

## <a name="back-up-an-encrypted-vm"></a>Tworzenie kopii zapasowej zaszyfrowanej maszyny Wirtualnej
Ustawienie cel kopii zapasowej, definiowanie zasad, skonfigurować elementy i wyzwalanie tworzenia kopii zapasowej, wykonaj następujące kroki.

### <a name="configure-backup"></a>Konfigurowanie kopii zapasowych
1. Jeśli masz już magazyn usługi Recovery Services, Otwórz, przejdź do następnego kroku. Jeśli nie masz magazyn usługi Recovery Services, Otwórz, ale znajdujesz się w witrynie Azure portal, wybierz opcję **wszystkich usług**.

   a. Na liście zasobów wpisz **Usługi odzyskiwania**.

   b. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Po wyświetleniu **Magazyny usługi Recovery Services**, wybierz ją.

      ![Magazyn usługi Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Zostanie wyświetlona lista magazynów Usług odzyskiwania. Wybierz magazyn z listy.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
1. Wybierz z listy elementów, która pojawia się w magazynie, **kopii zapasowej** można uruchomić tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych.

      ![Blok tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/select-backup.png)
1. Na **kopii zapasowej** kafelka, wybierz opcję **cel kopii zapasowej**.

      ![Bloku scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. W obszarze **gdzie jest uruchomione Twoje obciążenie?**, wybierz opcję **Azure**. W obszarze **co chcesz utworzyć kopię zapasową?**, wybierz opcję **maszyny wirtualnej**. Następnie wybierz przycisk **OK**.

   ![Otwarcie bloku scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. W obszarze **wybierz zasady tworzenia kopii zapasowej**, Wybieranie zasad kopii zapasowych, który chcesz zastosować do magazynu. Następnie wybierz przycisk **OK**.

      ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Szczegóły domyślnych zasad znajdują się. Aby utworzyć zasady, należy zaznaczyć **Utwórz nowy** z listy rozwijanej. Po wybraniu **OK**, zasad tworzenia kopii zapasowej jest skojarzone z magazynem.

1. Wybierz zaszyfrowanych maszyn wirtualnych do skojarzenia z określonymi zasadami i wybierz **OK**.

      ![Wybierz zaszyfrowanych maszyn wirtualnych](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. Ta strona zawiera komunikat o magazynów kluczy skojarzone z zaszyfrowanych maszyn wirtualnych, wybrane. Kopia zapasowa wymaga dostęp tylko do odczytu do kluczy i wpisów tajnych w magazynie kluczy. Aby utworzyć kopię zapasową kluczy i wpisów tajnych, wraz z skojarzonych maszynach wirtualnych używa tych uprawnień.<br>
Jeśli jesteś **użytkownika elementu członkowskiego**, proces Włącz wykonywanie kopii zapasowej bezproblemowo uzyska dostęp do magazynu kluczy do tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych bez konieczności żadnej interwencji użytkownika.

   ![Zaszyfrowanego komunikatu maszyn wirtualnych](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Aby uzyskać **użytkownik-Gość**, musisz podać uprawnienia dostępu magazynu kluczy dla kopii zapasowych do pracy z usługą kopii zapasowej. Możesz podać te uprawnienia, wykonując kroki opisane w poniższej sekcji

   ![Zaszyfrowanego komunikatu maszyn wirtualnych](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    Skoro zdefiniowano wszystkie ustawienia magazynu, wybierz **Włącz wykonywanie kopii zapasowej** w dolnej części strony. **Włącz wykonywanie kopii zapasowej** wdraża zasady dla magazynu i maszyn wirtualnych.

1. Do następnej fazy przygotowania jest zainstalowanie agenta maszyny Wirtualnej lub upewnij się, że Agent maszyny Wirtualnej jest zainstalowany. Taki sam, wykonaj kroki opisane w [przygotowania środowiska do tworzenia kopii zapasowych](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Wyzwalanie zadania tworzenia kopii zapasowej
Postępuj zgodnie z instrukcjami w [kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services](backup-azure-arm-vms.md) wyzwalanie zadania tworzenia kopii zapasowej.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Kontynuuj kopii zapasowych maszyn wirtualnych już kopii zapasowej jest włączone szyfrowanie  
W przypadku maszyn wirtualnych trwa już wykonywanie kopii zapasowej w magazynie usługi Recovery Services, które są włączone dla później szyfrowania musi mieć uprawnienia do tworzenia kopii do dostępu do klucza magazynu kopii zapasowych kontynuować. Możesz podać te uprawnienia, wykonując [kroki opisane w poniższej sekcji](#provide-permissions). Możesz też wykonać kroki programu PowerShell w sekcji "Włącz kopię zapasową" [dokumentacji programu PowerShell](backup-azure-vms-automation.md).

## <a name="provide-permissions"></a>Udostępnienie uprawnień
Wykonaj następujące kroki, aby zapewnić odpowiednie uprawnienia dla usługi Azure Backup dostęp do usługi key vault oraz wykonania kopii zapasowej zaszyfrowanych maszyn wirtualnych.
1. Wybierz **wszystkich usług**i wyszukaj **klucza magazynów**.

    ![Magazyny kluczy](./media/backup-azure-vms-encryption/search-key-vault.png)

1. Z listy magazynów kluczy wybierz magazyn kluczy skojarzone z zaszyfrowanych maszyn wirtualnych, które należy utworzyć kopię.

     ![Wybór usługi Key vault](./media/backup-azure-vms-encryption/select-key-vault.png)

1. Wybierz **zasady dostępu**, a następnie wybierz pozycję **Dodaj nowe**.

    ![Dodaj nowe](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. Wybierz **Wybierz podmiot zabezpieczeń**, a następnie wpisz **usługi zarządzania Backup** w polu wyszukiwania.

    ![Wyszukiwanie usługi kopii zapasowej](./media/backup-azure-vms-encryption/search-backup-service.png)

1. Wybierz **usługi zarządzania Backup**, a następnie wybierz pozycję **wybierz**.

    ![Wybieranie kopii zapasowej usługi](./media/backup-azure-vms-encryption/select-backup-service.png)

1. W obszarze **Konfiguruj z szablonu (opcjonalnie)**, wybierz opcję **kopia zapasowa Azure**. Wymagane uprawnienia są wstępnie dla **uprawnienia klucza** i **uprawnienia klucza tajnego**. Jeśli maszyna wirtualna jest zaszyfrowana przy użyciu **tylko bloków**, uprawnień tylko do wpisów tajnych są wymagane, dlatego należy usunąć wybranych dla **uprawnienia klucza**.

    ![Wybranej kopii zapasowej platformy Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Kliknij przycisk **OK**. Należy zauważyć, że **usługi zarządzania Backup** dodaniu w **zasady dostępu**.

    ![Zasady dostępu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Wybierz **Zapisz** udzielenia wymaganych uprawnień do wykonywania kopii zapasowych.

    ![Zasady tworzenia kopii zapasowej dostępu](./media/backup-azure-vms-encryption/save-access-policy.png)

Po pomyślnie podano uprawnienia, możesz kontynuować włączania kopii zapasowej zaszyfrowanych maszyn wirtualnych.

## <a name="restore-an-encrypted-vm"></a>Przywracanie zaszyfrowanych maszyn wirtualnych
Usługa Azure Backup obsługuje teraz Przywracanie [Azure zaszyfrowanych maszyn wirtualnych bez usługi Azure AD](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad) oprócz poprzedniej oferty przywracania pomocy technicznej Azure zaszyfrowanych maszyn wirtualnych z usługą Azure AD.<br>

Przywrócić zaszyfrowaną maszynę Wirtualną, najpierw Przywróć dyski wykonując kroki opisane w sekcji "Przywracanie dysków kopii zapasowej" [wybierz konfigurację przywracania maszyny Wirtualnej](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Po tym można użyć jednej z następujących opcji:

* Wykonaj kroki programu PowerShell [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) tworzenie pełnej maszyny Wirtualnej z przywróconych dysków.
* Ewentualnie [dostosować przywróconej maszyny Wirtualnej przy użyciu szablonów](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) do tworzenia maszyn wirtualnych z przywróconych dysków. Szablony mogą być używane tylko w przypadku punkty odzyskiwania utworzone po 26 kwietnia 2017 r.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Operacja | Szczegóły błędu | Rozwiązanie |
| --- | --- | --- |
|Backup | Kod błędu: UserErrorKeyVaultPermissionsNotConfigured<br><br>Komunikat o błędzie: Usługa Azure Backup nie ma wystarczających uprawnień do usługi Key Vault dla kopii zapasowej zaszyfrowanych maszyn wirtualnych. | Kopii zapasowej należy podać te uprawnienia, postępując [kroki opisane w poprzedniej sekcji](#provide-permissions). Możesz też wykonać kroki programu PowerShell w sekcji "Włącz ochronę" tego artykułu [Użyj programu PowerShell, tworzenia kopii zapasowej i przywracanie maszyn wirtualnych](backup-azure-vms-automation.md#enable-protection). |  
| Przywracanie | Nie można przywrócić tej zaszyfrowanej maszyny Wirtualnej, ponieważ nie istnieje magazyn kluczy skojarzone z tą maszyną Wirtualną. |Tworzenie magazynu kluczy przy użyciu [co to jest usługa Azure Key Vault?](../key-vault/key-vault-overview.md). Zobacz [przywrócić klucz usługi key vault i klucz tajny przy użyciu usługi Azure Backup](backup-azure-restore-key-secret.md) można przywrócić klucza i wpisu tajnego, jeśli nie są obecne. |
| Przywracanie | Kod błędu: UserErrorKeyVaultKeyDoesNotExist<br><br> Komunikat o błędzie: Nie możesz przywrócić tej zaszyfrowanej maszyny Wirtualnej, ponieważ klucz skojarzony z tą maszyną Wirtualną nie istnieje. |Zobacz [przywrócić klucz usługi key vault i klucz tajny przy użyciu usługi Azure Backup](backup-azure-restore-key-secret.md) można przywrócić klucza i wpisu tajnego, jeśli nie są obecne. |
| Przywracanie | Kod błędu: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>Komunikat o błędzie: Usługa Kopia zapasowa nie ma autoryzacji umożliwiającej dostęp do zasobów w Twojej subskrypcji. |Jak wspomniano wcześniej, Przywróć dyski najpierw wykonując kroki opisane w sekcji "Przywracanie dysków kopii zapasowej" [wybierz konfigurację przywracania maszyny Wirtualnej](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Po tym, za pomocą programu PowerShell do [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
