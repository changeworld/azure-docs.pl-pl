---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183691"
---
1. [Połącz się z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj, `Get-HcsApplianceInfo` aby uzyskać informacje o urządzeniu.

    W poniższym przykładzie przedstawiono użycie tego polecenia cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Oto tabela podsumowująca niektóre ważne informacje o urządzeniu:
    
    | Parametr                             | Opis                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Przyjazna nazwa urządzenia skonfigurowana za pośrednictwem lokalnego interfejsu użytkownika sieci web podczas wdrażania urządzenia. Domyślną przyjazną nazwą jest numer seryjny urządzenia.  |   |
    | SerialNumber                   | Numer seryjny urządzenia to unikatowy numer przypisany fabrycznie.                                                                             |   |
    | Model                          | Model urządzenia Data Box Edge lub Data Box Gateway. Model jest wirtualny dla bramy pola danych i fizyczny dla krawędzi pola danych.                   |   |
    | Wersja friendlysoftware        | Przyjazny ciąg odpowiadający wersji oprogramowania urządzenia. W przypadku systemu działającego w wersji zapoznawczej przyjazną wersją oprogramowania będzie Data Box Edge 1902. |   |
    | Wersja HcsVersion                     | Wersja oprogramowania HCS działająca na urządzeniu. Na przykład wersja oprogramowania HCS odpowiadająca Data Box Edge 1902 to 1.4.771.324.            |   |
    | LokalnapędnośćInMb              | Całkowita pojemność lokalna urządzenia w Megabitach.                                                                                                        |   |
    | Czyzarejestrowane                   | Ta wartość wskazuje, czy urządzenie jest aktywowane w usłudze.                                                                                         |   |


