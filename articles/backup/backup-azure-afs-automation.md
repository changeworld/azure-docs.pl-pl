---
title: "3."
description: W tym artykule dowiesz się, jak wykonać kopię zapasową plików Platformy Azure przy użyciu usługi Azure Backup i programu PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273542"
---
# <a name="back-up-azure-files-with-powershell"></a>3.

W tym artykule opisano sposób używania programu Azure PowerShell do tworzenia kopii zapasowych udziału plików usługi Azure Files przy użyciu magazynu usług [Azure Backup](backup-overview.md) Recovery Services.

W tym artykule wyjaśniono, jak:

> [!div class="checklist"]
>
> * Skonfiguruj program PowerShell i zarejestruj dostawcę usług azure recovery.
> * Utwórz magazyn usługi Recovery Services.
> * Konfigurowanie kopii zapasowej dla udziału plików platformy Azure.
> * Uruchamianie zadania tworzenia kopii zapasowej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach usług odzyskiwania.
* Przeczytaj o możliwościach tworzenia kopii zapasowych [udziałów plików platformy Azure](backup-afs.md)w wersji zapoznawczej.
* Przejrzyj hierarchię obiektów programu PowerShell dla usług odzyskiwania.

## <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów usług odzyskiwania

Hierarchia obiektów jest podsumowana na poniższym diagramie.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Review the **Az.RecoveryServices** [cmdlet reference](/powershell/module/az.recoveryservices) reference in the Azure library.

## <a name="set-up-and-install"></a>Konfigurowanie i instalowanie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skonfiguruj program PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję programu Az PowerShell](/powershell/azure/install-az-ps). Minimalna wymagana wersja to 1.0.0.

> [!WARNING]
> Minimalna wersja PS wymagana do podglądu to "Az 1.0.0". Ze względu na nadchodzące zmiany dla GA, minimalna wymagana wersja PS będzie "Az.RecoveryServices 2.6.0". Bardzo ważne jest uaktualnienie wszystkich istniejących wersji PS do tej wersji. W przeciwnym razie istniejące skrypty zostaną przerwane po ga. Zainstaluj wersję minimalną za pomocą następujących poleceń PS

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Znajdź polecenia cmdlet programu Azure Backup PowerShell za pomocą tego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Przejrzyj aliasy i polecenia cmdlet dla usługi Azure Backup, usługi Azure Site Recovery i magazynu usług odzyskiwania. Oto przykład tego, co możesz zobaczyć. Nie jest to pełna lista poleceń cmdlet.

    ![Lista poleceń cmdlet usług odzyskiwania](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Zaloguj się do konta platformy Azure za pomocą **connect-azaccount**.
5. Na wyświetlonej stronie sieci Web zostanie wyświetlony monit o wprowadzenie poświadczeń konta.

    * Alternatywnie można uwzględnić poświadczenia konta jako parametr w **połącz-azkonta** z **poleceniem -Credential**.
    * Jeśli jesteś partnerem CSP pracującym w imieniu dzierżawcy, określ klienta jako dzierżawcę, używając ich identyfikatora dzierżawcy lub nazwy domeny podstawowej dzierżawcy. Przykładem jest **Connect-AzAccount -Tenant** fabrikam.com.

6. Skojarz subskrypcję, której chcesz użyć z kontem, ponieważ konto może mieć kilka subskrypcji.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Jeśli używasz usługi Azure Backup po raz pierwszy, użyj polecenia cmdlet **Register-AzResourceProvider,** aby zarejestrować dostawcę usług Azure Recovery Services w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sprawdź, czy dostawcy zarejestrowali się pomyślnie:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. W danych wyjściowych polecenia sprawdź, czy **stan rejestracji** zmienia się na **Zarejestrowany**. Jeśli tak nie jest, uruchom ponownie polecenie cmdlet **Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usług odzyskiwania jest zasobem Menedżera zasobów, więc należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć grupę zasobów za pomocą polecenia cmdlet **New-AzResourceGroup.** Podczas tworzenia grupy zasobów określ nazwę i lokalizację grupy zasobów.

Wykonaj następujące kroki, aby utworzyć magazyn usług odzyskiwania.

1. Przechowalnia jest umieszczana w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, utwórz nową grupę za pomocą [grupy New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). W tym przykładzie tworzymy nową grupę zasobów w regionie Zachodnie stany USA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Użyj polecenia cmdlet [New-AzRecoveryServicesVault,](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) aby utworzyć przechowalnię. Określ tę samą lokalizację dla przechowalni, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określ typ nadmiarowości, który ma być używany w magazynie magazynu.

   * Można użyć [magazynu lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md).
   * W poniższym przykładzie ustawia opcję **-BackupStorageRedundancy** dla[właściwości Cmd Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) dla **testvault** ustawionego na **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, należy użyć funkcji [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do następujących. Należy zauważyć, że skojarzona grupa zasobów i lokalizacja są dostarczane.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ustawianie kontekstu przechowalni

Przechowuj obiekt przechowalni w zmiennej i ustawia kontekst przechowalni.

* Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usług odzyskiwania jako danych wejściowych, dzięki czemu wygodnie jest przechowywać obiekt przechowalni w zmiennej.
* Kontekst magazynu to typ danych chronionych w magazynie. Ustaw go za pomocą [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po ustawieniu kontekstu stosuje się do wszystkich kolejnych poleceń cmdlet.

W poniższym przykładzie ustawia kontekst przechowalni dla **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobieranie identyfikatora przechowalni

Planujemy przestarzałe ustawienie kontekstu magazynu zgodnie z wytycznymi programu Azure PowerShell. Zamiast tego można przechowywać lub pobierać identyfikator przechowalni i przekazać go do odpowiednich poleceń. Jeśli więc nie ustawisz kontekstu przechowalni lub chcesz określić polecenie do uruchomienia dla określonego magazynu, przekaż identyfikator przechowalni jako "-vaultID" do wszystkich odpowiednich poleceń w następujący sposób:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają harmonogram tworzenia kopii zapasowych i czas, przez jaki powinny być przechowywane punkty odzyskiwania kopii zapasowej:

* Zasady tworzenia kopii zapasowych są skojarzone z co najmniej jedną zasadą przechowywania. Zasady przechowywania określa, jak długo punkt odzyskiwania jest przechowywany przed jego usunięciem.
* Wyświetlanie domyślnego przechowywania zasad kopii zapasowej przy użyciu [funkcji Get-AzRecoveryServiceBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Wyświetlanie domyślnego harmonogramu zasad tworzenia kopii zapasowych przy użyciu [programu Get-AzRecoveryServiceSChedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Polecenie cmdlet [New-AzRecoveryServicesBackupProtectionPolicy do](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) utworzenia nowej zasady tworzenia kopii zapasowych. Wprowadź harmonogram i przechowywania obiektów zasad.

Domyślnie godzina rozpoczęcia jest zdefiniowana w obiekcie zasad harmonogramu. Użyj poniższego przykładu, aby zmienić czas rozpoczęcia na żądany czas rozpoczęcia. Żądany czas rozpoczęcia powinien być również w czasie UTC. Poniższy przykład zakłada, że żądana godzina rozpoczęcia to 01:00 AM UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać czas rozpoczęcia tylko w 30 minut wielokrotności. W powyższym przykładzie może to być tylko "01:00:00" lub "02:30:00". Czas rozpoczęcia nie może być "01:15:00"

W poniższym przykładzie przechowuje zasady harmonogramu i zasady przechowywania w zmiennych. Następnie używa tych zmiennych jako parametrów dla nowej polityki (**NewAFSPolicy**). **NewAFSPolicy** wykonuje codzienną kopię zapasową i zachowuje ją przez 30 dni.

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

## <a name="enable-backup"></a>Włączanie tworzenia kopii zapasowych

Po zdefiniowaniu zasad tworzenia kopii zapasowych można włączyć ochronę udziału plików platformy Azure przy użyciu zasad.

### <a name="retrieve-a-backup-policy"></a>Pobieranie zasad tworzenia kopii zapasowych

Pobierz odpowiedni obiekt zasad za pomocą [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Użyj tego polecenia cmdlet, aby uzyskać określone zasady lub wyświetlić zasady skojarzone z typem obciążenia.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Pobieranie zasad dla typu obciążenia

Poniższy przykład pobiera zasady dla typu obciążenia **AzureFiles**.

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
> Strefa czasowa pola **BackupTime** w programie PowerShell to uniwersalny czas koordynowany (UTC). Gdy czas tworzenia kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dostosowywany do lokalnej strefy czasowej.

### <a name="retrieve-a-specific-policy"></a>Pobieranie określonych zasad

Poniższa zasada pobiera zasady tworzenia kopii zapasowych o nazwie **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Włączanie tworzenia kopii zapasowych i stosowanie zasad

Włącz ochronę za pomocą [Enable-AzRecoveryServiceBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Po skojarzenia zasad z magazynem kopie zapasowe są wyzwalane zgodnie z harmonogramem zasad.

Poniższy przykład umożliwia ochronę testu udziału plików platformy **AzureAzureFileShare** w test konta **magazynuStorageAcct,** z **zasadą dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Polecenie czeka, aż zadanie ochrony konfiguracji zostanie zakończone i daje podobne dane wyjściowe, jak pokazano.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Ważna informacja — identyfikacja elementu kopii zapasowej dla kopii zapasowych AFS

W tej sekcji przedstawiono ważną zmianę w kopii zapasowej AFS w ramach przygotowań do ga.

Podczas włączania kopii zapasowej dla systemu AFS użytkownik dostarcza przyjazną dla klienta nazwę udziału plików jako nazwę jednostki i tworzony jest element kopii zapasowej. "Nazwa" elementu kopii zapasowej jest unikatowym identyfikatorem utworzonym przez usługę Azure Backup. Zazwyczaj identyfikator obejmuje nazwę przyjazną dla użytkownika. Ale do obsługi ważnego scenariusza usuwania nietrwałego, gdzie można usunąć udział plików i innego udziału plików można utworzyć o tej samej nazwie, unikatowa tożsamość udziału plików platformy Azure będzie teraz identyfikator zamiast nazwy przyjaznej dla klienta. Aby poznać unikatową tożsamość/nazwę każdego elementu, wystarczy uruchomić ```Get-AzRecoveryServicesBackupItem``` polecenie z odpowiednimi filtrami dla backupManagementType i WorkloadType, aby uzyskać wszystkie odpowiednie elementy, a następnie obserwować pole nazwy w zwróconym obiekcie/odpowiedzi PS. Zawsze zaleca się wyświetlanie elementów, a następnie pobieranie ich unikatowej nazwy z pola "nazwa" w odpowiedzi. Ta wartość służy do filtrowania elementów za pomocą parametru "Nazwa". W przeciwnym razie użyj FriendlyName parametr, aby pobrać element z jego przyjazną dla klienta nazwę/identyfikator.

> [!WARNING]
> Upewnij się, że wersja PS jest uaktualniona do minimalnej wersji dla "Az.RecoveryServices 2.6.0" dla kopii zapasowych AFS. W tej wersji filtr "friendlyName" ```Get-AzRecoveryServicesBackupItem``` jest dostępny dla polecenia. Przekaż nazwę udziału plików platformy Azure do parametru friendlyName. Jeśli przekażesz nazwę udziału plików platformy Azure do parametru "Name", ta wersja zgłosi ostrzeżenie, aby przekazać tę przyjazną nazwę do parametru przyjaznej nazwy. Nieinstalowanie tej minimalnej wersji może spowodować niepowodzenie istniejących skryptów. Zainstaluj minimalną wersję PS za pomocą następującego polecenia.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

Użyj [Backup-AzRecoveryServicesBackupUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) do uruchomienia kopii zapasowej na żądanie dla chronionego udziału plików platformy Azure.

1. Pobierz konto magazynu z kontenera w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Aby rozpocząć zadanie tworzenia kopii zapasowej, można uzyskać informacje o udziale plików platformy Azure za pomocą [get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom kopię zapasową na żądanie za pomocą[kopii zapasowej AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Uruchom kopię zapasową na żądanie w następujący sposób:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Polecenie zwraca zadanie z identyfikatorem do śledzenia, jak pokazano w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Migawki udziału plików platformy Azure są używane podczas wykonywania kopii zapasowych, więc zwykle zadanie kończy się do czasu, gdy polecenie zwraca to dane wyjściowe.

### <a name="using-on-demand-backups-to-extend-retention"></a>Rozszerzanie przechowywania za pomocą kopii zapasowych na żądanie

Kopie zapasowe na żądanie mogą służyć do przechowywania migawek przez 10 lat. Harmonogramy mogą służyć do uruchamiania skryptów programu PowerShell na żądanie z wybraną retencją, a tym samym do wykonywania migawek w regularnych odstępach czasu co tydzień, miesiąc lub rok. Podczas wykonywania regularnych migawek odnoszą się do [ograniczeń kopii zapasowych na żądanie](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) przy użyciu kopii zapasowej platformy Azure.

Jeśli szukasz przykładowych skryptów, możesz odwołać się do przykładowego skryptu w usłudze GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) przy użyciu uruchomieniu usługi Azure Automation, który umożliwia okresowe planowanie tworzenia kopii zapasowych i zachowywanie ich nawet do 10 lat.

> [!WARNING]
> Upewnij się, że wersja PS jest uaktualniona do minimalnej wersji dla "Az.RecoveryServices 2.6.0" dla kopii zapasowych AFS w systemach runbook automatyzacji. Będziesz musiał zastąpić stary moduł "AzureRM" modułem "Az". W tej wersji filtr "friendlyName" ```Get-AzRecoveryServicesBackupItem``` jest dostępny dla polecenia. Przekaż nazwę udziału plików azure do parametru friendlyName. Jeśli przekażesz nazwę udziału plików azure do parametru "Name", ta wersja zgłosi ostrzeżenie, aby przekazać tę przyjazną nazwę do parametru przyjaznej nazwy.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-afs.md) tworzenie kopii zapasowych plików platformy Azure w witrynie Azure portal.
