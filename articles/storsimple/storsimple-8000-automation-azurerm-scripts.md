---
title: Zarządzanie urządzeniami StorSimple za pomocą skryptów usługi AzureRM PowerShell
description: Dowiedz się, jak zautomatyzować zadania storsimple za pomocą skryptów usługi Azure Resource Manager
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 03a5ef49b2d58d351d882b30b5d11e4a5ba90264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471962"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Zarządzanie urządzeniami StorSimple za pomocą skryptów opartych na zestawie SDK usługi Azure Resource Manager

W tym artykule opisano, jak skrypty oparte na zestawie SDK usługi Azure Resource Manager mogą służyć do zarządzania urządzeniem z serii StorSimple 8000. Przykładowy skrypt jest również dołączony, aby przejść przez kroki konfigurowania środowiska do uruchamiania tych skryptów.

Ten artykuł dotyczy urządzeń z serii StorSimple 8000 działających tylko w witrynie Azure Portal.

## <a name="sample-scripts"></a>Przykładowe skrypty

Poniższe przykładowe skrypty są dostępne do automatyzacji różnych zadań StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela przykładowych skryptów opartych na zestawie SDK usługi Azure Resource Manager

| Skrypt usługi Azure Resource Manager                    | Opis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Autoryzuj-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ten skrypt umożliwia autoryzowanie urządzenia StorSimple w celu zmiany klucza szyfrowania danych usługi.                                                                                                           |
| [Utwórz-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ten skrypt tworzy urządzenie 8010 lub 8020 StorSimple Cloud Appliance. Urządzenie w chmurze można następnie skonfigurować i zarejestrować w usłudze StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ten skrypt tworzy lub modyfikuje woluminy StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ten skrypt zawiera listę wszystkich kopii zapasowych dla urządzenia zarejestrowanego w usłudze StorSimple Device Manager.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ten skrypt wszystkie zasady tworzenia kopii zapasowych dla urządzenia StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ten skrypt pobiera wszystkie zadania StorSimple uruchomione w usłudze StorSimple Device Manager.                                                                                                                     |
| [Get-DeviceUpdateDostępność.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ten skrypt skanuje serwer aktualizacji i informuje, czy aktualizacje są dostępne do zainstalowania na urządzeniu StorSimple.                                                                                          |
| [Instalacja-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ten skrypt instaluje dostępne aktualizacje na urządzeniu StorSimple.                                                                                                                                           |
| [Zarządzanie chmurami.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ten skrypt uruchamia ręczną migawkę chmury i usuwa migawki w chmurze starsze niż określone dni przechowywania.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Ten skrypt programu PowerShell programu Azure Automation Runbook raportuje stan wszystkich zadań tworzenia kopii zapasowej.                                                                                                              |
| [Usuń-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ten skrypt usuwa pojedynczy obiekt kopii zapasowej.                                                                                                                                                           |
| [Uruchamianie DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ten skrypt uruchamia ręczną kopię zapasową na urządzeniu StorSimple.                                                                                                                                       |
| [Aktualizacja-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ten skrypt aktualizuje klucz szyfrowania danych usługi dla wszystkich urządzeń 8010/8020 StorSimple Cloud Appliances zarejestrowanych w usłudze StorSimple Device Manager.                                     |
| [Weryfikuj-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ten skrypt wyróżnia brakujące kopie zapasowe po przeanalizowaniu wszystkich harmonogramów skojarzonych z zasadami tworzenia kopii zapasowych. Weryfikuje również katalog kopii zapasowych za pomocą listy dostępnych kopii zapasowych.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurowanie i uruchamianie przykładowego skryptu

W tej sekcji bierze skrypt przykład i szczegółowo różne kroki wymagane do uruchomienia skryptu.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Zainstalowano program Azure PowerShell. Aby zainstalować moduły programu Azure PowerShell:
    * W środowisku systemu Windows wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Program Azure PowerShell można zainstalować na hoście systemu Windows Server dla usługi StorSimple, jeśli go używasz.
    * W środowisku systemu Linux lub MacOS wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell w systemie MacOS lub Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Aby uzyskać więcej informacji na temat korzystania z programu Azure PowerShell, przejdź do [wprowadzenie do korzystania z programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Uruchamianie skryptu programu Azure PowerShell

Skrypt używany w tym przykładzie zawiera listę wszystkich zadań na urządzeniu StorSimple. Obejmuje to zadania, które powiodły się, nie powiodło się lub są w toku. Wykonaj następujące kroki, aby pobrać i uruchomić skrypt.

1. Uruchom program Azure PowerShell. Utwórz nowy folder i zmień katalog na nowy folder.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Pobierz nuget cli](https://www.nuget.org/downloads) w folderze utworzonym w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadającą sdk. Każdy link do pobrania wskazuje bezpośrednio na plik _.exe._ Pamiętaj, aby kliknąć prawym przyciskiem myszy i zapisać plik na komputerze, zamiast uruchamiać go w przeglądarce.

    Można również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Pobierz zależny sdk.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Pobierz skrypt z przykładowego projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Uruchom skrypt. Po wyświetleniu monitu o uwierzytelnienie podaj poświadczenia platformy Azure. Ten skrypt powinien wystawić filtrowane listy wszystkich zadań na urządzeniu StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Przykładowe dane wyjściowe

Następujące dane wyjściowe są prezentowane po uruchomieniu przykładowego skryptu. Dane wyjściowe zawierają wszystkie zadania uruchomione na zarejestrowanym urządzeniu, które rozpoczęło się 25 września 2017 r. i zostało ukończone do 2 października 2017 r.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Następne kroki

[Za pomocą usługi StorSimple Device Manager można zarządzać urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
