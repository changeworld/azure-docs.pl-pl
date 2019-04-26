---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301160"
---
1. Skopiuj Instalator do folderu lokalnego (np. C:\Temp) na serwerze, który chcesz chronić. Uruchom następujące polecenia jako administrator, w wierszu polecenia:

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. Aby zainstalować usługę mobilności, uruchom następujące polecenie:

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Teraz agent musi być zarejestrowany na serwerze konfiguracji.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumenty wiersza polecenia Instalatora usługi mobilności

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parametr|Type|Opis|Możliwe wartości|
|-|-|-|-|
|/Role|Obowiązkowy|Określa, czy należy zainstalować usługi mobilności (MS), czy główny serwer docelowy ma Oznaczała powinien być zainstalowany.|MS </br> MT|
|/InstallLocation|Optional (Opcjonalność)|Lokalizacja, w którym jest zainstalowana usługa mobilności.|Dowolny folder na komputerze|
|/ Platform|Obowiązkowy|Określa platformę, na którym zainstalowano usługę mobilności. </br> </br>- **VMware**: Użyj tej wartości, po zainstalowaniu usługi mobilności na maszynie Wirtualnej z systemem *hostami programu VMware vSphere ESXi*, *hosty funkcji Hyper-V*, i *serwerów fizycznych*. </br> - **Azure**: Użyj tej wartości, po zainstalowaniu agenta na maszynie Wirtualnej IaaS platformy Azure. | VMware </br> Azure|
|/Silent|Optional (Opcjonalność)|Określa, aby uruchomić Instalatora w trybie dyskretnym.| ND|

>[!TIP]
> Dzienniki instalacji znajdują się w ścieżce % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Argumenty wiersza polecenia rejestracji usługi mobilności

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parametr|Type|Opis|Możliwe wartości|
  |-|-|-|-|
  |/CSEndPoint |Obowiązkowy|Adres IP serwera konfiguracji| Dowolny prawidłowy adres IP|
  |/PassphraseFilePath|Obowiązkowy|Lokalizacja hasło |Wszelkie Nieprawidłowa ścieżka UNC lub ścieżka do pliku lokalnego|


>[!TIP]
> Dzienniki konfiguracji agenta można znaleźć w obszarze % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
