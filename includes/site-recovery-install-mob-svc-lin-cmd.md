---
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301447"
---
1. Skopiuj Instalator do folderu lokalnego (na przykład TMP) na serwerze, który chcesz chronić. W terminalu uruchom następujące polecenia:
     ```
     cd /tmp;
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

|Parametr|Type|Opis|Możliwe wartości|
|-|-|-|-|
|-r |Obowiązkowy|Określa, czy należy zainstalować usługi mobilności (MS), czy główny serwer docelowy ma Oznaczała powinien być zainstalowany.|MS <br /> MT|
|-d |Optional (Opcjonalność)|Lokalizacja, w którym jest zainstalowana usługa mobilności.|/usr/local/ASR|
|-v|Obowiązkowy|Określa platformę, na którym zainstalowano usługę mobilności. <br /> <br />- **VMware**: Użyj tej wartości, po zainstalowaniu usługi mobilności na maszynie Wirtualnej z systemem *hostami programu VMware vSphere ESXi*, *hosty funkcji Hyper-V*, i *serwerów fizycznych*. <br /> - **Azure**: Użyj tej wartości, po zainstalowaniu agenta na maszynie Wirtualnej IaaS platformy Azure.| VMware <br /> Azure|
|-q|Optional (Opcjonalność)|Określa, aby uruchomić Instalatora w trybie dyskretnym.| ND|

#### <a name="mobility-service-configuration-command-line"></a>Wiersz polecenia konfiguracji usługi mobilności

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parametr|Type|Opis|Możliwe wartości|
|-|-|-|-|
|-i |Obowiązkowy|Adres IP serwera konfiguracji|Dowolny prawidłowy adres IP|
|-P |Obowiązkowy|Pełna ścieżka pliku dla pliku, gdzie hasło połączenia zostanie zapisany.|Dowolny prawidłowy folder|
<!--Update_Description: wording update-->
<!--ms.date: 03/05/2018-->