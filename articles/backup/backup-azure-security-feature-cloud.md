---
title: Funkcje zabezpieczeń ułatwiające ochronę obciążeń w chmurze
description: Dowiedz się, jak korzystać z funkcji zabezpieczeń w usłudze Azure Backup, aby uczynić kopie zapasowe bezpieczniejszymi.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668738"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funkcje zabezpieczeń ułatwiające ochronę obciążeń w chmurze korzystających z usługi Azure Backup

Obawy dotyczące problemów z bezpieczeństwem, takich jak złośliwe oprogramowanie, ransomware i włamania, są coraz większe. Te problemy z bezpieczeństwem mogą być kosztowne, zarówno pod względem pieniędzy, jak i danych. Aby zabezpieczyć się przed takimi atakami, usługa Azure Backup udostępnia teraz funkcje zabezpieczeń ułatwiające ochronę danych kopii zapasowej nawet po usunięciu.

Jedną z takich funkcji jest usuwanie nietrwałe. Po usunięciu nietrwałym, nawet jeśli złośliwy aktor usunie kopię zapasową maszyny Wirtualnej (lub dane kopii zapasowej zostaną przypadkowo usunięte), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwania nietrwałe" nie ponosi żadnych kosztów dla klienta. Platforma Azure szyfruje również wszystkie kopie zapasowe danych w spoczynku przy użyciu [szyfrowania usługi storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) w celu dalszego zabezpieczania danych.

Ochrona przed usuwaniem nietrwałym dla maszyn wirtualnych platformy Azure jest ogólnie dostępna.

>[!NOTE]
>Usuwanie trędowak dla serwera SQL w usłudze Azure VM i usuwanie nietrwałe dla sap HANA w obciążeniach maszyn wirtualnych platformy Azure jest teraz dostępne w wersji zapoznawczej.<br>
>Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Usuwanie nietrwałe

### <a name="soft-delete-for-vms"></a>Usuwanie nietrwałe dla maszyn wirtualnych

Usuwanie nietrwałe dla maszyn wirtualnych chroni kopie zapasowe maszyn wirtualnych przed niezamierzonych usunięcia. Nawet po usunięciu kopii zapasowych są one zachowywane w stanie usuwania nietrwałego przez 14 dodatkowych dni.

> [!NOTE]
> Usuwanie nietrwałe chroni tylko usunięte dane kopii zapasowej. Jeśli maszyna wirtualna zostanie usunięta bez kopii zapasowej, funkcja usuwania nie będzie zachowywać danych. Wszystkie zasoby powinny być chronione za pomocą usługi Azure Backup, aby zapewnić pełną odporność.
>

### <a name="supported-regions"></a>Obsługiwane regiony

Usuwanie miękkie jest obecnie obsługiwane w zachodnio-środkowych stanach USA, Azji Wschodniej, Kanadzie Środkowej, Kanadzie Wschodniej, Francji Środkowej, Francji Południowej, Korei Środkowej, Korei Południowej, Południowej, Południowej, Południowej, Wielkiej Brytanii, Australii Zachodniej, Australii Południowo-Wschodniej, Europie Północnej, Zachodnich Stanach Zjednoczonych, Zachodnich Stanach Zjednoczonych, Azji Środkowej, Azji Południowo-Wschodniej, Północno-Środkowej USA, Południowo-Środkowo-Środkowych Stanach Zjednoczonych, Japonii Wschodniej, Japonii Zachodniej, Indiach Południowych, Indiach Południowych, Indiach Zachodnich, Indiach Zachodnich, Wschodnich Stanach Zjednoczonych 2 , Szwajcaria Północ, Szwajcaria Zachód, Norwegia Zachodnia, Norwegia Wschodnia i wszystkie regiony narodowe.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu witryny Azure portal

1. Aby usunąć dane kopii zapasowej maszyny Wirtualnej, należy zatrzymać kopię zapasową. W witrynie Azure portal przejdź do magazynu usług odzyskiwania, kliknij prawym przyciskiem myszy element kopii zapasowej i wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

   ![Zrzut ekranu przedstawiający elementy kopii zapasowej witryny Azure portal](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. W poniższym oknie otrzymasz możliwość usunięcia lub zachowania danych kopii zapasowej. Jeśli wybierzesz **opcję Usuń dane kopii zapasowej,** a następnie **Zatrzymaj kopię zapasową,** kopia zapasowa maszyny Wirtualnej nie zostanie trwale usunięta. Zamiast tego dane kopii zapasowej będą przechowywane przez 14 dni w stanie nietrwałego usunięcia. Jeśli wybrano **opcję Usuń dane kopii zapasowej,** do skonfigurowanego identyfikatora wiadomości e-mail jest wysyłany alert o usunięciu wiadomości e-mail informujący użytkownika, że pozostało 14 dni dłuższego przechowywania danych kopii zapasowej. Ponadto 12 dnia wysyłany jest alert e-mail informujący, że pozostało jeszcze dwa dni na wskrzeszenie usuniętych danych. Usunięcie jest odroczone do 15 dnia, kiedy nastąpi trwałe usunięcie i zostanie wysłany ostatni alert e-mail informujący o trwałym usunięciu danych.

   ![Zrzut ekranu przedstawiający witrynę Azure portal, ekran Stop kopii zapasowej](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. W ciągu tych 14 dni w magazynie usług odzyskiwania nietrwale usunięta maszyna wirtualna pojawi się z czerwoną ikoną "soft-delete" obok niej.

   ![Zrzut ekranu przedstawiający witrynę Azure portal, maszynę wirtualną w stanie usuwania nietrwałego](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Jeśli w przechowalni znajdują się wszystkie elementy kopii zapasowej usunięte z nietrwale, nie można go w tym czasie usunąć. Spróbuj usunąć przechowalnię po trwałym usunięciu elementów kopii zapasowej, a w przechowalni nie ma żadnego elementu w stanie nietrwałego usunięcia.

4. Aby przywrócić nietrwale usunięte maszyny Wirtualnej, należy najpierw cofnąć. Aby cofnąć usunięcie, wybierz nietrwale usuniętą maszynę wirtualną, a następnie wybierz opcję **Cofnij usunięcie**.

   ![Zrzut ekranu przedstawiający witrynę Azure portal, cofanie usuniania maszyny wirtualnej](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Pojawi się okno z ostrzeżeniem, że jeśli zostanie wybrana cofnięta ściągnienie, wszystkie punkty przywracania maszyny Wirtualnej zostaną cofnięte i będą dostępne do wykonania operacji przywracania. Maszyna wirtualna zostanie zachowana w stanie "zatrzymaj ochronę z zachowaniem danych" z wstrzymane kopie zapasowe i dane kopii zapasowej przechowywane na zawsze bez zasad tworzenia kopii zapasowych skuteczne.

   ![Zrzut ekranu przedstawiający witrynę Azure portal, Confirm undelete VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   W tym momencie można również przywrócić maszynę wirtualną, wybierając **przywróć maszynę wirtualną** z wybranego punktu przywracania.  

   ![Zrzut ekranu przedstawiający witrynę Azure portal, opcja Przywróć maszynę wirtualną](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Moduł zbierający elementy bezużyteczne będzie działać i czyścić wygasłe punkty odzyskiwania tylko wtedy, gdy użytkownik wykona operację **wznowienia tworzenia kopii zapasowej.**

5. Po zakończeniu procesu cofania usunięcia stan powróci do stanu "Zatrzymaj tworzenie kopii zapasowej z zachowaniem danych", a następnie możesz wybrać **wznawianie kopii zapasowej**. Operacja **wznowienia tworzenia kopii zapasowej** przywraca element kopii zapasowej w stanie aktywnym, skojarzony z zasadami tworzenia kopii zapasowych wybranymi przez użytkownika definiującego harmonogramy tworzenia kopii zapasowych i przechowywania.

   ![Zrzut ekranu przedstawiający witrynę Azure portal, opcję Wznów kopię zapasową](./media/backup-azure-security-feature-cloud/resume-backup.png)

Ten schemat blokowy przedstawia różne kroki i stany elementu kopii zapasowej po włączeniu usuwania nietrwałego:

![Cykl życia elementu kopii zapasowej usuniętego nietrwale](./media/backup-azure-security-feature-cloud/lifecycle.png)

Aby uzyskać więcej informacji, zobacz sekcję [Często zadawane pytania](backup-azure-security-feature-cloud.md#frequently-asked-questions) poniżej.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu programu Azure PowerShell

> [!IMPORTANT]
> Wersja Az.RecoveryServices wymagana do użycia usuwania nietrwałego przy użyciu usługi Azure PS jest min 2.2.0. Użyj, ```Install-Module -Name Az.RecoveryServices -Force``` aby uzyskać najnowszą wersję.

Jak opisano powyżej dla witryny Azure portal, sekwencja kroków jest taka sama podczas korzystania z programu Azure PowerShell, jak również.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Usuwanie elementu kopii zapasowej przy użyciu programu Azure PowerShell

Usuń element kopii zapasowej przy użyciu polecenia cmdlet [PS Disable-AzRecoveryServiceBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

"DeleteState" elementu kopii zapasowej zmieni się z "NotDeleted" na "ToBeDeleted". Dane kopii zapasowej będą przechowywane przez 14 dni. Jeśli chcesz przywrócić operację usuwania, należy cofnąć usunięcie.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Cofanie operacji usuwania przy użyciu programu Azure PowerShell

Najpierw pobierz odpowiedni element kopii zapasowej, który jest w stanie usuwania nietrwałego (czyli wkrótce zostanie usunięty).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Następnie wykonaj operację cofania usuwania przy użyciu polecenia cmdlet [PS Undo-AzRecoveryServicesBackupItemDeletion.](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0)

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

"DeleteState" elementu kopii zapasowej powróci do "NotDeleted". Ale ochrona jest nadal zatrzymana. [Wznowić kopię zapasową,](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) aby ponownie włączyć ochronę.

### <a name="soft-delete-for-vms-using-rest-api"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu interfejsu API REST

- Usuń kopie zapasowe za pomocą interfejsu API REST, jak wspomniano [tutaj](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Jeśli użytkownik chce cofnąć te operacje usuwania, zapoznaj się z krokami wymienionymi [w tym miejscu](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Wyłączanie usuwania nietrwałego

Usuwanie nietrwałe jest domyślnie włączone w nowo utworzonych magazynach w celu ochrony danych kopii zapasowej przed przypadkowymi lub złośliwymi usunięciami.  Wyłączenie tej funkcji nie jest zalecane. Jedyną okolicznością, w której należy rozważyć wyłączenie usuwania nietrwałego, jest przeniesienie chronionych elementów do nowego magazynu i nie możesz czekać 14 dni wymaganych przed usunięciem i ponownym usunięciem (na przykład w środowisku testowym). Tylko właściciel przechowalni może wyłączyć tę funkcję. Jeśli ta funkcja zostanie wyłączona, wszystkie przyszłe usunięcia chronionych elementów spowoduje natychmiastowe usunięcie, bez możliwości przywrócenia. Dane kopii zapasowej, które istnieją w stanie nietrwale usuniętych przed wyłączeniem tej funkcji, pozostaną w stanie nietrwale usuniętym przez okres 14 dni. Jeśli chcesz trwale usunąć je natychmiast, musisz cofnąć i usunąć je ponownie, aby uzyskać trwałe usunięcie.

### <a name="disabling-soft-delete-using-azure-portal"></a>Wyłączanie usuwania nietrwałego przy użyciu witryny Azure portal

Aby wyłączyć usuwanie nietrwałe, wykonaj następujące czynności:

1. W witrynie Azure portal przejdź do magazynu, a następnie przejdź do**właściwości** **ustawień** -> .
2. W okienku właściwości wybierz pozycję**Aktualizacja** **ustawień** -> zabezpieczeń .  
3. W okienku ustawień zabezpieczeń w obszarze **Usuwanie programowe**wybierz pozycję **Wyłącz**.

![Wyłącz usuwanie nietrwałe](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Wyłączanie usuwania nietrwałego przy użyciu programu Azure PowerShell

> [!IMPORTANT]
> Wersja Az.RecoveryServices wymagana do użycia usuwania nietrwałego przy użyciu usługi Azure PS jest min 2.2.0. Użyj, ```Install-Module -Name Az.RecoveryServices -Force``` aby uzyskać najnowszą wersję.

Aby wyłączyć, należy użyć polecenia cmdlet [PS Set-AzRecoveryServicesVaultBackupProperty.](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0)

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Wyłączanie usuwania nietrwałego przy użyciu interfejsu API REST

Aby wyłączyć funkcję usuwania nietrwałego za pomocą interfejsu REST API, zapoznaj się z wymienionymi [tutaj](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)krokami .

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Trwale usuwanie nietrwale usuniętych elementów kopii zapasowej

Dane kopii zapasowej w stanie nietrwale usuniętym przed wyłączeniem tej funkcji pozostaną w stanie nietrwale usuniętym. Jeśli chcesz je natychmiast usunąć, cofnij i usuń je ponownie, aby zostać trwale usunięte.

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Wykonaj następujące kroki:

1. Wykonaj kroki, aby [wyłączyć usuwanie nietrwałe](#disabling-soft-delete).
2. W witrynie Azure portal przejdź do magazynu, przejdź do **pozycji Elementy kopii zapasowej**i wybierz nietrwale usuniętą maszynę wirtualną.

   ![Wybieranie nietrwale usuniętej maszyny Wirtualnej](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Wybierz opcję **Cofnij usunięcie**.

   ![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Pojawi się okno. Wybierz **pozycję Cofnij usunięcie**.

   ![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Wybierz **pozycję Usuń dane kopii zapasowej,** aby trwale usunąć dane kopii zapasowej.

   ![Wybierz pozycję Usuń dane kopii zapasowej](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Wpisz nazwę elementu kopii zapasowej, aby potwierdzić, że chcesz usunąć punkty odzyskiwania.

   ![Wpisz nazwę elementu kopii zapasowej](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Aby usunąć dane kopii zapasowej elementu, wybierz pozycję **Usuń**. Komunikat z powiadomieniem informuje, że dane kopii zapasowej zostały usunięte.

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Jeśli elementy zostały usunięte przed soft-delete został wyłączony, a następnie będą one w stanie nietrwałe usunięte. Aby natychmiast je usunąć, operacja usuwania musi być odwrócona, a następnie wykonana ponownie.

Zidentyfikuj elementy, które są w stanie nietrwale usuniętym.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Następnie odwróć operację usuwania, która została wykonana po włączeniu usuwania nietrwałego.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Ponieważ usuwanie nietrwałe jest teraz wyłączone, operacja usuwania spowoduje natychmiastowe usunięcie danych kopii zapasowej.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST

Jeśli elementy zostały usunięte przed soft-delete został wyłączony, a następnie będą one w stanie nietrwałe usunięte. Aby natychmiast je usunąć, operacja usuwania musi być odwrócona, a następnie wykonana ponownie.

1. Najpierw cofnij operacje usuwania za pomocą kroków wymienionych [w tym miejscu](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Następnie wyłącz funkcję usuwania nietrwałego za pomocą interfejsu REST API, wykonując kroki wymienione [tutaj](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Następnie usuń kopie zapasowe za pomocą interfejsu API REST, jak wspomniano [tutaj](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Szyfrowanie

Wszystkie kopie zapasowe danych są automatycznie szyfrowane podczas przechowywania w chmurze przy użyciu szyfrowania usługi Azure Storage, które pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności. Te dane w stanie spoczynku są szyfrowane przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2.

Oprócz szyfrowania w spoczynku wszystkie przesyłane dane kopii zapasowej są przesyłane za pośrednictwem protokołu HTTPS. Zawsze pozostaje w sieci szkieletowej platformy Azure.

Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage dla danych w spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Zapoznaj się z [często zadawanymi pytaniami dotyczącymi kopii zapasowej platformy Azure,](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) aby odpowiedzieć na wszelkie pytania dotyczące szyfrowania.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowych przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Nie musisz podejmować żadnych jawnych działań od końca, aby włączyć to szyfrowanie i dotyczy wszystkich obciążeń, których kopię zapasową jest w magazynie usług odzyskiwania.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowych przy użyciu kluczy zarządzanych przez klienta

Podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure można teraz szyfrować dane przy użyciu kluczy będących własnością użytkownika i zarządzanych przez ciebie. Usługa Azure Backup umożliwia używanie kluczy RSA przechowywanych w usłudze Azure Key Vault do szyfrowania kopii zapasowych. Klucz szyfrowania używany do szyfrowania kopii zapasowych może się różnić od klucza używanego dla źródła. Dane są chronione za pomocą klucza szyfrowania danych opartego na AES 256 (DEK), który z kolei jest chroniony za pomocą kluczy. Daje to pełną kontrolę nad danymi i kluczami. Aby zezwolić na szyfrowanie, wymagane jest, aby magazyn usług odzyskiwania uzyskał dostęp do klucza szyfrowania w magazynie azure key vault. Klucz można wyłączyć lub odwołać w razie potrzeby. Jednak przed podjęciem próby ochrony elementów do przechowalni należy włączyć szyfrowanie przy użyciu kluczy.

>[!NOTE]
>Ta funkcja jest obecnie w ograniczonej dostępności. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) i AskAzureBackupTeam@microsoft.com napisz do nas, jeśli chcesz zaszyfrować dane kopii zapasowej za pomocą kluczy zarządzanych przez klienta. Należy zauważyć, że możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Tworzenie kopii zapasowych maszyn wirtualnych z dysku zarządzanego zaszyfrowanych przy użyciu kluczy zarządzanych przez klienta

Usługa Azure Backup umożliwia również tworzenie kopii zapasowych maszyn wirtualnych platformy Azure, które używają klucza do szyfrowania po stronie serwera. Klucz używany do szyfrowania dysków jest przechowywany w magazynie azure key vault i zarządzany przez Ciebie. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta różni się od szyfrowania dysków Azure, ponieważ usługa ADE wykorzystuje funkcje BitLocker (dla systemu Windows) i DM-Crypt (dla systemu Linux) do szyfrowania w trybie rzeczywistym, SSE szyfruje dane w usłudze magazynu, umożliwiając używanie dowolnego systemu operacyjnego lub obrazów dla maszyn wirtualnych. Aby uzyskać więcej informacji, zapoznaj się [z informacjami dotyczącymi szyfrowania dysków zarządzanych za pomocą kluczy zarządzanych](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) przez klienta.

### <a name="backup-of-vms-encrypted-using-ade"></a>Tworzenie kopii zapasowych maszyn wirtualnych zaszyfrowanych przy użyciu usługi ADE

Za pomocą usługi Azure Backup można również wykonać kopię zapasową maszyn wirtualnych platformy Azure, które mają swoje dyski systemu operacyjnego lub dysków danych zaszyfrowanych przy użyciu szyfrowania dysków azure. Usługa ADE używa funkcji BitLocker dla maszyn wirtualnych z systemem Windows i maszyn wirtualnych DM-Crypt dla systemu Linux do szyfrowania gościa. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie kopii zapasowych i przywracanie zaszyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Inne funkcje zabezpieczeń

### <a name="protection-of-azure-backup-recovery-points"></a>Ochrona punktów odzyskiwania kopii zapasowej platformy Azure

Konta magazynu używane przez magazyny usług odzyskiwania są izolowane i nie mogą być dostępne dla użytkowników w złośliwych celach. Dostęp jest dozwolony tylko za pośrednictwem operacji zarządzania pomocą usługi Azure Backup, takich jak przywracanie. Te operacje zarządzania są kontrolowane za pomocą kontroli dostępu opartej na rolach (RBAC).

Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami odzyskiwania kopii zapasowej usługi Azure za pomocą kontroli dostępu opartej na rolach.](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="for-soft-delete"></a>W przypadku usuwania nietrwałego

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Czy muszę włączyć funkcję usuwania nietrwałego w każdym magazynie?

Nie, jest ono domyślnie wbudowane i włączone dla wszystkich magazynów usług odzyskiwania.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Czy mogę skonfigurować liczbę dni, przez które moje dane będą przechowywane w stanie nietrwałym po zakończeniu operacji usuwania?

Nie, jest on akcesorowany do 14 dni dodatkowego przechowywania po operacji usuwania.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Czy muszę ponieść koszty tego dodatkowego 14-dniowego przechowywania?

Nie, ta 14-dniowa dodatkowa retencja jest bezpłatna w ramach funkcji usuwania nietrwałego.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Czy można wykonać operację przywracania, gdy moje dane są w stanie nietrwałego usuwania?

Nie, aby przywrócić, należy cofnąć usunięcie nietrwalonego zasobu. Operacja cofania usunięcia spowoduje przywrócenie zasobu do **ochrony zatrzymania z zachowaniem stanu danych,** w którym można przywrócić do dowolnego punktu w czasie. Moduł zbierający elementy bezużyteczne pozostaje wstrzymany w tym stanie.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Czy moje migawki będą przebiegać według tego samego cyklu życia, co punkty odzyskiwania w przechowalni?

Tak.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak ponownie wyzwolić zaplanowane kopie zapasowe dla nietrwałego zasobu?

Cofanie usunięcia, a następnie operacja wznawiania, ponownie będzie chronić zasób. Operacja Wznawiania kojarzy zasady tworzenia kopii zapasowych w celu wyzwolenia zaplanowanych kopii zapasowych z wybranym okresem przechowywania. Ponadto moduł zbierający elementy bezużyteczne działa natychmiast po zakończeniu operacji wznowienia. Jeśli chcesz wykonać przywracanie z punktu odzyskiwania, który jest po jego dacie wygaśnięcia, zaleca się to zrobić przed wyzwoleniem operacji wznowienia.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Czy mogę usunąć przechowalnię, jeśli w przechowalni znajdują się nietrwale usunięte elementy?

Nie można usunąć magazynu usług odzyskiwania, jeśli w przechowalni znajdują się elementy kopii zapasowej w stanie nietrwale usuniętym. Elementy usunięte nietrwale są trwale usuwane 14 dni po operacji usuwania. Jeśli nie możesz czekać 14 dni, [wyłącz usuwanie nietrwałe](#disabling-soft-delete), cofnij usunięcie usuniętych nietrwałych elementów i usuń je ponownie, aby trwale zostać usunięte. Po upewnieniu się, że nie ma żadnych elementów chronionych i nie ma nietrwałych usuniętych elementów, przechowalnia może zostać usunięta.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Czy mogę usunąć dane wcześniej niż 14-dniowy okres usuwania nietrwałego po usunięciu?

Nie. Nie można wymusić usunięcia usuniętych elementów, które zostaną automatycznie usunięte po 14 dniach. Ta funkcja zabezpieczeń jest włączona w celu ochrony kopii zapasowych danych przed przypadkowymi lub złośliwymi usunięciami.  Należy poczekać na 14 dni przed wykonaniem jakiejkolwiek innej akcji na maszynie wirtualnej.  Nieuiszczone elementy zostaną obciążone.  Jeśli potrzebujesz ponownego przekroczenie ceł maszyn wirtualnych oznaczonych do usuwania nietrwałego w ciągu 14 dni do nowego magazynu, skontaktuj się z pomocą techniczną firmy Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Czy operacje usuwania nietrwałego mogą być wykonywane w programie PowerShell lub CLI?

Operacje usuwania nietrwałego można wykonywać za pomocą programu [PowerShell](#soft-delete-for-vms-using-azure-powershell). Obecnie cli nie jest obsługiwany.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Czy usuwanie nietrwałe jest obsługiwane dla innych obciążeń w chmurze, takich jak SQL Server na maszynach wirtualnych platformy Azure i SAP HANA na maszynach wirtualnych platformy Azure?

Nie. Obecnie usuwanie nietrwałe jest obsługiwane tylko dla maszyn wirtualnych platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [kontrolach zabezpieczeń dla usługi Azure Backup](backup-security-controls.md).
