---
title: Tworzenie kopii zapasowej Azure Files przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową Azure Files przy użyciu usługi Azure Backup i programu PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5147ab893d4ebad395d7dbd8cc25872177ec10a2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773101"
---
# <a name="back-up-azure-files-with-powershell"></a>Tworzenie kopii zapasowej Azure Files przy użyciu programu PowerShell

W tym artykule opisano sposób użycia Azure PowerShell do tworzenia kopii zapasowej Azure Files udziału plików przy użyciu magazynu [Azure Backup](backup-overview.md) Recovery Services.

W tym artykule wyjaśniono, jak:

> [!div class="checklist"]
>
> * Skonfiguruj program PowerShell i zarejestruj dostawcę usługi Azure Recovery Services.
> * Utwórz magazyn usługi Recovery Services.
> * Skonfiguruj kopię zapasową udziału plików platformy Azure.
> * Uruchom zadanie tworzenia kopii zapasowej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach Recovery Services.
* Przeczytaj o możliwościach [tworzenia kopii zapasowych udziałów plików platformy Azure](backup-afs.md).
* Zapoznaj się z hierarchią obiektów programu PowerShell dla Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów Recovery Services

Hierarchia obiektów jest podsumowana na poniższym diagramie.

![Hierarchia obiektów Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zapoznaj się z dokumentacją [polecenia cmdlet](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** w bibliotece platformy Azure.

## <a name="set-up-and-install"></a>Skonfiguruj i zainstaluj

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skonfiguruj program PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję polecenia AZ PowerShell](/powershell/azure/install-az-ps). Minimalna wymagana wersja to 1.0.0.

2. Znajdź Azure Backup poleceń cmdlet programu PowerShell za pomocą tego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zapoznaj się z aliasami i poleceniami cmdlet dla Azure Backup, Azure Site Recovery i magazynu Recovery Services. Oto przykład tego, co może być widoczne. Nie jest to kompletna lista poleceń cmdlet.

    ![Lista poleceń cmdlet Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Zaloguj się do konta platformy Azure za pomocą programu **Connect-AzAccount**.
5. Na wyświetlonej stronie sieci Web zostanie wyświetlony monit o wprowadzenie poświadczeń konta.

    * Alternatywnie można uwzględnić poświadczenia konta jako parametr w poleceniu cmdlet **Connect-AzAccount** z poleceniem **-Credential**.
    * Jeśli jesteś partnerem programu CSP działającym w imieniu dzierżawy, określ klienta jako dzierżawcę przy użyciu nazwy domeny głównej dzierżawy tenantID lub. Przykładem jest **Connect-AzAccount-dzierżawca** fabrikam.com.

6. Skojarz subskrypcję, której chcesz używać z kontem, ponieważ konto może mieć kilka subskrypcji.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Jeśli używasz Azure Backup po raz pierwszy, użyj polecenia cmdlet **register-AzResourceProvider** , aby zarejestrować dostawcę Recovery Services platformy Azure w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sprawdź, czy dostawcy zarejestrowali się pomyślnie:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. W danych wyjściowych polecenia Sprawdź, czy **RegistrationState** zmiany zostały **zarejestrowane**. Jeśli nie, uruchom ponownie polecenie cmdlet **register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest zasobem Menedżer zasobów, dlatego należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub można utworzyć grupę zasobów za pomocą polecenia cmdlet **New-AzResourceGroup** . Podczas tworzenia grupy zasobów należy określić nazwę i lokalizację grupy zasobów.

Wykonaj następujące kroki, aby utworzyć magazyn Recovery Services.

1. Magazyn znajduje się w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, Utwórz nową aplikację przy użyciu [nowej AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). W tym przykładzie utworzymy nową grupę zasobów w regionie zachodnie stany USA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Użyj polecenia cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) , aby utworzyć magazyn. Określ tę samą lokalizację dla magazynu, który został użyty dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określ typ nadmiarowości, która ma być używana w magazynie magazynu.

   * Można użyć magazynu [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md).
   * W poniższym przykładzie ustawiono opcję **-BackupStorageRedundancy** dla polecenia[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd dla **testvault** jako **geomiarowy**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w subskrypcji, użyj polecenie [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do następujących. Należy pamiętać, że skojarzona Grupa zasobów i lokalizacja są udostępniane.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Zapisz obiekt magazynu w zmiennej i ustaw kontekst magazynu.

* Wiele Azure Backup poleceń cmdlet wymaga obiektu magazynu Recovery Services jako danych wejściowych, dlatego warto przechowywać obiekt magazynu w zmiennej.
* Kontekst magazynu to typ danych chronionych w magazynie. Ustaw go za pomocą [opcji Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po ustawieniu kontekstu ma zastosowanie do wszystkich kolejnych poleceń cmdlet.

Poniższy przykład ustawia kontekst magazynu dla **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobierz identyfikator magazynu

Planujemy zaniechanie ustawienia kontekstu magazynu zgodnie z zaleceniami Azure PowerShell. Zamiast tego można zapisać lub pobrać identyfikator magazynu i przekazać go do odpowiednich poleceń. Dlatego jeśli nie ustawisz kontekstu magazynu lub chcesz określić polecenie do uruchomienia dla pewnego magazynu, Przekaż identyfikator magazynu jako "-vaultID" do wszystkich odpowiednich poleceń w następujący sposób:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają harmonogram tworzenia kopii zapasowych i czas przechowywania punktów odzyskiwania kopii zapasowych:

* Zasady tworzenia kopii zapasowej są skojarzone z co najmniej jedną zasadą przechowywania. Zasady przechowywania określają czas przechowywania punktu odzyskiwania przed jego usunięciem.
* Wyświetl domyślne przechowywanie zasad kopii zapasowych za pomocą polecenia [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Wyświetlanie domyślnego harmonogramu zasad tworzenia kopii zapasowych za pomocą polecenia [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Aby utworzyć nowe zasady tworzenia kopii zapasowych, należy użyć polecenia cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) . Wprowadzasz obiekty zasad harmonogramu i przechowywania.

Domyślnie w obiekcie zasad harmonogramu jest zdefiniowany czas rozpoczęcia. Użyj poniższego przykładu, aby zmienić godzinę rozpoczęcia na żądaną godzinę rozpoczęcia. Wymagana godzina rozpoczęcia powinna być również w formacie UTC. W poniższym przykładzie przyjęto założenie, że wymagany czas rozpoczęcia to 01:00 czasu UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać godzinę rozpoczęcia tylko w ciągu 30 minut. W powyższym przykładzie może to być tylko "01:00:00" lub "02:30:00". Godzina rozpoczęcia nie może być "01:15:00"

W poniższym przykładzie są przechowywane zasady harmonogramu i zasady przechowywania w zmiennych. Następnie używa tych zmiennych jako parametrów dla nowych zasad (**NewAFSPolicy**). **NewAFSPolicy** pobiera codzienne kopie zapasowe i zachowuje ją przez 30 dni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe są podobne do następujących.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Włącz tworzenie kopii zapasowej

Po zdefiniowaniu zasad tworzenia kopii zapasowych można włączyć ochronę udziału plików platformy Azure przy użyciu zasad.

### <a name="retrieve-a-backup-policy"></a>Pobieranie zasad kopii zapasowych

Należy pobrać odpowiedni obiekt zasad z poleceniem [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). To polecenie cmdlet umożliwia uzyskanie określonych zasad lub wyświetlenie zasad skojarzonych z typem obciążenia.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Pobieranie zasad dla typu obciążenia

Poniższy przykład pobiera zasady dla typu obciążenia **migracji pamięci**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Dane wyjściowe są podobne do następujących.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Strefa czasowa pola **nieprzerwanego** działania w programie PowerShell to uniwersalny czas koordynowany (UTC). Gdy w Azure Portal zostanie wyświetlony czas tworzenia kopii zapasowej, czas jest dostosowywany do lokalnej strefy czasowej.

### <a name="retrieve-a-specific-policy"></a>Pobieranie określonych zasad

Następujące zasady pobierają zasady kopii zapasowej o nazwie **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Włącz tworzenie kopii zapasowych i stosowanie zasad

Włącz ochronę za pomocą [enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Po skojarzeniu zasad z magazynem kopie zapasowe są wyzwalane zgodnie z harmonogramem zasad.

Poniższy przykład umożliwia ochronę udziału plików platformy Azure **testAzureFileShare** na koncie magazynu **testStorageAcct**przy użyciu zasad **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Polecenie czeka na zakończenie zadania konfigurowania ochrony i daje podobne dane wyjściowe, jak pokazano.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

Użyj [Narzędzia Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) , aby uruchomić kopię zapasową na żądanie dla chronionego udziału plików platformy Azure.

1. Pobierz konto magazynu i udział plików z kontenera w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Aby rozpocząć zadanie tworzenia kopii zapasowej, należy uzyskać informacje o maszynie wirtualnej za pomocą [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom kopię zapasową na żądanie przy użyciu[kopii zapasowej AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Wykonaj kopię zapasową na żądanie w następujący sposób:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Polecenie zwraca zadanie z IDENTYFIKATORem, który ma być śledzony, jak pokazano w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Migawki udziałów plików platformy Azure są używane podczas wykonywania kopii zapasowych, więc zwykle zadanie jest wykonywane przez czas, gdy polecenie zwróci te dane wyjściowe.

### <a name="using-on-demand-backups-to-extend-retention"></a>Korzystanie z kopii zapasowych na żądanie w celu zwiększenia okresu przechowywania

Kopie zapasowe na żądanie mogą służyć do przechowywania migawek przez 10 lat. Harmonogramy mogą służyć do uruchamiania skryptów programu PowerShell na żądanie z wybranym przechowywaniem, a tym samym wykonywania migawek w regularnych odstępach czasu każdego tygodnia, miesiąca lub roku. Podczas tworzenia zwykłych migawek zapoznaj się z [ograniczeniami tworzenia kopii zapasowych na żądanie](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) przy użyciu usługi Azure Backup.

Jeśli szukasz przykładowych skryptów, możesz zapoznać się z przykładowym skryptem w witrynie GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) przy użyciu Azure Automation elementu Runbook, który umożliwia okresowe planowanie tworzenia kopii zapasowych i ich zachowanie nawet do 10 lat.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat](backup-afs.md) tworzenia kopii zapasowych Azure Files w Azure Portal.
