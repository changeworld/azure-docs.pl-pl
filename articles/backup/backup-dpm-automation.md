---
title: Używanie programu PowerShell do wytwarzania kopii zapasowych obciążeń programu DPM
description: Dowiedz się, jak wdrażać usługę Azure Backup for Data Protection Manager (DPM) i zarządzać nią za pomocą programu PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 06c138a4015a0b730369e091fc57a34d2190051d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616730"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Wdrażanie kopii zapasowych serwerów Data Protection Manager (DPM) na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell

W tym artykule pokazano, jak używać programu PowerShell do konfigurowania usługi Azure Backup na serwerze programu DPM oraz do zarządzania tworzeniem kopii zapasowych i odzyskiwaniem.

## <a name="setting-up-the-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Aby można było używać programu PowerShell do zarządzania kopiami zapasowymi z programu Data Protection Manager na platformie Azure, musisz mieć odpowiednie środowisko w programie PowerShell. Na początku sesji programu PowerShell upewnij się, że uruchomisz następujące polecenie, aby zaimportować odpowiednie moduły i zezwolić na poprawne odwoływanie się do poleceń cmdlet programu DPM:

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

Aby rozpocząć, [pobierz najnowszą usługę Azure PowerShell](/powershell/azure/install-az-ps).

W programie PowerShell można zautomatyzować następujące zadania konfiguracji i rejestracji:

* Tworzenie magazynu usługi Recovery Services
* Instalowanie agenta usługi Azure Backup
* Rejestrowanie się w usłudze Azure Backup
* Ustawienia sieci
* Ustawienia szyfrowania

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki prowadzą przez tworzenie magazynu usług odzyskiwania. Magazyn usług odzyskiwania różni się od magazynu kopii zapasowych.

1. Jeśli używasz usługi Azure Backup po raz pierwszy, należy użyć polecenia cmdlet **Register-AzResourceProvider,** aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Magazyn usług odzyskiwania jest zasobem ARM, więc należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową. Podczas tworzenia nowej grupy zasobów określ nazwę i lokalizację grupy zasobów.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Użyj polecenia cmdlet **New-AzRecoveryServicesVault,** aby utworzyć nowy magazyn. Należy określić tę samą lokalizację dla przechowalni, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Określ typ nadmiarowości magazynu do użycia; można użyć [magazynu lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego (GRS).](../storage/common/storage-redundancy-grs.md) Poniższy przykład pokazuje -BackupStorageRedundancy opcja dla testVault jest ustawiona na GeoRedundant.

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Użyj **Get-AzRecoveryServicesVault,** aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Za pomocą tego polecenia można sprawdzić, czy utworzono nowy magazyn lub zobaczyć, jakie magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie Get-AzRecoveryServicesVault i wszystkie magazyny w subskrypcji są wymienione.

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

Przed zainstalowaniem agenta usługi Azure Backup należy pobrać instalatora i być obecny na serwerze Windows Server. Najnowszą wersję instalatora można pobrać z [Centrum pobierania Firmy Microsoft](https://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu usług odzyskiwania. Zapisz instalator w łatwo dostępnej lokalizacji, takiej\*jak *C:\Downloads .

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień **na serwerze programu DPM:**

```powershell
MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta ze wszystkimi opcjami domyślnymi. Instalacja trwa kilka minut w tle. Jeśli opcja */nu* nie zostanie określona, okno **Windows Update** zostanie otwarte po zakończeniu instalacji w celu sprawdzenia dostępności aktualizacji.

Agent pojawia się na liście zainstalowanych programów. Aby wyświetlić listę zainstalowanych programów, przejdź do strony Programy**i funkcje** **Panelu** > **sterowania** > .

![Zainstalowany agent](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Rejestrowanie programu DPM w magazynie usług odzyskiwania

Po utworzeniu magazynu usług odzyskiwania pobierz najnowsze poświadczenia agenta i magazynu i przechowuj je w dogodnej lokalizacji, takiej jak C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serwerze programu DPM uruchom polecenie cmdlet [Start-OBRegistration,](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) aby zarejestrować komputer w przechowalni.

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

Po zarejestrowaniu serwera programu DPM w magazynie usług odzyskiwania rozpoczyna się od domyślnych ustawień subskrypcji. Te ustawienia subskrypcji obejmują sieć, szyfrowanie i obszar przemieszczania. Aby zmienić ustawienia subskrypcji, musisz najpierw uzyskać dojście do istniejących (domyślnych) ustawień przy użyciu polecenia cmdlet [Get-DPMCloudSubscriptionSetting:](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019)

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Wszystkie modyfikacje są dokonywane w ```$setting``` tym lokalnym obiekcie programu PowerShell, a następnie pełny obiekt jest zobowiązana do programu DPM i usługi Azure Backup, aby zapisać je przy użyciu polecenia cmdlet [Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) Należy użyć flagi, ```–Commit``` aby upewnić się, że zmiany są utrwalone. Ustawienia nie będą stosowane i używane przez usługę Azure Backup, chyba że zostaną zatwierdzone.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Obsługa sieci

Jeśli łączność komputera programu DPM z usługą Azure Backup w Internecie odbywa się za pośrednictwem serwera proxy, należy zapewnić ustawienia serwera proxy w celu pomyślnego tworzenia kopii zapasowych. Odbywa się to ```-ProxyServer```za ```-ProxyPort``` ```-ProxyUsername``` pomocą ```ProxyPassword``` i , i parametry z [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) polecenie cmdlet. W tym przykładzie nie ma serwera proxy, więc jawnie czyścimy wszelkie informacje związane z serwerem proxy.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Użycie przepustowości można również ```-WorkHourBandwidth``` kontrolować ```-NonWorkHourBandwidth``` za pomocą opcji i dla danego zestawu dni tygodnia. W tym przykładzie nie ustawiamy żadnych ograniczania przepustowości.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurowanie obszaru przemieszczania

Agent usługi Azure Backup uruchomiony na serwerze programu DPM wymaga tymczasowego magazynu dla danych przywróconych z chmury (lokalny obszar przemieszczania). Skonfiguruj obszar przemieszczania za pomocą polecenia cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) i parametru. ```-StagingAreaPath```

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

W powyższym przykładzie obszar przemieszczania zostanie ustawiony na *C:\StagingArea* w obiekcie ```$setting```programu PowerShell . Upewnij się, że określony folder już istnieje, w przeciwnym razie ostateczne zatwierdzenie ustawień subskrypcji zakończy się niepowodzeniem.

### <a name="encryption-settings"></a>Ustawienia szyfrowania

Dane kopii zapasowej wysyłane do usługi Azure Backup są szyfrowane w celu ochrony poufności danych. Hasło szyfrowania to "hasło", które służy do odszyfrowywania danych w czasie przywracania. Ważne jest, aby te informacje były bezpieczne po ich ustawieniu.

W poniższym przykładzie pierwsze polecenie ```passphrase123456789``` konwertuje ciąg na bezpieczny ciąg i ```$Passphrase```przypisuje bezpieczny ciąg do zmiennej o nazwie . drugie polecenie ustawia bezpieczny ```$Passphrase``` ciąg jako hasło do szyfrowania kopii zapasowych.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Po ustawieniu informacji o hasłach należy zachować bezpieczeństwo. Nie będzie można przywrócić danych z platformy Azure bez tego hasła.
>
>

W tym momencie należy dokonać wszystkich wymaganych ```$setting``` zmian w obiekcie. Pamiętaj, aby zatwierdzić zmiany.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrona danych w kopii zapasowej platformy Azure

W tej sekcji dodasz serwer produkcyjny do programu DPM, a następnie ochraniasz dane do lokalnego magazynu programu DPM, a następnie do usługi Azure Backup. W przykładach pokażemy, jak zrobić kopie zapasowe plików i folderów. Logikę można łatwo rozszerzyć, aby utworzyć kopię zapasową dowolnego źródła danych obsługiwanego przez program DPM. Wszystkie kopie zapasowe programu DPM są zarządzane przez grupę ochrony (PG) z czterema częściami:

1. **Członkowie grupy** to lista wszystkich obiektów chronionych (nazywanych również *źródłami danych* w programie DPM), które mają być chronione w tej samej grupie ochrony. Na przykład można chronić maszyny wirtualne produkcyjne w jednej grupie ochrony i bazach danych programu SQL Server w innej grupie ochrony, ponieważ mogą one mieć różne wymagania dotyczące tworzenia kopii zapasowych. Przed utworzeniem kopii zapasowej dowolnego źródła danych na serwerze produkcyjnym należy upewnić się, że agent programu DPM jest zainstalowany na serwerze i jest zarządzany przez program DPM. Wykonaj kroki, aby [zainstalować agenta programu DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) i połączyć go z odpowiednim serwerem programu DPM.
2. **Metoda ochrony danych** określa docelowe lokalizacje kopii zapasowych — taśmę, dysk i chmurę. W naszym przykładzie będziemy chronić dane na dysku lokalnym i w chmurze.
3. **Harmonogram tworzenia kopii zapasowych,** który określa, kiedy należy wykonać kopie zapasowe i jak często dane powinny być synchronizowane między serwerem programu DPM a serwerem produkcyjnym.
4. **Harmonogram przechowywania,** który określa, jak długo zachować punkty odzyskiwania na platformie Azure.

### <a name="creating-a-protection-group"></a>Tworzenie grupy ochrony

Zacznij od utworzenia nowej grupy ochrony przy użyciu polecenia cmdlet [New-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Powyższe polecenie cmdlet utworzy grupę ochrony o nazwie *ProtectGroup01*. Istniejącą grupę ochrony można również później zmodyfikować, aby dodać kopię zapasową do chmury platformy Azure. Jednak aby wprowadzić wszelkie zmiany w grupie ochrony — nowe lub istniejące — musimy uzyskać dojście na *modyfikować* obiektu przy użyciu polecenia cmdlet [Get-DPMModifiableProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019)

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Dodawanie członków grupy do grupy ochrony

Każdy agent programu DPM zna listę źródeł danych na serwerze, na który jest zainstalowany. Aby dodać źródło danych do grupy ochrony, agent programu DPM musi najpierw wysłać listę źródeł danych z powrotem do serwera programu DPM. Następnie wybiera się co najmniej jedno źródło danych i dodaje się je do grupy ochrony. Kroki programu PowerShell potrzebne do osiągnięcia tego celu to:

1. Pobierz listę wszystkich serwerów zarządzanych przez program DPM za pośrednictwem agenta programu DPM.
2. Wybierz określony serwer.
3. Pobierz listę wszystkich źródeł danych na serwerze.
4. Wybieranie jednego lub większej liczby źródeł danych i dodawanie ich do grupy ochrony

Lista serwerów, na których agent programu DPM jest zainstalowany i jest zarządzany przez serwer programu DPM, jest nabywana za pomocą polecenia cmdlet [Get-DPMProductionServer.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) W tym przykładzie będziemy filtrować i tylko skonfigurować PS z nazwa *productionserver01* do tworzenia kopii zapasowych.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Teraz pobierz listę źródeł danych ```$server``` przy użyciu polecenia cmdlet [Get-DPMDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) W tym przykładzie filtrujemy wolumin *D:\\ * który chcemy skonfigurować do tworzenia kopii zapasowych. To źródło danych jest następnie dodawane do grupy ochrony przy użyciu polecenia cmdlet [Add-DPMChildDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) Pamiętaj, aby użyć *zmodyfikowanego* obiektu ```$MPG``` grupy ochrony, aby uzupełnić.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Powtórz ten krok tyle razy, ile jest to wymagane, dopóki nie zostanie dodane wszystkie wybrane źródła danych do grupy ochrony. Można również rozpocząć od tylko jednego źródła danych i zakończyć przepływ pracy do tworzenia grupy ochrony, a w późniejszym momencie dodać więcej źródeł danych do grupy ochrony.

### <a name="selecting-the-data-protection-method"></a>Wybór metody ochrony danych

Po dodaniu źródeł danych do grupy ochrony następnym krokiem jest określenie metody ochrony przy użyciu polecenia cmdlet [Set-DPMProtectionType.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) W tym przykładzie grupa ochrony jest skonfigurowana dla kopii zapasowej dysku lokalnego i chmury. Należy również określić źródło danych, które chcesz chronić w chmurze przy użyciu polecenia cmdlet [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) z flagą -Online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ustawianie zakresu przechowywania

Ustaw retencję punktów kopii zapasowej przy użyciu polecenia cmdlet [Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) Chociaż ustawienie retencji przed zdefiniowaniem harmonogramu tworzenia kopii ```Set-DPMPolicyObjective``` zapasowych może wydawać się dziwne, użycie polecenia cmdlet automatycznie ustawia domyślny harmonogram tworzenia kopii zapasowych, który można następnie zmodyfikować. Zawsze jest możliwe, aby najpierw ustawić harmonogram tworzenia kopii zapasowych i zasady przechowywania po.

W poniższym przykładzie polecenie cmdlet ustawia parametry przechowywania kopii zapasowych dysku. Spowoduje to zachowanie kopii zapasowych przez 10 dni i synchronizowanie danych co 6 godzin między serwerem produkcyjnym a serwerem programu DPM. Nie ```SynchronizationFrequencyMinutes``` określa, jak często tworzony jest punkt kopii zapasowej, ale jak często dane są kopiowane na serwer programu DPM.  To ustawienie zapobiega zbyt dużemu tworzeniu kopii zapasowych.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

W przypadku kopii zapasowych przechodzących na platformę Azure (program DPM odnosi się do nich jako kopie zapasowe online) zakresy przechowywania można skonfigurować do [długoterminowego przechowywania przy użyciu schematu Dziadek-Ojciec-Syn (GFS).](backup-azure-backup-cloud-as-tape.md) Oznacza to, że można zdefiniować połączone zasady przechowywania obejmujące dzienne, tygodniowe, miesięczne i roczne zasady przechowywania. W tym przykładzie tworzymy tablicę reprezentującą złożony schemat przechowywania, który chcemy, a następnie konfigurujemy zakres przechowywania przy użyciu polecenia cmdlet [Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019)

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ustawianie harmonogramu tworzenia kopii zapasowych

Program DPM automatycznie ustawia domyślny harmonogram tworzenia kopii ```Set-DPMPolicyObjective``` zapasowych, jeśli zostanie określony cel ochrony przy użyciu polecenia cmdlet. Aby zmienić domyślne harmonogramy, należy użyć polecenia cmdlet [Get-DPMPolicySchedule,](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) po którym następuje polecenie cmdlet [Set-DPMPolicySchedule.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019)

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

W powyższym ```$onlineSch``` przykładzie jest tablica z czterema elementami, która zawiera istniejący harmonogram ochrony online dla grupy ochrony w schemacie GFS:

1. ```$onlineSch[0]```zawiera dzienny harmonogram
2. ```$onlineSch[1]```zawiera tygodniowy harmonogram
3. ```$onlineSch[2]```zawiera harmonogram miesięczny
4. ```$onlineSch[3]```zawiera roczny harmonogram

Więc jeśli chcesz zmodyfikować harmonogram tygodniowy, musisz ```$onlineSch[1]```odwołać się do .

### <a name="initial-backup"></a>Początkowa kopia zapasowa

Podczas tworzenia kopii zapasowej źródła danych po raz pierwszy program DPM potrzebuje tworzy replikę początkową, która tworzy pełną kopię źródła danych, które mają być chronione na woluminie repliki programu DPM. To działanie można zaplanować na określony czas lub można je wyzwolić ręcznie, używając polecenia cmdlet [Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) z parametrem ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Zmiana rozmiaru woluminu punktu odzyskiwania repliki programu DPM &

Można również zmienić rozmiar woluminu repliki programu DPM i woluminu kopiowania w tle przy użyciu polecenia cmdlet [Set-DPMDatasourceDiskAllocation,](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) jak w poniższym przykładzie: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Zatwierdzanie zmian w grupie ochrony

Na koniec zmiany muszą zostać zatwierdzone, zanim program DPM może wykonać kopię zapasową według nowej konfiguracji grupy ochrony. Można to osiągnąć za pomocą polecenia cmdlet [Set-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Wyświetlanie punktów kopii zapasowej

Polecenia cmdlet [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) można użyć, aby uzyskać listę wszystkich punktów odzyskiwania dla źródła danych. W tym przykładzie:

* pobrać wszystkie obiekty PG na serwerze programu DPM i być przechowywane w macierzy```$PG```
* uzyskać źródła danych odpowiadające```$PG[0]```
* uzyskać wszystkie punkty odzyskiwania dla źródła danych.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Przywracanie danych chronionych na platformie Azure

Przywracanie danych jest kombinacją ```RecoverableItem``` obiektu ```RecoveryOption``` i obiektu. W poprzedniej sekcji otrzymaliśmy listę punktów kopii zapasowej dla źródła danych.

W poniższym przykładzie zademonstrujemy sposób przywracania maszyny wirtualnej funkcji Hyper-V z usługi Azure Backup, łącząc punkty kopii zapasowej z obiektem docelowym do odzyskiwania. W tym przykładzie:

* Tworzenie opcji odzyskiwania przy użyciu polecenia cmdlet [New-DPMRecoveryOption.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019)
* Pobieranie tablicy punktów kopii zapasowej ```Get-DPMRecoveryPoint``` przy użyciu polecenia cmdlet.
* Wybieranie punktu kopii zapasowej do przywrócenia.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Polecenia można łatwo rozszerzyć dla dowolnego typu źródła danych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu DPM do usługi Azure Backup, zobacz [Wprowadzenie do kopii zapasowej programu DPM](backup-azure-dpm-introduction.md)
