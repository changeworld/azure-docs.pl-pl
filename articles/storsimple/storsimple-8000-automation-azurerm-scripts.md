---
title: Za pomocą skryptów usługi Azure Resource Manager do zarządzania urządzeniami StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Resource Manager skryptów do automatyzacji zadań StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: f4456200d6f497a87424f12a23034dbff00c75aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26373945"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Za pomocą skryptów na podstawie zestawu SDK usługi Azure Resource Manager do zarządzania urządzeniami StorSimple

W tym artykule opisano, jak zestaw SDK usługi Azure Resource Manager skryptów może służyć do zarządzania urządzeniem serii StorSimple 8000. Przykładowy skrypt znajduje się również do prowadzą użytkownika przez kroki konfigurowania środowiska, aby uruchamiać te skrypty.

Ten artykuł dotyczy uruchomionych w portalu Azure tylko urządzeń z serii StorSimple 8000.

## <a name="sample-scripts"></a>Przykładowe skrypty

Następujące przykładowe skrypty są dostępne w celu automatyzacji różne zadania StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabeli na podstawie zestawu SDK usługi Azure Resource Manager przykładowe skrypty

| Skrypt Menedżera zasobów Azure                    | Opis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [ServiceEncryptionRollover.ps1 autoryzacji](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ten skrypt można zezwolić urządzeniu StorSimple można zmienić klucza szyfrowania danych usługi.                                                                                                           |
| [Utwórz StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ten skrypt tworzy 8010 lub 8020 urządzenia chmury StorSimple. Następnie można konfigurować i zarejestrowany w usłudze Menedżer StorSimple danych urządzenia chmury.                                                       |
| [CreateOrUpdate Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ten skrypt tworzy lub modyfikuje woluminów StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ten skrypt zawiera listę wszystkich kopii zapasowych dla urządzeń zarejestrowanych przy użyciu usługi Menedżer StorSimple urządzenia.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ten skrypt zasad tworzenia kopii zapasowych dla urządzenia StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ten skrypt pobiera wszystkich zadań StorSimple uruchomionych w usłudze Menedżer StorSimple urządzenia.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ten skrypt skanuje serwer aktualizacji i informuje o tym, jeśli aktualizacje są dostępne do zainstalowania na urządzeniu StorSimple.                                                                                          |
| [DeviceUpdate.ps1 instalacji](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ten skrypt instaluje dostępne aktualizacje na urządzeniu StorSimple.                                                                                                                                           |
| [Zarządzanie CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ten skrypt uruchamia migawkę chmury ręczne i usuwa starsze niż określona przechowywania w dniach migawki w chmurze.                                                                                                   |
| [Monitor Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Ten skrypt programu PowerShell dla elementu Runbook automatyzacji Azure informuje o stanie wszystkich zadań tworzenia kopii zapasowej.                                                                                                              |
| [Usuń DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ten skrypt usuwa pojedynczy obiekt kopii zapasowej.                                                                                                                                                           |
| [Start DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ten skrypt uruchamiania ręcznego tworzenia kopii zapasowej na urządzeniu StorSimple.                                                                                                                                       |
| [CloudApplianceServiceEncryptionKey.ps1 aktualizacji](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ten skrypt aktualizacji klucza szyfrowania danych usługi dla wszystkich zarejestrowanych urządzenia 8010/8020 StorSimple chmury przy użyciu usługi Menedżer StorSimple urządzenia.                                     |
| [Sprawdź BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ten skrypt prezentuje Brak kopii zapasowych po przeanalizowaniu wszystkie harmonogramy skojarzonych z zasadami tworzenia kopii zapasowej. Sprawdza także katalogu kopii zapasowej z listy dostępnych kopii zapasowych.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurowanie i uruchamianie przykładowego skryptu

W tej sekcji przyjmuje przykładowy skrypt i zawiera szczegóły różnych kroków wymaganych do uruchomienia skryptu.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Zainstalowany program Azure PowerShell. Aby zainstalować moduły programu Azure PowerShell:
    * W środowisku systemu Windows, postępuj zgodnie z instrukcjami [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0). Instalowanie programu Azure PowerShell na hoście z systemem Windows Server dla Twojego urządzenia StorSimple przy użyciu jednego.
    * W środowisku systemu Linux lub MacOS postępuj zgodnie z instrukcjami [Instalowanie i konfigurowanie programu Azure PowerShell MacOS lub Linux](https://docs.microsoft.com/powershell/azure/install-azurermps-maclinux?view=azurermps-4.4.0).

Aby uzyskać więcej informacji o korzystaniu z programu Azure PowerShell, przejdź do [rozpocząć używanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Uruchom skrypt programu PowerShell systemu Azure

W tym przykładzie skrypt zawiera listę wszystkich zadań na urządzeniu StorSimple. Dotyczy to nieukończonych zadań, które zakończyło się pomyślnie, nie powiodło się lub są w toku. Wykonaj poniższe kroki, aby pobrać i uruchom skrypt.

1. Uruchom program Azure PowerShell. Utwórz nowy folder i zmień katalog do nowego folderu.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Pobierz interfejs wiersza polecenia NuGet](http://www.nuget.org/downloads) w folderze, który został utworzony w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadający zestawu SDK. Każde łącze pobierania punktów bezpośrednio do _.exe_ pliku. Kliknij prawym przyciskiem myszy i Zapisz plik na komputerze, zamiast uruchomienie jej w przeglądarce.

    Można również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Pobierz zestaw SDK zależnych.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Skrypt można pobrać przykładowy projekt GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Uruchom skrypt. Gdy monit o uwierzytelnienie, wprowadź swoje poświadczenia usługi Azure. Ten skrypt powinien output wyfiltrowanej listy wszystkich zadań na urządzeniu StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Przykładowe dane wyjściowe

Następujące dane wyjściowe są prezentowane, gdy przykładowy skrypt jest uruchamiany. Dane wyjściowe zawierają wszystkie zadania uruchomione na zarejestrowane urządzenie uruchomiona na 25 września 2017 i wykonane przez 2 października 2017 r.

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

[Usługi StorSimple za pomocą Menedżera urządzeń do zarządzania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).