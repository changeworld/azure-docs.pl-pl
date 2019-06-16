---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161184"
---
1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj `Get-HcsApplianceInfo` Aby uzyskać informacje o urządzeniu.

    Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet:

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

    Poniżej przedstawiono tabelę zawierającą podsumowanie niektórych informacji ważnych urządzenia:
    
    | Parametr                             | Opis                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Przyjazna nazwa urządzenia, zgodnie z konfiguracją za pomocą lokalnego internetowego interfejsu użytkownika podczas wdrażania urządzenia. Przyjazna nazwa domyślna jest numer seryjny urządzenia.  |   |
    | numer seryjny                   | Numer seryjny urządzenia jest unikatowy numer przypisany na etapie produkcji.                                                                             |   |
    | Modelowanie                          | Model urządzenia krawędzi pola danych lub bramy pola danych. Model jest wirtualne dla bramy pola danych i fizyczne do krawędzi ramki danych.                   |   |
    | FriendlySoftwareVersion        | Przyjazne ciąg, który odnosi się do wersji oprogramowania urządzenia. Na komputerze z systemem w wersji zapoznawczej wersja oprogramowania przyjazna byłoby 1902 krawędzi pola danych. |   |
    | HcsVersion                     | Wersja oprogramowania magazynu HCS uruchomiony na twoim urządzeniu. Na przykład wersja oprogramowania magazynu HCS odpowiadający 1902 krawędzi pola danych jest 1.4.771.324.            |   |
    | LocalCapacityInMb              | Całkowita pojemność lokalna urządzenia w megabitach.                                                                                                        |   |
    | IsRegistered                   | Ta wartość wskazuje, czy urządzenia został aktywowany w usłudze.                                                                                         |   |


