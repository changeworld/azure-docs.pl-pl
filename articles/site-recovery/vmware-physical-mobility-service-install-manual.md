---
title: Instalowanie usługi mobilności ręcznie do odzyskiwania po awarii serwerów fizycznych i maszyn wirtualnych VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować agenta usługi mobilności do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: dfed0209131379843b97ff8050c2f2ba7294537d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019317"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Ręczne instalowanie usługi mobilności na maszynach wirtualnych VMware oraz serwerach fizycznych

Podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md), należy zainstalować [usługi Site Recovery Mobility](vmware-physical-mobility-service-overview.md) na każdej maszyny Wirtualnej VMware w środowisku lokalnym i serwera fizycznego.  Usługa mobilności służy do przechwytywania zapisów danych na maszynie i przekazuje je do serwera przetwarzania Site Recovery.

W tym artykule opisano sposób ręcznie zainstalować usługę mobilności na każdym komputerze, który chcesz chronić.

## <a name="create-a-passphrase"></a>Utwórz hasło

Przed instalacją należy utworzyć hasło, które będą używane podczas instalacji.

1. Zaloguj się do serwera konfiguracji.
2. Otwórz wiersz polecenia jako administrator.
3. Zmień katalog na bin folder, a następnie utwórz pliku hasła.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store pliku hasła w bezpiecznym miejscu. 


## <a name="install-the-service-from-the-ui"></a>Instalowanie usługi z poziomu interfejsu użytkownika

>[!IMPORTANT]
> Jeśli replikujesz maszyny Wirtualnej IaaS platformy Azure między regionami platformy Azure do innego, nie używaj tej metody. Użyj insteadl metody instalacji za pośrednictwem wiersza polecenia.

1. Znajdź wersję Instalatora, czego potrzebujesz do systemu operacyjnego maszyny. Instalatory znajdują się w folderze %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Sprawdź](vmware-physical-mobility-service-overview.md#installer-files) Instalatora, których potrzebujesz.
2. Skopiuj plik instalacyjny na maszynie i uruchomimy ją.
3. W **opcji instalacji**, wybierz opcję **zainstalować usługi mobilności**.
4. Wybierz lokalizację instalacji > **zainstalować**.

    ![Strona opcji instalacji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Monitorowanie instalacji **postęp instalacji**. Po zakończeniu instalacji wybierz **przejść do konfiguracji** można zarejestrować usługi z serwerem konfiguracji.

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  w **szczegóły serwera konfiguracji**, podaj adres IP i hasło skonfigurowane.  

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Wybierz **zarejestrować** do zakończenia rejestracji.

    ![Strony końcowe rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Instalowanie usługi z poziomu wiersza polecenia

### <a name="on-a-windows-machine"></a>Na komputerze Windows

1. Skopiuj Instalator do folderu lokalnego (np. C:\Temp) na serwerze, który chcesz chronić. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Należy zainstalować w następujący sposób:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Zarejestruj agenta z serwerem konfiguracji.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Ustawienia instalacji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | UnifiedAgent.exe/role < MS|MT > /InstallLocation  <Install Location> /silent "VmWare" opcji/platform
Dzienniki Instalatora | W obszarze % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/ Roli | Parametr obowiązkowo instalowany. Określa, czy można zainstalować usługi mobilności (MS) lub główny serwer docelowy (MT).
/InstallLocation| Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności (dowolnego folderu).
/ Platform | Obowiązkowy. Określa platformę, na którym zainstalowano usługę mobilności. **VMware** dla serwerów fizycznych i maszyn wirtualnych Mware; **Azure** dla maszyn wirtualnych platformy Azure. 
/ Silent| Opcjonalny. Określa, czy należy uruchomić Instalatora w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | / Csendpoint UnifiedAgentConfigurator.exe  <CSIP> /passphrasefilepath <PassphraseFilePath>
Dzienniki konfiguracji agenta | W obszarze % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasło. Użyj dowolnego Nieprawidłowa ścieżka UNC lub ścieżka do pliku lokalnego.


### <a name="on-a-linux-machine"></a>Na maszynie z systemem Linux

1. Skopiuj Instalator do folderu lokalnego (na przykład TMP) na serwerze, który chcesz chronić. W terminalu uruchom następujące polecenia:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Należy zainstalować w następujący sposób:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Po zakończeniu instalacji usługi mobilności musi być zarejestrowany z serwerem konfiguracji. Uruchom następujące polecenie, aby zarejestrować usługę mobilności z serwerem konfiguracji:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>Ustawienia instalacji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | . / install -d <Install Location> - r < MS|MT > - v, VmWare - q
-r | Parametr obowiązkowo instalowany. Określa, czy można zainstalować usługi mobilności (MS) lub główny serwer docelowy (MT).
-d | Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności: /usr/local/ASR.
-v | Obowiązkowy. Określa platformę, na którym zainstalowano usługę mobilności. **VMware** dla serwerów fizycznych i maszyn wirtualnych Mware; **Azure** dla maszyn wirtualnych platformy Azure. 
-q | Opcjonalny. Określa, czy należy uruchomić Instalatora w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | /usr/local/ASR/Vx/bin dysku CD<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisywane są hasła. Użyj dowolnego prawidłowego folderu

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware](vmware-azure-tutorial.md)
- [Konfigurowanie odzyskiwania po awarii dla serwerów fizycznych](physical-azure-disaster-recovery.md)
