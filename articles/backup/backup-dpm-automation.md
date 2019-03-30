---
title: Usługa Azure Backup — Użyj programu PowerShell do tworzenia kopii zapasowych obciążeń programu DPM
description: Dowiedz się, jak wdrażać i zarządzać nimi dla Data Protection Manager (DPM) przy użyciu programu PowerShell usługi Azure Backup
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: b16963265c971e604f03b51fd63f7fe411bab36e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651844"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Wdrażanie kopii zapasowych serwerów Data Protection Manager (DPM) na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell

W tym artykule przedstawiono sposób instalacji usługi Azure Backup na serwerze programu DPM przy użyciu programu PowerShell, a do zarządzania i przywracania kopii zapasowych.

## <a name="setting-up-the-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Zanim użyjesz programu PowerShell do zarządzania kopiami zapasowymi z programu Data Protection Manager na platformie Azure, musisz mieć odpowiednie środowisko w programie PowerShell. Na początku sesji programu PowerShell upewnij się, że uruchom następujące polecenie, aby zaimportować odpowiednie moduły i pozwalają na poprawne odwołania aplety poleceń DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Konfiguracja i rejestracja

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć, [Pobierz najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps).

Następujące zadania instalację i rejestrację można zautomatyzować za pomocą programu PowerShell:

* Tworzenie magazynu usługi Recovery Services
* Instalowanie agenta usługi Azure Backup
* Rejestrowanie przy użyciu usługi Azure Backup
* Ustawienia sieci
* Ustawienia szyfrowania

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Następujące kroki przeprowadzą Cię przez proces tworzenia magazynu usługi Recovery Services. Magazyn usługi Recovery Services jest inny niż magazynu kopii zapasowych.

1. Jeśli używasz usługi Azure Backup po raz pierwszy, musisz użyć **AzResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Magazyn usługi Recovery Services jest zasobu usługi ARM, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub Utwórz nową. Podczas tworzenia nowej grupy zasobów, określ nazwę i lokalizację grupy zasobów.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Użyj **New AzRecoveryServicesVault** polecenia cmdlet, aby utworzyć nowy magazyn. Pamiętaj określić lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Określenie typu nadmiarowości magazynu, które mają być używane; Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Poniższy przykład pokazuje, że ustawiono opcję - BackupStorageRedundancy testVault GeoRedundant.

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Użyj **Get AzRecoveryServicesVault** Aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Można użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn lub aby zobaczyć, jakie magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie Get-AzRecoveryServicesVault i wszystkich magazynów w subskrypcji są wyświetlane.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalowanie agenta usługi Azure Backup na serwerze programu DPM

Przed zainstalowaniem agenta usługi Azure Backup, musisz mieć Instalatora pobrane i są obecne w systemie Windows Server. Możesz pobrać najnowszą wersję Instalatora z [Microsoft Download Center](https://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu usługi Recovery Services. Zapisanie Instalatora, aby łatwo dostępnej lokalizacji, takich jak * C:\Downloads\*.

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień **na serwerze programu DPM**:

```powershell
MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta z opcjami domyślnymi. Instalacja potrwa kilka minut w tle. Jeśli nie określisz */nu* opcji **Windows Update** zostanie otwarte okno po zakończeniu instalacji sprawdź, czy jakiekolwiek aktualizacje.

Agent jest wyświetlana na liście zainstalowanych programów. Aby wyświetlić listę zainstalowanych programów, przejdź do **Panelu sterowania** > **programy** > **programy i funkcje**.

![Agent jest zainstalowany](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opcje instalacji

Aby wyświetlić wszystkie opcje dostępne za pośrednictwem wiersza polecenia, użyj następującego polecenia:

```powershell
MARSAgentInstaller.exe /?
```

Dostępne opcje to:

| Opcja | Szczegóły | Domyślne |
| --- | --- | --- |
| /q |Instalację cichą |- |
| /p:"location" |Ścieżka do folderu instalacji agenta usługi Azure Backup. |Agent usług C:\Program Files\Microsoft Azure Recovery Services |
| /s:"location" |Ścieżka do folderu pamięci podręcznej dla agenta usługi Kopia zapasowa Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Zoptymalizowany pod kątem w usłudze Microsoft Update |- |
| /nu |Nie sprawdzaj aktualizacje po ukończeniu instalacji |- |
| /d |Odinstalowuje agenta usług odzyskiwania Microsoft Azure |- |
| /ph |Adres hosta proxy |- |
| /po |Numer portu hosta serwera proxy |- |
| /pu |Nazwa użytkownika hosta serwera proxy |- |
| /pw |Hasło serwera proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Rejestrowanie programu DPM do odzyskiwania usług magazynu

Po utworzeniu magazynu usługi Recovery Services, Pobierz najnowszą wersję agenta i poświadczenia magazynu i zapisz go w dogodnym miejscu, takich jak C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serwerze programu DPM Uruchom [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) polecenia cmdlet, aby zarejestrować maszynę w magazynie.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Ustawienia konfiguracji początkowej

Po zarejestrowaniu serwera programu DPM przy użyciu magazynu usługi Recovery Services rozpoczyna się przy użyciu domyślnych ustawień subskrypcji. Te ustawienia subskrypcji obejmują sieci, szyfrowania i obszaru przejściowego. Aby zmienić ustawienia subskrypcji, musisz najpierw pobrać dojścia na istniejące ustawienia (ustawienie domyślne), za pomocą [Get DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) polecenia cmdlet:

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Wszystkie zmiany zostaną wprowadzone do tego lokalnego obiektu programu PowerShell ```$setting``` a następnie pełna obiekt wszelkich starań, aby program DPM i usługi Azure Backup, aby zapisać je przy użyciu [DPMCloudSubscriptionSetting zestaw](https://technet.microsoft.com/library/jj612791) polecenia cmdlet. Należy użyć ```–Commit``` flagi, aby upewnić się, że zmiany są zachowywane. Ustawienia nie będą stosowane i używane przez usługę Azure Backup, o ile nie zatwierdzone.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Networking

W przypadku łączności maszyny programu DPM dla usługi Azure Backup w Internecie za pośrednictwem serwera proxy, ustawienia serwera proxy należy podać pomyślnego tworzenia kopii zapasowych. Jest to wykonywane przy użyciu ```-ProxyServer```i ```-ProxyPort```, ```-ProxyUsername``` i ```ProxyPassword``` parametrów za pomocą [DPMCloudSubscriptionSetting zestaw](https://technet.microsoft.com/library/jj612791) polecenia cmdlet. W tym przykładzie istnieje żaden serwer proxy, dzięki czemu możemy są jawnie wyczyścić wszystkie informacje dotyczące serwera proxy.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Można także kontrolować użycie przepustowości przy użyciu opcji ```-WorkHourBandwidth``` i ```-NonWorkHourBandwidth``` dla danego zestawu dni tygodnia. W tym przykładzie firma Microsoft nie ustawiono żadnych ograniczania przepustowości.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurowanie obszaru przemieszczania

Agent usługi Azure Backup, uruchomiony na serwerze programu DPM wymaga tymczasowego magazynu dla danych przywróconych z chmury (lokalny obszar przemieszczania). Konfigurowanie przy użyciu obszaru przemieszczania [DPMCloudSubscriptionSetting zestaw](https://technet.microsoft.com/library/jj612791) polecenia cmdlet i ```-StagingAreaPath``` parametru.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

W powyższym przykładzie obszaru przemieszczania zostanie ustawiony na *C:\StagingArea* w obiekcie programu PowerShell ```$setting```. Upewnij się, że określony folder już istnieje; w przeciwnym razie ostatecznego zatwierdzenia ustawień subskrypcji zakończy się niepowodzeniem.

### <a name="encryption-settings"></a>Ustawienia szyfrowania

Wysyłane do usługi Azure Backup dane kopii zapasowej jest szyfrowany do ochrony poufności danych. Hasło szyfrowania jest "password" do odszyfrowania danych w czasie przywracania. Należy zachować te informacje, bezpieczny po ustawieniu.

W poniższym przykładzie pierwsze polecenie konwertuje ciąg ```passphrase123456789``` bezpieczny ciąg i przypisuje bezpieczny ciąg do zmiennej o nazwie ```$Passphrase```. drugie polecenie powoduje ustawienie bezpieczny ciąg ```$Passphrase``` jako hasła do szyfrowania kopii zapasowych.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Zachowaj informacje hasła bezpieczny, po ustawieniu. Nie można przywrócić dane z platformy Azure bez tego hasła.
>
>

W tym momencie powinna wprowadzono wszystkie zmiany wymagane w celu ```$setting``` obiektu. Pamiętaj, aby zatwierdzić zmiany.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrona danych w usłudze Azure Backup

W tej sekcji spowoduje dodanie serwera produkcyjnego do programu DPM, a następnie włącz ochronę danych do lokalnego magazynu programu DPM, a następnie do usługi Azure Backup. W przykładach pokażemy sposób wykonywania kopii zapasowych plików i folderów. Łatwo można rozszerzyć logikę do tworzenia kopii zapasowych dowolnego źródła danych obsługiwane przez program DPM. Kopie zapasowe programu DPM są regulowane przez ochronę grupy (PG) z czterech części:

1. **Członkowie grupy** znajduje się lista wszystkich obiekty chronione (nazywane również *źródeł danych* w programie DPM), którą chcesz chronić w tej samej grupie ochrony. Na przykład można chronić produkcyjnych maszyn wirtualnych w jednej grupie ochrony i baz danych SQL Server w innej grupie ochrony, jak mogą one mieć różne wymagania kopii zapasowej. Przed utworzeniem kopii zapasowej dowolnego źródła danych na serwerze produkcyjnym, czego potrzebujesz, aby upewnić się, Agent programu DPM jest zainstalowany na serwerze i jest zarządzana przez program DPM. Postępuj zgodnie z instrukcjami dla [Instalowanie agenta programu DPM](https://technet.microsoft.com/library/bb870935.aspx) i połączenie go do odpowiedniego serwera programu DPM.
2. **Metoda ochrony danych** Określa docelowy kopii zapasowej lokalizacje — taśma, dysk i chmura. W tym przykładzie firma Microsoft będzie chronić dane na dysku lokalnym i w chmurze.
3. A **harmonogram tworzenia kopii zapasowych** określający, kiedy należy wykonać kopie zapasowe i jak często dane powinny być synchronizowane między serwerem programu DPM i serwerze produkcyjnym.
4. A **harmonogram przechowywania** określająca czas przechowywania punktów odzyskiwania na platformie Azure.

### <a name="creating-a-protection-group"></a>Utworzenie grupy ochrony

Rozpocznij od utworzenia nowej grupy ochrony, używając [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) polecenia cmdlet.

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Powyższego polecenia cmdlet spowoduje utworzenie grupy ochrony, o nazwie *ProtectGroup01*. Istniejącej grupy ochrony można także modyfikować później dodać kopii zapasowych w chmurze platformy Azure. Aby wprowadzić zmiany do grupy ochrony — nowej lub istniejącej — Potrzebujemy jednak można pobrać uchwytu *można modyfikować* przy użyciu [Get DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) polecenia cmdlet.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Dodawanie członków grupy do grupy ochrony

Lista źródeł danych na serwerze, na którym jest zainstalowany na wie, każdego agenta programu DPM. Aby dodać źródło danych do grupy ochrony, agenta programu DPM musi najpierw wysyłały listę źródeł danych na serwer programu DPM. Jeden lub więcej źródeł danych są następnie wybrany i dodane do grupy ochrony. Dostępne są następujące kroki programu PowerShell, niezbędne do osiągnięcia tego:

1. Pobierz listę wszystkich serwerów zarządzanych przez program DPM za pomocą agenta programu DPM.
2. Wybierz konkretny serwer.
3. Pobierz listę wszystkich źródeł danych na serwerze.
4. Wybierz jeden lub więcej źródeł danych, a następnie dodać je do grupy ochrony

Lista serwerów, na których zainstalowano agenta programu DPM i jest zarządzany przez serwer programu DPM jest uzyskiwana z [Get DPMProductionServer](https://technet.microsoft.com/library/hh881600) polecenia cmdlet. W tym przykładzie firma Microsoft będzie filtrować i skonfigurować tylko PS o nazwie *productionserver01* do utworzenia kopii zapasowej.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains “productionserver01”}
```

Teraz pobrać listę źródeł danych w ```$server``` przy użyciu [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) polecenia cmdlet. W tym przykładzie firma Microsoft filtrowania dla woluminu *D:\\*  , chcemy skonfigurować dla kopii zapasowej. To źródło danych jest dodawane do grupy ochrony za pomocą [DPMChildDatasource Dodaj](https://technet.microsoft.com/library/hh881732) polecenia cmdlet. Pamiętaj, aby używać *można modyfikować* obiektu grupy ochrony ```$MPG``` uzupełnianie.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains “D:\” }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Powtórz ten krok tyle razy, zgodnie z wymaganiami, wszystkie wybrane źródła danych zostały dodane do grupy ochrony. Można również rozpoczynać się tylko jedno źródło danych i Ukończ przepływ pracy tworzenia grupy ochrony i później dodać więcej źródeł danych do grupy ochrony.

### <a name="selecting-the-data-protection-method"></a>Wybieranie metody ochrony danych

Po źródeł danych zostały dodane do grupy ochrony, następnym krokiem jest określenie, przy użyciu metody ochrony [DPMProtectionType zestaw](https://technet.microsoft.com/library/hh881725) polecenia cmdlet. W tym przykładzie grupa ochrony to ustawienie dysk lokalny, a kopia zapasowa w chmurze. Należy również określić źródło danych, który chcesz chronić do chmury przy użyciu [DPMChildDatasource Dodaj](https://technet.microsoft.com/library/hh881732.aspx) polecenia cmdlet flagą - Online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ustawianie zakresu przechowywania

Ustaw okres przechowywania dla kopii zapasowej wskazuje na to, za pomocą [DPMPolicyObjective zestaw](https://technet.microsoft.com/library/hh881762) polecenia cmdlet. Chociaż może się wydawać nieparzysta ustawić okres przechowywania, zanim zdefiniowano harmonogram tworzenia kopii zapasowych, przy użyciu ```Set-DPMPolicyObjective``` polecenia cmdlet automatycznie ustawia domyślny harmonogram tworzenia kopii zapasowej, który może być modyfikowany. Zawsze jest możliwe do zestawu kopii zapasowych, planowanie najpierw i zasad przechowywania po.

W poniższym przykładzie polecenie cmdlet ustawia parametry przechowywania kopii zapasowych na dyskach. To zachowa kopii zapasowych dla 10 dni i synchronizowanie danych co 6 godzin między serwerem produkcyjnym i serwerze programu DPM. ```SynchronizationFrequencyMinutes``` Nie definiuje, jak często punktu kopii zapasowej jest tworzony, ale jak często dane są kopiowane do serwera programu DPM.  To ustawienie uniemożliwia tworzenie kopii zapasowych staje się zbyt duży.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Kopii zapasowych, przechodząc na platformę Azure (Program DPM odwołuje się do nich jako kopie zapasowe Online) zakresów przechowywania można skonfigurować dla [długotrwałego przechowywania przy użyciu schematu dziadek-ojciec-syn. (GFS)](backup-azure-backup-cloud-as-tape.md). Oznacza to można zdefiniować zasady przechowywania połączone obejmujące codziennie, co tydzień, miesięczne i roczne zasady przechowywania. W tym przykładzie, możemy utworzyć tablicę, reprezentujący schemat przechowywania złożony, który chcemy, a następnie skonfiguruj, przy użyciu zakresu przechowywania [DPMPolicyObjective zestaw](https://technet.microsoft.com/library/hh881762) polecenia cmdlet.

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ustaw harmonogram tworzenia kopii zapasowych

Program DPM domyślny harmonogram tworzenia kopii zapasowej automatycznie ustawia w przypadku określenia ochronę celu za pomocą ```Set-DPMPolicyObjective``` polecenia cmdlet. Aby zmienić domyślne harmonogramy, użyj [Get DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) polecenia cmdlet, o których następuje [DPMPolicySchedule zestaw](https://technet.microsoft.com/library/hh881723) polecenia cmdlet.

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

W powyższym przykładzie ```$onlineSch``` jest tablicą z czterema elementami, który zawiera istniejący harmonogram ochrony w trybie online dla grupy ochrony w schemacie GFS:

1. ```$onlineSch[0]``` zawiera Dzienny harmonogram
2. ```$onlineSch[1]``` zawiera harmonogramu tygodniowego
3. ```$onlineSch[2]``` zawiera harmonogramu co miesiąc
4. ```$onlineSch[3]``` zawiera roczne harmonogramu

Dlatego jeśli trzeba zmodyfikować harmonogram tygodniowy, należy do odwoływania się do ```$onlineSch[1]```.

### <a name="initial-backup"></a>Początkowa kopia zapasowa

Podczas tworzenia kopii zapasowej źródła danych po raz pierwszy, program DPM musi powoduje utworzenie repliki początkowej, tworzy pełną kopię źródła danych, które mają być chronione na woluminie repliki programu DPM. To działanie, albo można zaplanować na określoną godzinę lub może być uruchamiane ręcznie, przy użyciu [DPMReplicaCreationMethod zestaw](https://technet.microsoft.com/library/hh881715) polecenia cmdlet z parametrem ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Zmiana rozmiaru repliki programu DPM i wolumin punktu odzyskiwania

Możesz również zmienić rozmiar woluminu repliki programu DPM i kopii w tle woluminu za pomocą [DPMDatasourceDiskAllocation zestaw](https://technet.microsoft.com/library/hh881618.aspx) polecenia cmdlet, jak w poniższym przykładzie: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Zatwierdzanie zmian do grupy ochrony

Na koniec zmiany muszą zostać zatwierdzone, zanim program DPM może potrwać kopii zapasowych na nową konfigurację grupy ochrony. Można to osiągnąć przy użyciu [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) polecenia cmdlet.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Wyświetlanie punktów kopii zapasowej

Możesz użyć [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) polecenia cmdlet, aby uzyskać listę wszystkich punktów odzyskiwania dla źródła danych. W tym przykładzie obejmuje następujące czynności:

* Pobierz wszystkie PGA na serwerze programu DPM i przechowywane w tablicy ```$PG```
* Pobierz źródła danych, odpowiadający ```$PG[0]```
* Pobierz wszystkie punkty odzyskiwania dla źródła danych.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Przywracanie danych chronionych na platformie Azure

Przywracanie danych jest kombinacją ```RecoverableItem``` obiektu i ```RecoveryOption``` obiektu. W poprzedniej sekcji mamy listę punktów kopii zapasowej dla źródła danych.

W poniższym przykładzie pokażemy, jak przywrócić maszynę wirtualną funkcji Hyper-V z usługi Azure Backup, łącząc punktów kopii zapasowej z elementem docelowym odzyskiwania. Ten przykład obejmuje:

* Utworzenie przy użyciu opcji odzyskiwania [New DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) polecenia cmdlet.
* Pobieranie tablicy punktów kopii zapasowej przy użyciu ```Get-DPMRecoveryPoint``` polecenia cmdlet.
* Wybieranie punktu przywracania z kopii zapasowych.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Polecenia można z łatwością rozszerzyć dla dowolnego typu źródła danych.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat usługi Azure Backup w programie DPM zobacz [wprowadzenie do kopii zapasowej programu DPM](backup-azure-dpm-introduction.md)
