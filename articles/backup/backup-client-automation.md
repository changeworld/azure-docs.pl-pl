---
title: Używanie programu PowerShell do wytwarzania kopii zapasowej systemu Windows Server na platformie Azure
description: W tym artykule dowiesz się, jak używać programu PowerShell do konfigurowania usługi Azure Backup w systemie Windows Server lub na kliencie systemu Windows oraz zarządzania tworzeniem kopii zapasowych i odzyskiwaniem.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: efe0b93fe1e37990422ffbd2256e38c12401dca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673193"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Wdrażanie kopii zapasowych systemu Windows Server/Windows Client na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell

W tym artykule pokazano, jak używać programu PowerShell do konfigurowania usługi Azure Backup w systemie Windows Server lub na kliencie systemu Windows oraz zarządzania tworzeniem kopii zapasowych i odzyskiwaniem.

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć, [zainstaluj najnowszą wersję programu PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki prowadzą przez tworzenie magazynu usług odzyskiwania. Magazyn usług odzyskiwania różni się od magazynu kopii zapasowych.

1. Jeśli używasz usługi Azure Backup po raz pierwszy, należy użyć polecenia cmdlet **Register-AzResourceProvider,** aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Magazyn usług odzyskiwania jest zasobem ARM, więc należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową. Podczas tworzenia nowej grupy zasobów określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Użyj polecenia cmdlet **New-AzRecoveryServicesVault,** aby utworzyć nowy magazyn. Należy określić tę samą lokalizację dla przechowalni, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Określ typ nadmiarowości magazynu do użycia; można użyć [magazynu lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego (GRS).](../storage/common/storage-redundancy-grs.md) Poniższy przykład pokazuje -BackupStorageRedundancy opcja dla testVault jest ustawiona na GeoRedundant.

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Użyj **Get-AzRecoveryServicesVault,** aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Za pomocą tego polecenia można sprawdzić, czy utworzono nowy magazyn lub zobaczyć, jakie magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie **Get-AzRecoveryServicesVault**i wszystkie magazyny w subskrypcji są wymienione.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalowanie agenta usługi Azure Backup

Przed zainstalowaniem agenta usługi Azure Backup należy pobrać instalatora i być obecny na serwerze Windows Server. Najnowszą wersję instalatora można pobrać z [Centrum pobierania Firmy Microsoft](https://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu usług odzyskiwania. Zapisz instalator w łatwo dostępnej lokalizacji, takiej\*jak *C:\Downloads .

Alternatywnie, użyj programu PowerShell, aby pobrać:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta ze wszystkimi opcjami domyślnymi. Instalacja trwa kilka minut w tle. Jeśli opcja */nu* nie zostanie określona, po zakończeniu instalacji zostanie otwarte okno **Windows Update,** aby sprawdzić dostępność aktualizacji. Po zainstalowaniu agent pojawi się na liście zainstalowanych programów.

Aby wyświetlić listę zainstalowanych programów, przejdź do strony Programy**i funkcje** **Panelu** > **sterowania** > .

![Zainstalowany agent](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opcje instalacji

Aby wyświetlić wszystkie opcje dostępne za pośrednictwem wiersza polecenia, użyj następującego polecenia:

```powershell
MARSAgentInstaller.exe /?
```

Dostępne opcje obejmują:

| Opcja | Szczegóły | Domyślne |
| --- | --- | --- |
| /q |Cicha instalacja |- |
| /p:"lokalizacja" |Ścieżka do folderu instalacyjnego dla agenta usługi Azure Backup. |C:\Pliki programów\Agent usług odzyskiwania platformy Microsoft Azure |
| /s:"lokalizacja" |Ścieżka do folderu pamięci podręcznej dla agenta usługi Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Zgodę na usługę Microsoft Update |- |
| /nu |Nie sprawdzaj dostępności aktualizacji po zakończeniu instalacji |- |
| /d |Odinstalowuje agenta usług odzyskiwania platformy Microsoft Azure |- |
| /ph |Adres hosta serwera proxy |- |
| /po |Numer portu hosta serwera proxy |- |
| /pu |Nazwa użytkownika hosta serwera proxy |- |
| /pw |Hasło serwera proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Rejestrowanie systemu Windows Server lub komputera klienckiego systemu Windows w magazynie usług odzyskiwania

Po utworzeniu magazynu usług odzyskiwania pobierz najnowsze poświadczenia agenta i magazynu i przechowuj je w dogodnej lokalizacji, takiej jak C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Rejestracja za pomocą modułu PS Az

> [!NOTE]
> Błąd z generowaniem certyfikatu przechowalni został naprawiony w wersji Az 3.5.0. Użyj wersji Az 3.5.0 lub większej, aby pobrać certyfikat przechowalni.

W najnowszym module Az programu PowerShell, ze względu na podstawowe ograniczenia platformy, pobieranie poświadczeń magazynu wymaga certyfikatu z podpisem własnym. W poniższym przykładzie pokazano, jak podać certyfikat z podpisem własnym i pobrać poświadczenia magazynu.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Na komputerze klienckim systemu Windows Server lub Windows uruchom polecenie cmdlet [Start-OBRegistration,](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) aby zarejestrować komputer w przechowalni.
To i inne polecenia cmdlet używane do tworzenia kopii zapasowych pochodzą z modułu MSONLINE, który marsjański agentinstalator dodał w ramach procesu instalacji.

Instalator agenta nie aktualizuje zmiennej $Env:PSModulePath. Oznacza to, że automatyczne ładowanie modułu nie powiedzie się. Aby rozwiązać ten problem, można wykonać następujące czynności:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternatywnie można ręcznie załadować moduł w skrypcie w następujący sposób:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Po załadowaniu poleceń cmdlet kopii zapasowej online należy zarejestrować poświadczenia magazynu:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Nie należy używać ścieżek względnych do określania pliku poświadczeń przechowalni. Należy podać ścieżkę bezwzględną jako dane wejściowe do polecenia cmdlet.
>
>

## <a name="networking-settings"></a>Ustawienia sieci

Gdy łączność komputera z systemem Windows z Internetem odbywa się za pośrednictwem serwera proxy, ustawienia serwera proxy mogą być również dostarczane do agenta. W tym przykładzie nie ma serwera proxy, więc jawnie czyścimy wszelkie informacje związane z serwerem proxy.

Użycie przepustowości można również kontrolować `work hour bandwidth` `non-work hour bandwidth` za pomocą opcji i dla danego zestawu dni tygodnia.

Ustawianie szczegółów serwera proxy i przepustowości odbywa się za pomocą polecenia cmdlet [Set-OBMachineSetting:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps)

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Ustawienia szyfrowania

Dane kopii zapasowej wysyłane do usługi Azure Backup są szyfrowane w celu ochrony poufności danych. Hasło szyfrowania to "hasło", które służy do odszyfrowywania danych w czasie przywracania.

Kod zabezpieczający należy wygenerować, wybierając pozycję **Generuj**, w obszarze**Numer PIN zabezpieczeń** **Właściwości** >  **ustawień** > w sekcji **Magazyn usług odzyskiwania** w witrynie Azure portal. Następnie użyj tego `generatedPIN` jako w poleceniu:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Po ustawieniu informacji o hasłach należy zachować bezpieczeństwo. Nie można przywrócić danych z platformy Azure bez tego hasła.
>
>

## <a name="back-up-files-and-folders"></a>1.

Wszystkie kopie zapasowe z serwerów systemu Windows i klientów do usługi Azure Backup są regulowane przez zasady. Polityka składa się z trzech części:

1. **Harmonogram tworzenia kopii zapasowych,** który określa, kiedy kopie zapasowe muszą być podejmowane i synchronizowane z usługą.
2. **Harmonogram przechowywania,** który określa, jak długo zachować punkty odzyskiwania na platformie Azure.
3. **Specyfikacja dołączania/wykluczania plików,** która określa, co powinno być archiwizowane.

W tym dokumencie, ponieważ automatyzujemy tworzenie kopii zapasowych, zakładamy, że nic nie zostało skonfigurowane. Rozpoczynamy od utworzenia nowej zasady tworzenia kopii zapasowych przy użyciu polecenia cmdlet [New-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps)

```powershell
$NewPolicy = New-OBPolicy
```

W tej chwili zasady są puste i inne polecenia cmdlet są potrzebne do określenia, jakie elementy zostaną uwzględnione lub wykluczone, kiedy będą uruchamiane kopie zapasowe i gdzie będą przechowywane kopie zapasowe.

### <a name="configuring-the-backup-schedule"></a>Konfigurowanie harmonogramu tworzenia kopii zapasowych

Pierwszą z trzech części zasad jest harmonogram tworzenia kopii zapasowych, który jest tworzony przy użyciu polecenia cmdlet [New-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) Harmonogram tworzenia kopii zapasowych określa, kiedy należy wykonać kopie zapasowe. Podczas tworzenia harmonogramu należy określić dwa parametry wejściowe:

* **Dni tygodnia, w** których kopia zapasowa powinna zostać uruchomina. Zadanie tworzenia kopii zapasowej można uruchomić tylko jednego dnia lub każdego dnia tygodnia lub dowolnej kombinacji pomiędzy nimi.
* **Godziny dnia,** kiedy kopia zapasowa powinna być uruchamiana. Można zdefiniować maksymalnie trzy różne godziny w ciągu dnia, kiedy kopia zapasowa zostanie wyzwolona.

Na przykład można skonfigurować zasady tworzenia kopii zapasowych, która jest uruchamiana o godzinie 16:00 w każdą sobotę i niedzielę.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Harmonogram tworzenia kopii zapasowych musi być skojarzony z zasadami, co można osiągnąć za pomocą polecenia cmdlet [Set-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps)

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Konfigurowanie zasad przechowywania

Zasady przechowywania określa, jak długo punkty odzyskiwania utworzone z zadań kopii zapasowej są zachowywane. Podczas tworzenia nowych zasad przechowywania przy użyciu polecenia cmdlet [New-OBRetentionPolicy,](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) można określić liczbę dni, które punkty odzyskiwania kopii zapasowej muszą być przechowywane za pomocą usługi Azure Backup. W poniższym przykładzie ustawia zasady przechowywania siedmiu dni.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zasady przechowywania muszą być skojarzone z głównymi zasadami przy użyciu polecenia cmdlet [Set-OBRetentionPolicy:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps)

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Uwzględnianie plików, które mają być archiwizowane, z wyłączeniem plików, które mają być archiwizowane

Obiekt `OBFileSpec` definiuje pliki, które mają zostać uwzględnione i wykluczone w kopii zapasowej. Jest to zestaw reguł, które mają zakres chronionych plików i folderów na komputerze. Można mieć dowolną liczbę reguł dołączania lub wykluczania plików, ile jest wymagane, i skojarzyć je z zasadami. Podczas tworzenia nowego obiektu OBFileSpec można:

* Określanie plików i folderów, które mają zostać dołączone
* Określanie plików i folderów, które mają zostać wykluczone
* Określ cykliczną kopię zapasową danych w folderze (lub), czy należy wykonać kopię zapasową tylko plików najwyższego poziomu w określonym folderze.

Ten ostatni jest osiągany przy użyciu -NonRecursive flagi w New-OBFileSpec polecenia.

W poniższym przykładzie sprzeczki zapasowej woluminu C: i D: i wykluczymy pliki binarne systemu operacyjnego w folderze systemu Windows i folderach tymczasowych. W tym celu utworzymy dwie specyfikacje plików przy użyciu polecenia cmdlet [New-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) - jeden do włączenia i jeden do wykluczenia. Po utworzeniu specyfikacji plików są one skojarzone z zasadami przy użyciu polecenia cmdlet [Add-OBFileSpec.](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps)

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Stosowanie zasad

Teraz obiekt zasad jest kompletny i ma skojarzony harmonogram tworzenia kopii zapasowych, zasady przechowywania i listę plików włączenia/wykluczenia. Ta zasada może być teraz zatwierdzona do użycia usługi Azure Backup. Przed zastosowaniem nowo utworzonych zasad upewnij się, że z serwerem nie są skojarzone żadne istniejące zasady tworzenia kopii zapasowych przy użyciu polecenia cmdlet [Usuń obpolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) Usunięcie zasad spowoduje wyświetlenie monitu o potwierdzenie. Aby pominąć potwierdzenie, `-Confirm:$false` użyj flagi z poleceniem cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Zatwierdzanie obiektu zasad odbywa się przy użyciu polecenia cmdlet [Set-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) Poprosi o potwierdzenie. Aby pominąć potwierdzenie, `-Confirm:$false` użyj flagi z poleceniem cmdlet.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Szczegóły istniejących zasad tworzenia kopii zapasowych można wyświetlić za pomocą polecenia cmdlet [Get-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) Można przejść do szczegółów dalej przy użyciu polecenia cmdlet [Get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) dla harmonogramu tworzenia kopii zapasowych i polecenia cmdlet [Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) dla zasad przechowywania

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Wykonywanie kopii zapasowej na żądanie

Po ustawieniu zasad tworzenia kopii zapasowych kopie zapasowe będą występować zgodnie z harmonogramem. Wyzwalanie kopii zapasowej na żądanie jest również możliwe przy użyciu polecenia cmdlet [Start-OBBackup:](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps)

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Czelonia zapasu stanu systemu Windows Server w agencie MABS

Ta sekcja obejmuje polecenie Programu PowerShell służące do konfigurowania stanu systemu w agencie MABS

### <a name="schedule"></a>Harmonogram

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Przechowywanie

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Konfigurowanie harmonogramu i przechowywania

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Weryfikowanie zasad

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Przywracanie danych z usługi Azure Backup

W tej sekcji poprowadzą Cię przez kroki automatyzacji odzyskiwania danych z usługi Azure Backup. W ten sposób obejmuje następujące kroki:

1. Wybieranie woluminu źródłowego
2. Wybieranie punktu kopii zapasowej do przywrócenia
3. Określanie elementu do przywrócenia
4. Wyzwalanie procesu przywracania

### <a name="picking-the-source-volume"></a>Pobieranie woluminu źródłowego

Aby przywrócić element z usługi Azure Backup, należy najpierw zidentyfikować źródło elementu. Ponieważ wykonujemy polecenia w kontekście systemu Windows Server lub klienta systemu Windows, urządzenie jest już identyfikowane. Następnym krokiem w identyfikacji źródła jest zidentyfikowanie woluminu zawierającego go. Listę woluminów lub źródeł, które są archiwizowane z tego komputera można pobrać, wykonując polecenie cmdlet [Get-OBRecoverableSource.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) To polecenie zwraca tablicę wszystkich źródeł kopii zapasowej z tego serwera/klienta.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Wybieranie punktu kopii zapasowej, z którego ma być przywracany

Można pobrać listę punktów kopii zapasowej, wykonując [get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) cmdlet z odpowiednimi parametrami. W naszym przykładzie wybierzemy najnowszy punkt kopii zapasowej dla woluminu źródłowego *C:* i użyjemy go do odzyskania określonego pliku.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Obiekt `$Rps` jest tablicą punktów kopii zapasowej. Pierwszy element jest najnowszym punktem, a N-ty element jest najstarszym punktem. Aby wybrać najnowszy punkt, `$Rps[0]`użyjemy .

### <a name="specifying-an-item-to-restore"></a>Określanie elementu do przywrócenia

Aby przywrócić określony plik, określ nazwę pliku względem woluminu głównego. Na przykład, aby pobrać C:\Test\Cat.job, wykonaj następujące polecenie.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Wyzwalanie procesu przywracania

Aby wyzwolić proces przywracania, musimy najpierw określić opcje odzyskiwania. Można to zrobić za pomocą polecenia cmdlet [New-OBRecoveryOption.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) W tym przykładzie załóżmy, że chcemy przywrócić pliki do *C:\temp*. Załóżmy również, że chcemy pominąć pliki, które już istnieją w folderze docelowym *C:\temp*. Aby utworzyć taką opcję odzyskiwania, użyj następującego polecenia:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Teraz wyzwolić proces przywracania za pomocą polecenia [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) na wybranej `$Item` z danych wyjściowych `Get-OBRecoverableItem` polecenia cmdlet:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalowywanie agenta kopii zapasowej platformy Azure

Odinstalowanie agenta usługi Azure Backup można wykonać za pomocą następującego polecenia:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Odinstalowanie plików binarnych agenta z urządzenia ma pewne konsekwencje do rozważenia:

* Usuwa filtr plików z komputera, a śledzenie zmian jest zatrzymane.
* Wszystkie informacje o zasadach są usuwane z komputera, ale informacje o zasadach są nadal przechowywane w usłudze.
* Wszystkie harmonogramy tworzenia kopii zapasowych są usuwane i nie są pobierane żadne dalsze kopie zapasowe.

Jednak dane przechowywane na platformie Azure pozostaje i jest zachowywany zgodnie z konfiguracją zasad przechowywania przez Ciebie. Starsze punkty są automatycznie przestarzałe.

## <a name="remote-management"></a>Zdalne zarządzanie

Wszystkie zarządzanie wokół agenta usługi Azure Backup, zasad i źródeł danych można wykonać zdalnie za pośrednictwem programu PowerShell. Maszyna, która będzie zarządzana zdalnie, musi być odpowiednio przygotowana.

Domyślnie usługa WinRM jest skonfigurowana do ręcznego uruchamiania. Typ uruchamiania musi być ustawiony na *Automatyczny* i należy uruchomić usługę. Aby sprawdzić, czy usługa WinRM jest uruchomiona, wartość właściwości Status powinna być *uruchomiona*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Program PowerShell powinien być skonfigurowany do komunikacji zdalnej.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Urządzenie może być teraz zarządzane zdalnie - począwszy od instalacji agenta. Na przykład poniższy skrypt kopiuje agenta do komputera zdalnego i instaluje go.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup dla systemu Windows Server/Client:

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [1.](backup-windows-with-mars-agent.md)
