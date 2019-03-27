---
title: Zarządzanie urządzeniami StorSimple za pomocą skryptów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Resource Manager skryptów do automatyzacji zadań usługi StorSimple
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
ms.openlocfilehash: 646b862733e8727c9c8729f1ac038fa88cfa0580
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443180"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Zarządzanie urządzeniami StorSimple za pomocą skryptów na podstawie zestawu SDK usługi Azure Resource Manager

W tym artykule opisano sposób zestawu SDK usługi Azure Resource Manager skryptów może służyć do zarządzania urządzeniem serii StorSimple 8000. Przykładowy skrypt znajduje się również przeprowadzi Cię przez kroki konfigurowania środowiska w taki sposób, aby uruchamiać tych skryptów.

Ten artykuł ma zastosowanie w portalu Azure, tylko urządzeń z serii StorSimple 8000.

## <a name="sample-scripts"></a>Przykładowe skrypty

Następujące przykładowe skrypty są dostępne w celu automatyzacji różne zadania usługi StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabelę na podstawie zestawu SDK usługi Azure Resource Manager przykładowe skrypty

| Skrypt programu Azure Resource Manager                    | Opis                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ten skrypt można zezwolić urządzeniu StorSimple można zmienić klucza szyfrowania danych usługi.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ten skrypt tworzy 8010 lub 8020 urządzenia StorSimple w chmurze. Urządzenie w chmurze, następnie można konfigurować i rejestrowania przy użyciu usługi StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ten skrypt tworzy lub modyfikuje woluminów StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ten skrypt zawiera listę wszystkich kopii zapasowych dla urządzeń zarejestrowanych w usłudze Menedżer urządzeń StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ten skrypt wszystkich zasad dotyczących kopii zapasowych dla urządzenia StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ten skrypt pobiera wszystkie zadania StorSimple uruchomione w usłudze Menedżer urządzeń StorSimple.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ten skrypt skanuje serwer aktualizacji i informuje o tym, jeśli aktualizacje są dostępne do zainstalowania na urządzeniu StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ten skrypt instaluje dostępnych aktualizacji na urządzeniu StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ten skrypt uruchamia migawkę w chmurze ręcznych i usuwa starsze niż liczba dni przechowywania określonej migawki w chmurze.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Ten skrypt programu Runbook PowerShell automatyzacji Azure informuje o stanie wszystkich zadań tworzenia kopii zapasowej.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ten skrypt usuwa pojedynczy obiekt kopii zapasowej.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ten skrypt uruchamia ręcznego tworzenia kopii zapasowej na urządzeniu StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ten skrypt aktualizuje klucz szyfrowania danych usługi dla wszystkich urządzeń StorSimple w chmurze 8010/8020 zarejestrowana przy użyciu usługi Menedżer urządzeń StorSimple.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ten skrypt powoduje wyróżnienie Brak kopii zapasowych po przeanalizowaniu wszystkie harmonogramy, które są skojarzone z zasadami tworzenia kopii zapasowych. Sprawdza także wykaz kopii zapasowych z listą dostępnych kopii zapasowych.             |




## <a name="configure-and-run-a-sample-script"></a>Skonfiguruj i uruchom skrypt przykładowy

W tej sekcji pobiera przykładowy skrypt i szczegóły różnych kroków wymaganych do uruchomienia skryptu.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Zainstalowany program Azure PowerShell. Aby zainstalować moduły programu Azure PowerShell:
    * W środowisku Windows, wykonaj kroki opisane w [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Instalowanie programu Azure PowerShell na hoście z systemem Windows Server dla usługi StorSimple przy użyciu jednego.
    * W środowisku systemu Linux lub MacOS, wykonaj kroki opisane w [Instalowanie i konfigurowanie programu Azure PowerShell w systemie MacOS lub Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Aby uzyskać więcej informacji o używaniu programu Azure PowerShell, przejdź do [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Uruchom skrypt programu Azure PowerShell

Skrypt używany w tym przykładzie wyświetlane są wszystkie zadania na urządzeniu StorSimple. Dotyczy to również zadania, które zakończyło się pomyślnie, nie powiodło się lub są w toku. Wykonaj poniższe kroki, aby pobrać i uruchomić skrypt.

1. Uruchom program Azure PowerShell. Utwórz nowy folder i zmień katalog do nowego folderu.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Pobierz interfejs wiersza polecenia NuGet](https://www.nuget.org/downloads) w folderze, który został utworzony w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadającą zestawu SDK. Każdy link pobierania punktów bezpośrednio do _.exe_ pliku. Należy koniecznie kliknij prawym przyciskiem myszy, a następnie zapisz plik na komputer, zamiast uruchamiać go za pomocą przeglądarki.

    Można również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Pobierz zależnego zestawu SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Pobierz skrypt z przykładowego projektu usługi GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Uruchom skrypt. Po wyświetleniu, do uwierzytelniania, podaj swoje poświadczenia platformy Azure. Ten skrypt ma produkt wyjściowy filtrowana lista wszystkich zadań na urządzeniu StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Przykładowe dane wyjściowe

Następujące dane wyjściowe są prezentowane w przypadku przykładowy skrypt jest uruchamiany. Dane wyjściowe zawierają wszystkich zadań uruchomionych na urządzeniu zarejestrowanych rozpoczęte 25 września 2017 r i zakończone przez 2 października 2017 r.

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


## <a name="next-steps"></a>Kolejne kroki

[Usługa Menedżer urządzeń StorSimple użycia do zarządzania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).