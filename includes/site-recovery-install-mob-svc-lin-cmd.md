---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165353"
---
1. Skopiuj Instalator do folderu lokalnego (na przykład TMP) na serwerze, który chcesz chronić. W terminalu uruchom następujące polecenia:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Aby zainstalować usługę mobilności, uruchom następujące polecenie:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Po zakończeniu instalacji usługi mobilności musi być zarejestrowany z serwerem konfiguracji. Uruchom następujące polecenie, aby zarejestrować usługę mobilności z serwerem konfiguracji:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Wiersz polecenia Instalatora usługi mobilności

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parametr|Typ|Opis|Możliwe wartości|
|-|-|-|-|
|-r |Obowiązkowy|Określa, czy należy zainstalować usługi mobilności (MS), czy główny serwer docelowy ma Oznaczała powinien być zainstalowany.|MS </br> MT|
|-d |Optional (Opcjonalność)|Lokalizacja, w którym jest zainstalowana usługa mobilności.|/usr/local/ASR|
|-v|Obowiązkowy|Określa platformę, na którym zainstalowano usługę mobilności. </br> </br>- **VMware**: Użyj tej wartości, po zainstalowaniu usługi mobilności na maszynie Wirtualnej z systemem *hostami programu VMware vSphere ESXi*, *hosty funkcji Hyper-V*, i *serwerów fizycznych*. </br> - **Azure**: Użyj tej wartości, po zainstalowaniu agenta na maszynie Wirtualnej IaaS platformy Azure.| VMware </br> Azure|
|-q|Optional (Opcjonalność)|Określa, aby uruchomić Instalatora w trybie dyskretnym.| ND|


#### <a name="mobility-service-configuration-command-line"></a>Wiersz polecenia konfiguracji usługi mobilności

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parametr|Typ|Opis|Możliwe wartości|
|-|-|-|-|
|-i |Obowiązkowy|Adres IP serwera konfiguracji|Dowolny prawidłowy adres IP|
|-P |Obowiązkowy|Pełna ścieżka pliku dla pliku, gdzie hasło połączenia zostanie zapisany.|Dowolny prawidłowy folder|
