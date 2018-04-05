---
title: Kopia zapasowa Azure — tworzenie kopii zapasowej obciążeń programu DPM w programie PowerShell | Dokumentacja firmy Microsoft
description: Informacje o sposobie wdrażania i zarządzania nimi kopia zapasowa Azure dla Data Protection Manager (DPM) przy użyciu programu PowerShell
services: backup
documentationcenter: ''
author: NKolli1
manager: shreeshd
editor: ''
ms.assetid: e9bd223c-2398-4eb1-9bf3-50e08970fea7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: adigan;anuragm;trinadhk;markgal
ms.openlocfilehash: 89dd965208cd473e47de9e0c9bdbfa3ab986c3d5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Wdrażanie kopii zapasowych serwerów Data Protection Manager (DPM) na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell
W tym artykule przedstawiono sposób instalacji usługi Kopia zapasowa Azure na serwerze DPM przy użyciu programu PowerShell, a zarządzanie kopii zapasowych i odzyskiwania.

## <a name="setting-up-the-powershell-environment"></a>Konfigurowanie środowiska PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Przed użyciem programu PowerShell do zarządzania kopiami zapasowymi z programu Data Protection Manager na platformie Azure, musisz mieć prawo środowiska w programie PowerShell. Na początku sesji programu PowerShell upewnij się, że uruchom następujące polecenie, aby zaimportować moduły, prawym i pozwalają na poprawnie odwołują się następujące polecenia cmdlet programu DPM:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Instalację i rejestrację
Aby rozpocząć:

1. [Pobierz najnowsze PowerShell](https://github.com/Azure/azure-powershell/releases) (minimalna wersja wymagana jest: 1.0.0)
2. Włącz przez przełączenie do polecenia cmdlet usługi Kopia zapasowa Azure *AzureResourceManager* trybie przy użyciu **Switch-AzureMode** apletu polecenia:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Następujące zadania instalację i rejestrację można zautomatyzować przy użyciu programu PowerShell:

* Tworzenie magazynu Usług odzyskiwania
* Instalowanie agenta usługi Kopia zapasowa Azure
* Rejestrowanie w usłudze Kopia zapasowa Azure
* Ustawienia sieciowe
* Ustawienia szyfrowania

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Poniższe kroki prowadzi przez proces tworzenia magazynu usług odzyskiwania. Magazyn usług odzyskiwania są inne niż magazynu kopii zapasowych.

1. Jeśli korzystasz z usługi Kopia zapasowa Azure po raz pierwszy, należy użyć **AzureRMResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usług odzyskiwania Azure w ramach subskrypcji.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Magazyn usług odzyskiwania jest zasobem ARM, więc należy umieścić w grupie zasobów. Użyj istniejącej grupy zasobów lub Utwórz nową. Podczas tworzenia nowej grupy zasobów, określ nazwę i lokalizację dla grupy zasobów.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Użyj **AzureRmRecoveryServicesVault nowy** , aby utworzyć nowy magazyn. Należy określić tę samą lokalizację dla magazynu, które było używane dla grupy zasobów.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
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

Uruchom polecenie Get-AzureRmRecoveryServicesVault i są wyświetlane wszystkie magazyny do subskrypcji.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalowanie agenta usługi Kopia zapasowa Azure na serwerze programu DPM
Przed zainstalowaniem agenta usługi Kopia zapasowa Azure, musisz mieć Instalator pobrane i są obecne w systemie Windows Server. Możesz pobrać najnowszą wersję Instalatora z [Microsoft Download Center](http://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu usług odzyskiwania. Zapisanie Instalatora, aby łatwo dostępnej lokalizacji, takich jak * C:\Downloads\*.

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień **na serwerze programu DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta przy użyciu opcji domyślnej. Instalacja trwa kilka minut w tle. Jeśli nie określisz */nu* opcji **usługi Windows Update** zostanie otwarte okno po zakończeniu instalacji sprawdź, czy wszystkie aktualizacje.

Agent zostaną wyświetlone na liście zainstalowanych programów. Aby wyświetlić listę zainstalowanych programów, przejdź do **Panelu sterowania** > **programy** > **programy i funkcje**.

![Agent jest zainstalowany](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Magazyn usługi rejestrowania programu DPM do odzyskiwania
Po utworzeniu magazynu usług odzyskiwania, Pobierz najnowszą wersję agenta i poświadczenia magazynu i zapisz go w dogodnym miejscu, podobnie jak C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serwerze programu DPM Uruchom [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) polecenia cmdlet, aby zarejestrować komputer w magazynie.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Ustawienia konfiguracji początkowej
Gdy serwer DPM jest zarejestrowany w magazynie usług odzyskiwania, rozpoczyna się od domyślnego ustawienia subskrypcji. Te ustawienia subskrypcji obejmują sieci, szyfrowania i obszaru przemieszczania. Aby zmienić ustawienia subskrypcji, należy najpierw uzyskać uchwytu na istniejące ustawienia (ustawienie domyślne), za pomocą [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) polecenia cmdlet:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Wszystkie modyfikacje są dokonywane na ten obiekt programu PowerShell ```$setting``` , a następnie pełne obiektu jest przekazane do programu DPM i kopia zapasowa Azure, aby zapisać je przy użyciu [DPMCloudSubscriptionSetting zestaw](https://technet.microsoft.com/library/jj612791) polecenia cmdlet. Należy użyć ```–Commit``` flagę, aby upewnić się, że zmiany są zachowywane. Ustawienia nie będą stosowane i używane przez usługi Kopia zapasowa Azure, chyba że zostało zatwierdzone.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Networking
W przypadku łączności maszyny programu DPM z usługą kopia zapasowa Azure w sieci internet za pośrednictwem serwera proxy, ustawienia serwera proxy należy podać dla pomyślnie utworzone kopie zapasowe. Jest to zrobić za pomocą ```-ProxyServer```i ```-ProxyPort```, ```-ProxyUsername``` i ```ProxyPassword``` parametrów z [DPMCloudSubscriptionSetting zestaw](https://technet.microsoft.com/library/jj612791) polecenia cmdlet. W tym przykładzie nie żadnego serwera proxy, dlatego firma Microsoft są jawnie wyczyszczenie żadnych informacji dotyczących serwera proxy.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Wykorzystanie przepustowości można również sterować za pomocą opcji ```-WorkHourBandwidth``` i ```-NonWorkHourBandwidth``` dla danego zestawu dni tygodnia. W tym przykładzie firma Microsoft nie ustawienia dowolnej ograniczania.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurowanie obszaru przemieszczania
Agent usługi Kopia zapasowa Azure uruchomionych na serwerze programu DPM wymaga tymczasowego magazynu dla danych przywróconych z chmury (lokalny obszar przemieszczania). Konfigurowanie przy użyciu obszaru przemieszczania [DPMCloudSubscriptionSetting zestaw](https://technet.microsoft.com/library/jj612791) polecenia cmdlet i ```-StagingAreaPath``` parametru.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

W powyższym przykładzie obszar tymczasowy zostanie ustawiona do *C:\StagingArea* w obiekcie środowiska PowerShell ```$setting```. Upewnij się, że podany folder już istnieje, w przeciwnym razie końcowego zatwierdzania ustawień subskrypcji zakończy się niepowodzeniem.

### <a name="encryption-settings"></a>Ustawienia szyfrowania
Dane kopii zapasowej wysyłane do usługi Kopia zapasowa Azure są szyfrowane w chronieniu poufności danych. Hasło szyfrowania jest "password" do odszyfrowania danych w czasie przywracania. Należy zachować te informacje bezpieczne po ustawieniu.

W poniższym przykładzie pierwsze polecenie konwertuje ciąg ```passphrase123456789``` ciąg bezpieczny i przypisuje bezpieczny ciąg do zmiennej o nazwie ```$Passphrase```. drugie polecenie ustawia bezpieczny ciąg w ```$Passphrase``` jako hasło do szyfrowania kopii zapasowych.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Zachowaj informacje hasło bezpieczne po ustawieniu. Nie można przywrócić dane z platformy Azure bez tego hasła.
>
>

W tym momencie powinien wprowadzone wszystkie zmiany wymagane w celu ```$setting``` obiektu. Pamiętaj, aby zatwierdzić zmiany.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrona danych do usługi Kopia zapasowa Azure
W tej sekcji zostanie Dodawanie serwera produkcyjnego do programu DPM, a następnie włącz ochronę danych do lokalnego magazynu programu DPM, a następnie kopia zapasowa Azure. W przykładach przedstawiony zostanie sposób wykonywania kopii zapasowej plików i folderów. Logika można z łatwością rozszerzyć kopii zapasowej dowolnych źródeł danych obsługiwane przez program DPM. Kopii zapasowych programu DPM są regulowane przez ochronę grupy (PG) z czterech części:

1. **Członków grupy** znajduje się lista wszystkich obiekty chronione (nazywane również *źródeł danych* w programie DPM), które mają być chronione w tej samej grupy ochrony. Na przykład można chronić produkcji maszyny wirtualne w jednej grupy ochrony i baz danych programu SQL Server w innej grupy ochrony, ponieważ mogą mieć różne wymagania kopii zapasowej. Przed utworzeniem kopii zapasowej żadnego źródła danych na serwerze produkcyjnym, które należy się upewnić, Agent programu DPM jest zainstalowany na serwerze i jest zarządzany przez program DPM. Wykonaj kroki [zainstalowanie agenta DPM](https://technet.microsoft.com/library/bb870935.aspx) i połączenie go do odpowiedniego serwera programu DPM.
2. **Metoda ochrony danych** Określa docelowy kopii zapasowej lokalizacje - taśmy, dysku i chmury. W naszym przykładzie będzie chronić dane na dysku lokalnym i w chmurze.
3. A **harmonogram tworzenia kopii zapasowych** Określa, kiedy należy podjąć kopii zapasowych i jak często dane powinny być synchronizowane między serwerem DPM i serwerze produkcyjnym.
4. A **harmonogramu przechowywania** określająca czas przechowywania punktów odzyskiwania na platformie Azure.

### <a name="creating-a-protection-group"></a>Utworzenie grupy ochrony
Rozpocznij od utworzenia nowej grupy ochrony za pomocą [DPMProtectionGroup nowy](https://technet.microsoft.com/library/hh881722) polecenia cmdlet.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Powyższe polecenia cmdlet spowoduje utworzenie grupy ochrony o nazwie *ProtectGroup01*. Aby dodać kopii zapasowej w chmurze platformy Azure istniejącej grupy ochrony może być modyfikowany również później. Jednak wprowadzać żadnych zmian do grupy ochrony — nowy lub istniejący - musimy pobrać dojścia *modyfikowalnymi* przy użyciu [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) polecenia cmdlet.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Dodawanie członków grupy do grupy ochrony
Lista źródeł danych na serwerze, na którym jest zainstalowany na wie, każdego agenta programu DPM. Aby dodać źródła danych do grupy ochrony, agenta programu DPM musi najpierw wysłać listę źródeł danych na serwer programu DPM. Jeden lub więcej źródeł danych są następnie i dodane do grupy ochrony. Potrzebne w tym kroki programu PowerShell są:

1. Pobierz listę wszystkich serwerów zarządzanych przez program DPM za pomocą agenta programu DPM.
2. Wybierz określonego serwera.
3. Pobranie listy wszystkich źródeł danych na serwerze.
4. Wybierz co najmniej jednego źródła danych i dodaj je do grupy ochrony

Lista serwerów, na których Agent programu DPM jest zainstalowany i jest zarządzany przez serwer programu DPM są uzyskiwane z [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) polecenia cmdlet. W tym przykładzie firma Microsoft będzie filtrowania i skonfigurować tylko PS o nazwie *productionserver01* do utworzenia kopii zapasowej.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Teraz pobrać listy źródeł danych na ```$server``` przy użyciu [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) polecenia cmdlet. W tym przykładzie mamy filtrowania dla woluminu * D:\* interesujące można skonfigurować dla kopii zapasowej. To źródło danych jest dodawane do grupy ochrony za pomocą [DPMChildDatasource Dodaj](https://technet.microsoft.com/library/hh881732) polecenia cmdlet. Pamiętaj, aby użyć *modyfikowalnymi* obiektu grupy ochrony ```$MPG``` uzupełnianie.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Powtórz ten krok liczbę razy, aż zostaną dodane wszystkie wybrane źródła danych do grupy ochrony. Można również uruchomić z tylko jednego źródła danych i ukończenia przepływu pracy tworzenia grupy ochrony i w późniejszym czasie dodać więcej źródeł danych do grupy ochrony.

### <a name="selecting-the-data-protection-method"></a>Wybieranie metody ochrony danych
Po źródła danych zostały dodane do grupy ochrony, następnym krokiem jest określenie, przy użyciu metody ochrony [DPMProtectionType zestaw](https://technet.microsoft.com/library/hh881725) polecenia cmdlet. W tym przykładzie grupy ochrony jest ustawiony na potrzeby dysku lokalnego, a kopia zapasowa w chmurze. Należy również określić źródło danych, które chcesz chronić do chmury przy użyciu [DPMChildDatasource Dodaj](https://technet.microsoft.com/library/hh881732.aspx) polecenia cmdlet flagą - Online.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ustawianie zakresu przechowywania
Ustawienia przechowywania dla punktów kopii zapasowej za pomocą [DPMPolicyObjective zestaw](https://technet.microsoft.com/library/hh881762) polecenia cmdlet. Gdy może wydawać się nieparzysta ustawień przechowywania, przed rozpoczęciem zdefiniowano harmonogram tworzenia kopii zapasowych, przy użyciu ```Set-DPMPolicyObjective``` polecenie cmdlet automatycznie ustawia domyślny harmonogram tworzenia kopii zapasowej, który może być modyfikowany. Zawsze jest możliwe zestawu kopii zapasowej najpierw zaplanować, jak i zasady przechowywania po.

W poniższym przykładzie polecenia cmdlet do ustawiania parametrów przechowywania kopii zapasowych na dyskach. To zachowują kopie zapasowe 10 dni i synchronizowanie danych co 6 godzin między serwerem produkcyjnym i serwerze programu DPM. ```SynchronizationFrequencyMinutes``` Nie definiuje, jak często punktu kopii zapasowej jest tworzony, ale jak często dane są kopiowane do serwera DPM.  To ustawienie uniemożliwia tworzenie kopii zapasowych za duży.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Kopii zapasowych, przechodząc do platformy Azure (Program DPM odwołuje się do nich jako kopii zapasowych Online) zakresów przechowywania można skonfigurować dla [długoterminowych przechowywania użyciu schematu dziadek-ojciec-syn. (GFS)](backup-azure-backup-cloud-as-tape.md). Oznacza to można zdefiniować zasady przechowywania Scalonej obejmujące codziennie, co tydzień, miesięcznych i rocznych zasady przechowywania. W tym przykładzie, możemy utworzyć tablicy reprezentujący schemat przechowywania złożonych, która ma, a następnie skonfiguruj, przy użyciu zakresu przechowywania [DPMPolicyObjective zestaw](https://technet.microsoft.com/library/hh881762) polecenia cmdlet.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ustaw harmonogram tworzenia kopii zapasowych
Program DPM ustawia automatycznie domyślny harmonogram tworzenia kopii zapasowej, jeśli określisz ochronę celu za pomocą ```Set-DPMPolicyObjective``` polecenia cmdlet. Aby zmienić domyślne harmonogramy, użyj [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) polecenia cmdlet następuje [DPMPolicySchedule zestaw](https://technet.microsoft.com/library/hh881723) polecenia cmdlet.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

W powyższym przykładzie ```$onlineSch``` jest tablicą o czterech elementów zawierający istniejący harmonogram ochrony w trybie online dla grupy ochrony w schemacie GFS:

1. ```$onlineSch[0]``` zawiera harmonogramu dziennego
2. ```$onlineSch[1]``` zawiera harmonogramu tygodniowego
3. ```$onlineSch[2]``` zawiera harmonogramu miesięcznego
4. ```$onlineSch[3]``` zawiera corocznych harmonogramu

Dlatego jeśli trzeba zmodyfikować harmonogram tygodniowy, trzeba odwoływać się do ```$onlineSch[1]```.

### <a name="initial-backup"></a>Początkowa kopia zapasowa
Podczas wykonywania kopii zapasowej źródła danych po raz pierwszy, program DPM musi tworzy repliki początkowej który tworzy pełną kopię źródła danych mają być chronione na woluminie repliki programu DPM. To działanie albo mogą być planowane na określoną godzinę lub mogą być wyzwalane ręcznie, używając [DPMReplicaCreationMethod zestaw](https://technet.microsoft.com/library/hh881715) polecenia cmdlet z parametrem ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Zmiana rozmiaru repliki programu DPM i wolumin punktu odzyskiwania
Możesz również zmienić rozmiar woluminu repliki programu DPM i kopii w tle woluminu przy użyciu [DPMDatasourceDiskAllocation zestaw](https://technet.microsoft.com/library/hh881618.aspx) jak w poniższym przykładzie polecenie cmdlet: Get-DatasourceDiskAllocation - Datasource $DS Set-DatasourceDiskAllocation - Źródło danych $DS - ProtectionGroup $MPG-ręczne - ReplicaArea (2 gb) — ShadowCopyArea (2 gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Zatwierdzenie zmian do grupy ochrony
Na koniec zmiany muszą być zatwierdzone przed programu DPM można wykonać kopię zapasową na nową konfigurację grupy ochrony. Można to osiągnąć przy użyciu [DPMProtectionGroup zestaw](https://technet.microsoft.com/library/hh881758) polecenia cmdlet.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Wyświetlanie punktów kopii zapasowej
Można użyć [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) polecenia cmdlet, aby uzyskać listę wszystkich punktów odzyskiwania dla źródła danych. W tym przykładzie zostaną wykonane następujące czynności:

* Pobierz wszystkie PGA na serwerze DPM i przechowywane w tablicy ```$PG```
* Pobierz odpowiadający źródeł danych ```$PG[0]```
* Pobierz wszystkie punkty odzyskiwania dla źródła danych.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Przywracanie danych chronionych na platformie Azure
Przywracanie danych jest kombinacją ```RecoverableItem``` obiektu i ```RecoveryOption``` obiektu. W poprzedniej sekcji dotarliśmy listę punktów kopii zapasowej dla źródła danych.

W poniższym przykładzie przedstawiony sposób przywracania maszyny wirtualnej funkcji Hyper-V z kopii zapasowej Azure dzięki połączeniu z elementem docelowym odzyskiwania punktów kopii zapasowej. Ten przykład obejmuje:

* Utworzenie przy użyciu opcji odzyskiwania [DPMRecoveryOption nowy](https://technet.microsoft.com/library/hh881592) polecenia cmdlet.
* Tablica punktów kopii zapasowej przy użyciu pobierania ```Get-DPMRecoveryPoint``` polecenia cmdlet.
* Wybieranie punktu kopii zapasowej do przywrócenia z.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Polecenia można z łatwością rozszerzyć dla dowolnego typu źródła danych.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat programu DPM w usłudze Kopia zapasowa Azure zobacz [wprowadzenie do kopii zapasowej programu DPM](backup-azure-dpm-introduction.md)
