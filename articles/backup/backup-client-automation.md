---
title: Utwórz kopię zapasową serwera systemu Windows Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Informacje o sposobie wdrażania i zarządzania nimi przy użyciu programu PowerShell usługi Kopia zapasowa Azure
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: 65218095-2996-44d9-917b-8c84fc9ac415
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: saurse;markgal;jimpark;nkolli;trinadhk
ms.openlocfilehash: 61bb58b2cf0d76f662144cb1911a6521394e92b6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Wdrażanie kopii zapasowych systemu Windows Server/Windows Client na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell
W tym artykule przedstawiono sposób konfigurowania usługi Kopia zapasowa Azure w systemie Windows Server lub klienta systemu Windows oraz zarządzania nimi kopii zapasowych i odzyskiwania przy użyciu programu PowerShell.

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Ten artykuł dotyczy usługi Azure Resource Manager (ARM) i poleceń cmdlet programu środowiska PowerShell kopii zapasowej Online MS umożliwiają korzystanie z magazynu usług odzyskiwania w grupie zasobów.

Października 2015 Azure PowerShell 1.0 został wydany. Tej wersji 0.9.8 powiodło się. Zwolnij i temat istotne zmiany, szczególnie w wzorzec nazewnictwa poleceń cmdlet. Polecenia cmdlet 1.0 mają wzorzec nazewnictwa {czasownik}-AzureRm{rzeczownik}; natomiast nazwy 0.9.8 nie zawierają elementu **Rm** (np. New-AzureRmResourceGroup zamiast New-AzureResourceGroup). W przypadku korzystania z programu Azure PowerShell 0.9.8 trzeba najpierw włączyć tryb usługi Resource Manager przez uruchomienie polecenia **Switch-AzureMode AzureResourceManager**. To polecenie nie jest konieczne w wersji 1.0 lub nowszej.

Jeśli chcesz używać skryptów przeznaczony dla 0.9.8 środowisko, w środowisku w wersji 1.0 lub nowszej, należy ostrożnie aktualizacji i przetestować skrypty w środowisku przedprodukcyjnym przed ich użyciem w środowisku produkcyjnym, aby uniknąć nieoczekiwanego wpływu.

[Pobierz najnowszą wersję programu PowerShell](https://github.com/Azure/azure-powershell/releases) (minimalna wersja wymagana jest: 1.0.0)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Poniższe kroki prowadzi przez proces tworzenia magazynu usług odzyskiwania. Magazyn usług odzyskiwania są inne niż magazynu kopii zapasowych.

1. Jeśli korzystasz z usługi Kopia zapasowa Azure po raz pierwszy, należy użyć **AzureRMResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usług odzyskiwania Azure w ramach subskrypcji.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Magazyn usług odzyskiwania jest zasobem ARM, więc należy umieścić w grupie zasobów. Użyj istniejącej grupy zasobów lub Utwórz nową. Podczas tworzenia nowej grupy zasobów, określ nazwę i lokalizację dla grupy zasobów.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Użyj **AzureRmRecoveryServicesVault nowy** , aby utworzyć nowy magazyn. Należy określić tę samą lokalizację dla magazynu, które było używane dla grupy zasobów.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```
4. Określ typ nadmiarowość magazynu mają być używane; można użyć [lokalnie nadmiarowego magazynu (LRS)](../storage/common/storage-redundancy-lrs.md) lub [z magazynu geograficznie nadmiarowego magazynu (GRS)](../storage/common/storage-redundancy-grs.md). W poniższym przykładzie przedstawiono, że opcja - BackupStorageRedundancy testVault jest ustawiona na GeoRedundant.

   > [!TIP]
   > Wiele poleceń cmdlet narzędzia Kopia zapasowa Azure wymaga obiektu magazynu usług odzyskiwania jako danych wejściowych. Z tego powodu jest wygodne do przechowywania obiektów magazynu usług odzyskiwania kopii zapasowej w zmiennej.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji
Użyj **Get AzureRmRecoveryServicesVault** Aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Można użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn lub aby zobaczyć, jakie magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie **Get-AzureRmRecoveryServicesVault**, i są wyświetlane wszystkie magazyny do subskrypcji.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalowanie agenta usługi Kopia zapasowa Azure
Przed zainstalowaniem agenta usługi Kopia zapasowa Azure, musisz mieć Instalator pobrane i są obecne w systemie Windows Server. Możesz pobrać najnowszą wersję Instalatora z [Microsoft Download Center](http://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu usług odzyskiwania. Zapisanie Instalatora, aby łatwo dostępnej lokalizacji, takich jak * C:\Downloads\*.

Można również użyć programu PowerShell, aby uzyskać narzędzia do pobierania:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień:

```
PS C:\> MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta przy użyciu opcji domyślnej. Instalacja trwa kilka minut w tle. Jeśli nie określisz */nu* opcji, a następnie **usługi Windows Update** zostanie otwarte okno po zakończeniu instalacji sprawdź, czy wszystkie aktualizacje. Po zainstalowaniu agenta zostaną wyświetlone na liście zainstalowanych programów.

Aby wyświetlić listę zainstalowanych programów, przejdź do **Panelu sterowania** > **programy** > **programy i funkcje**.

![Agent jest zainstalowany](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opcje instalacji
Aby wyświetlić wszystkie opcje dostępne za pomocą wiersza polecenia, użyj następującego polecenia:

```
PS C:\> MARSAgentInstaller.exe /?
```

Dostępne opcje to:

| Opcja | Szczegóły | Domyślne |
| --- | --- | --- |
| /q |Instalację cichą |- |
| / p: "Lokalizacja" |Ścieżka do folderu instalacji agenta usługi Kopia zapasowa Azure. |C:\Program Files\Microsoft Azure Recovery usługi agenta |
| / s: "Lokalizacja" |Ścieżka do folderu pamięci podręcznej agenta usługi Kopia zapasowa Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Zezwól na usługi Microsoft Update |- |
| /nu |Nie sprawdzaj aktualizacji po ukończeniu instalacji |- |
| /d |Odinstalowuje agenta usług odzyskiwania Microsoft Azure |- |
| /pH |Adres hosta serwera proxy |- |
| /Po |Numer portu hosta serwera proxy |- |
| /pu |Nazwa użytkownika serwera proxy hosta |- |
| /PW |Hasło serwera proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Rejestracja systemu Windows Server lub komputer kliencki z systemem Windows w magazynie usług odzyskiwania
Po utworzeniu magazynu usług odzyskiwania, Pobierz najnowszą wersję agenta i poświadczenia magazynu i zapisz go w dogodnym miejscu, podobnie jak C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

Na serwerze systemu Windows lub komputera klienckiego z systemem Windows, uruchom [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) polecenia cmdlet, aby zarejestrować komputer w magazynie.
To i inne polecenia cmdlet używane do tworzenia kopii zapasowych, są w module MSONLINE, który Mars AgentInstaller dodany jako część procesu instalacji. 

Instalator agenta nie aktualizuje $Env: PSModulePath zmiennej. Oznacza to, że automatyczne ładowanie modułu nie powiodło się. Aby rozwiązać ten problem można wykonaj następujące czynności:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules
```

Alternatywnie można ręcznie załadować moduł skryptu w następujący sposób:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Po załadowaniu poleceń cmdlet programu kopii zapasowej Online, zarejestruj się poświadczenia magazynu:


```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Aby określić plik poświadczeń magazynu nie należy używać ścieżek względnych. Należy podać ścieżkę bezwzględną jako dane wejściowe polecenia cmdlet.
>
>

## <a name="networking-settings"></a>Ustawienia sieciowe
W przypadku połączenia komputera z systemem Windows do Internetu za pośrednictwem serwera proxy, ustawienia serwera proxy można również podać agenta. W tym przykładzie nie żadnego serwera proxy, dlatego firma Microsoft są jawnie wyczyszczenie żadnych informacji dotyczących serwera proxy.

Przepustowości można również sterować za pomocą opcji ```work hour bandwidth``` i ```non-work hour bandwidth``` dla danego zestawu dni tygodnia.

Ustawienie szczegóły serwera proxy i przepustowości jest wykonywane przy użyciu [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) polecenia cmdlet:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Ustawienia szyfrowania
Dane kopii zapasowej wysyłane do usługi Kopia zapasowa Azure są szyfrowane w chronieniu poufności danych. Hasło szyfrowania jest "password" do odszyfrowania danych w czasie przywracania.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Zachowaj informacje hasło bezpieczne po ustawieniu. Nie można przywrócić dane z platformy Azure, bez tego hasła.
>
>

## <a name="back-up-files-and-folders"></a>Tworzenie kopii zapasowych plików i folderów
Wszystkie kopie zapasowe i serwery z systemem Windows Azure Backup zarządzane przez zasady. Zasady składa się z trzech części:

1. A **harmonogram tworzenia kopii zapasowych** określający, gdy kopie zapasowe muszą być podjęte i zsynchronizowane z usługą.
2. A **harmonogramu przechowywania** określająca czas przechowywania punktów odzyskiwania na platformie Azure.
3. A **określania włączenia/wyłączenia pliku** które nakazują, co należy utworzyć kopię.

W tym dokumencie ponieważ będziemy w przypadku automatyzacji kopii zapasowej, przyjęto będzie założenie, że nic nie skonfigurowano. Zaczniemy poprzez tworzenie na podstawie nowych zasad tworzenia kopii zapasowej [OBPolicy nowy](https://technet.microsoft.com/library/hh770416.aspx) polecenia cmdlet.

```
PS C:\> $newpolicy = New-OBPolicy
```

W tym momencie zasady jest pusta i innych poleceń cmdlet są wymagane do zdefiniowania, jakie elementy będą dołączone lub wykluczone, kiedy kopii zapasowych zostanie wykonane, a w przypadku, gdy kopie zapasowe będą przechowywane.

### <a name="configuring-the-backup-schedule"></a>Konfigurowanie harmonogramu tworzenia kopii zapasowych
Pierwsza z 3 części zasad jest harmonogram tworzenia kopii zapasowych, który jest tworzony przy użyciu [OBSchedule nowy](https://technet.microsoft.com/library/hh770401) polecenia cmdlet. Harmonogram tworzenia kopii zapasowych definiuje, gdy kopie zapasowe należy podjąć. Podczas tworzenia harmonogramu, musisz podać parametry wejściowe 2:

* **Dni tygodnia** uruchomienia tworzenia kopii zapasowej. Można uruchomić zadanie tworzenia kopii zapasowej na tylko jeden dzień lub każdego dnia, tygodnia lub dowolnej kombinacji między.
* **Porach dnia** uruchomienia tworzenia kopii zapasowej. Można określić maksymalnie 3 różnych porach dnia, gdy zostanie wywołane kopii zapasowej.

Na przykład można skonfigurować zasad tworzenia kopii zapasowej, wykonywany o 16: 00 w każdą sobotę i niedziela.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

Harmonogram tworzenia kopii zapasowych musi być skojarzone z zasadami i można to osiągnąć przy użyciu [OBSchedule zestaw](https://technet.microsoft.com/library/hh770407) polecenia cmdlet.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Konfigurowania zasad przechowywania
Zasady przechowywania określają, jak długo są przechowywane punkty odzyskiwania utworzone na podstawie zadania tworzenia kopii zapasowej. Podczas tworzenia nowych zasad przechowywania, przy użyciu [OBRetentionPolicy nowy](https://technet.microsoft.com/library/hh770425) polecenia cmdlet, można określić liczbę dni, które muszą być przechowywane w usłudze Kopia zapasowa Azure punktów odzyskiwania. W poniższym przykładzie ustawia zasady przechowywania wynosi 7 dni.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zasady przechowywania muszą być skojarzone z zasadami głównego za pomocą polecenia cmdlet [OBRetentionPolicy zestaw](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

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
### <a name="including-and-excluding-files-to-be-backed-up"></a>Włączanie i wyłączanie plików do wykonania kopii zapasowej
```OBFileSpec``` Obiekt definiuje pliki mają być uwzględnione i wykluczone w kopii zapasowej. Jest to zestaw reguł, które zakres chronionych plików i folderów na komputerze. Może mieć wiele pliku reguł dołączania lub wykluczania zgodnie z wymaganiami i skojarzyć je z zasadami. Tworząc nowy obiekt OBFileSpec, można:

* Określ pliki i foldery do uwzględnienia
* Określ pliki i foldery do wykluczenia
* Określ rekursywne tworzenie kopii zapasowych danych w folderze (lub) czy tylko pliki najwyższego poziomu w określonym folderze należy utworzyć kopię zapasową.

Rozwinięciu przy użyciu flagi - Nierekurencyjny polecenia New-OBFileSpec.

W poniższym przykładzie możemy utworzyć kopię zapasową woluminu, C: i D: i wykluczanie plików binarnych systemu operacyjnego w folderze systemu Windows i foldery tymczasowe. Aby to zrobić, utworzymy dwóch plików przy użyciu specyfikacji [OBFileSpec nowy](https://technet.microsoft.com/library/hh770408) polecenia cmdlet — jeden dla dołączania i jeden do wykluczenia. Po utworzeniu specyfikacji pliku są powiązane z zasad przy użyciu opcji [OBFileSpec Dodaj](https://technet.microsoft.com/library/hh770424) polecenia cmdlet.

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

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


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

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
Teraz ten obiekt zasad została zakończona i ma skojarzone harmonogram tworzenia kopii zapasowych, zasad przechowywania i włączenia/wyłączenia lista plików. Te zasady można teraz zatwierdzona dla usługi Kopia zapasowa Azure do użycia. Przed zastosowaniem zasad nowo utworzony upewnij się, że nie ma żadnych istniejących zasad tworzenia kopii zapasowej skojarzone z serwerem przy użyciu [OBPolicy Usuń](https://technet.microsoft.com/library/hh770415) polecenia cmdlet. Usuwanie zasad wyświetli monit o potwierdzenie. Aby pominąć użycie potwierdzenia ```-Confirm:$false``` flagę za pomocą polecenia cmdlet.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Zatwierdzania obiektu zasad jest wykonywane przy użyciu [OBPolicy zestaw](https://technet.microsoft.com/library/hh770421) polecenia cmdlet. Spowoduje to również monituje o potwierdzenie. Aby pominąć użycie potwierdzenia ```-Confirm:$false``` flagę za pomocą polecenia cmdlet.

```
PS C:> Set-OBPolicy -Policy $newpolicy
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

Można wyświetlić szczegółowe informacje o istniejących zasad tworzenia kopii zapasowej przy użyciu [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) polecenia cmdlet. Można przejść za pomocą [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) polecenia cmdlet dla harmonogramu tworzenia kopii zapasowych i [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) polecenia cmdlet dla zasad przechowywania

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
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

### <a name="performing-an-ad-hoc-backup"></a>Wykonywanie kopii zapasowej w ad hoc
Po ustawieniu zasad tworzenia kopii zapasowej kopie zapasowe zostanie przeprowadzona na harmonogram. Wyzwolenie ad hoc kopii zapasowej jest także możliwe przy użyciu [Start OBBackup](https://technet.microsoft.com/library/hh770426) polecenia cmdlet:

```
PS C:> Get-OBPolicy | Start-OBBackup
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

## <a name="restore-data-from-azure-backup"></a>Przywróć dane z usługi Kopia zapasowa Azure
Ta sekcja przeprowadzi Cię przez kolejne kroki do automatyzacji odzyskiwanie danych z kopii zapasowej Azure. Dzięki temu obejmuje następujące kroki:

1. Wybierz wolumin źródłowy
2. Wybierz punktu kopii zapasowej do przywrócenia
3. Wybierz element, aby przywrócić
4. Wyzwalacz proces przywracania

### <a name="picking-the-source-volume"></a>Pobrania woluminu źródłowego
Aby przywrócić element z kopii zapasowej Azure, należy najpierw określić źródło elementu. Ponieważ firma Microsoft w przypadku wykonywania polecenia w kontekście systemu Windows Server lub klienta systemu Windows, komputer jest już zidentyfikowany. Następnym krokiem w identyfikacji źródła jest zidentyfikować woluminu zawierającego go. Listę woluminów lub źródła Trwa wykonywanie kopii zapasowej z tego komputera mogą być pobierane, wykonując [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) polecenia cmdlet. To polecenie zwraca tablicę wszystkich źródeł kopii zapasowej z serwera/klienta.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Wybieranie punktu kopii zapasowej, z której można przywrócić
Pobierz listę punktów kopii zapasowych, wykonując [Get OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) polecenie cmdlet z odpowiednimi parametrami. W naszym przykładzie wybrano najnowszego punktu kopii zapasowej woluminu źródłowego *D:* i używać go do odzyskania określonego pliku.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
Obiekt ```$rps``` jest tablicą punktów kopii zapasowej. Pierwszy element jest najnowszy punkt i n-tego elementu jest starsze punkty. Aby wybrać najnowszy punkt, będziemy używać ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Wybranie elementu do przywrócenia
Aby zidentyfikować dokładną plik lub folder do przywrócenia, użyj rekursywnie [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) polecenia cmdlet. W ten sposób hierarchię folderów można przeglądać wyłącznie przy użyciu ```Get-OBRecoverableItem```.

W tym przykładzie, jeśli chcemy przywrócić plik *finances.xls* firma Microsoft może odwoływać się który przy użyciu obiektu ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Możesz również wyszukać elementy do przywrócenia przy użyciu ```Get-OBRecoverableItem``` polecenia cmdlet. W naszym przykładzie, aby wyszukać *finances.xls* firma Microsoft można uzyskać dojścia do pliku, uruchamiając poniższe polecenie:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Wyzwolenie procesu przywracania
Aby wyzwolić procesu przywracania, najpierw musimy Określ opcje odzyskiwania. Można to zrobić za pomocą [OBRecoveryOption nowy](https://technet.microsoft.com/library/hh770417.aspx) polecenia cmdlet. Na przykład załóżmy, że chcemy przywrócić pliki *C:\temp*. Załóżmy również, że chcemy pominąć pliki, które już istnieją w folderze docelowym *C:\temp*. Aby utworzyć opcji odzyskiwania, użyj następującego polecenia:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Teraz uruchomić proces przywracania, używając [Start OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) polecenia na wybranym ```$item``` z danych wyjściowych ```Get-OBRecoverableItem``` polecenia cmdlet:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalowanie agenta usługi Kopia zapasowa Azure
Odinstalowanie agenta usługi Kopia zapasowa Azure może odbywać się przy użyciu następującego polecenia:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Odinstalowywanie pliki binarne agenta na komputerze ma niektórych konsekwencje wziąć pod uwagę:

* Usuwa filtr plików z komputera, a zatrzymania śledzenia zmian.
* Wszystkie informacje o zasadach jest usuwany z komputera, ale informacje o zasadach nadal mają być przechowywane w usłudze.
* Wszystkie harmonogramy tworzenia kopii zapasowej są usuwane i są pobierane nie dalsze kopii zapasowych.

Jednak dane przechowywane w pozostaje Azure i są przechowywane zgodnie z ustawień zasad przechowywania przez użytkownika. Starsze punkty automatycznie są przestarzałe.

## <a name="remote-management"></a>Zdalne zarządzanie
Całe Zarządzanie wokół agenta usługi Kopia zapasowa Azure, zasady i źródłami danych mogą to robić zdalnie za pomocą programu PowerShell. Komputer, który będzie zarządzany zdalnie musi poprawnie przygotowany.

Domyślnie Usługa WinRM jest skonfigurowany do uruchamiania ręcznego. Typ uruchamiania musi mieć ustawioną *automatyczne* i można uruchomić usługi. Aby sprawdzić, czy Usługa WinRM jest uruchomiona, wartość właściwości Stan powinien być *systemem*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Programu PowerShell, należy skonfigurować dla niego komunikację zdalną.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

Komputer teraz można zarządzać zdalnie — począwszy od instalacji agenta. Na przykład poniższy skrypt kopiuje agenta z komputerem zdalnym i instaluje je.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o kopii zapasowej dla systemu Windows Server/klienta Azure, zobacz

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [Wykonaj kopię zapasową serwerów z systemem Windows](backup-configure-vault.md)
