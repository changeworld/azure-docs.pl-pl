---
title: Temat usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agenta usługi mobilności do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 6b06ee7710dedbf2283fc4e365b767aa57547e7c
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417823"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Temat usługi mobilności na potrzeby maszyn wirtualnych VMware i serwerów fizycznych

Podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md), zainstaluj usługę Site Recovery Mobility na każdej lokalnej maszyny Wirtualnej VMware i serwerów fizycznych.  Usługa mobilności służy do przechwytywania zapisów danych na maszynie i przekazuje je do serwera przetwarzania Site Recovery. Można wdrożyć usługę mobilności, za pomocą następujących metod:

- [Instalacja wypychana](#push-installation): Usługa Site Recovery instaluje agenta mobilności na serwerze, po włączeniu ochrony za pomocą witryny Azure portal.
- Zainstaluj ręcznie: Można zainstalować usługi mobilności ręcznie na każdym komputerze za pośrednictwem [interfejsu użytkownika](#install-mobility-agent-through-ui) lub [polecenia](#install-mobility-agent-through-command-prompt).
- [Automatyczne wdrażanie](vmware-azure-mobility-install-configuration-mgr.md): Można zautomatyzować instalację przy użyciu narzędzi wdrażania oprogramowania takich jak System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Oprogramowanie antywirusowe na replikowanych maszyn

Jeśli maszyn, które mają być replikowane active oprogramowanie antywirusowe uruchomione, upewnij się, folder instalacji usługi mobilności można wykluczyć z ochrony antywirusowej operacji (*C:\ProgramData\ASR\agent*). Gwarantuje to, że replikacja działa zgodnie z oczekiwaniami.

## <a name="push-installation"></a>Instalacja wypychana

Instalacja wypychana jest integralną częścią "[włączania replikacji](vmware-azure-enable-replication.md#enable-replication)" zadanie wyzwalane w portalu. Po wybraniu zestawu maszyn wirtualnych, które chcesz chronić i uruchom "Włącz replikację", serwer konfiguracji wypycha agenta mobilności się do serwerów, instaluje agenta i zakończyć rejestrację agenta z serwerem konfiguracji. Do pomyślnego ukończenia tej operacji

- Upewnij się, że wszystkie instalacja wypychana [wymagania wstępne](vmware-azure-install-mobility-service.md) są spełnione.
- Upewnij się, że wszystkie konfiguracje serwerów są objęte [macierz obsługi oprogramowania VMware do scenariusza odzyskiwania po awarii z platformy Azure](vmware-physical-azure-support-matrix.md).

Szczegóły przepływu pracy instalacji wypychanej został opisany w poniższych sekcjach.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Z [9.23 wersji](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) lub nowszy

Podczas instalacji wypychanej agenta mobilności wykonywane są następujące czynności

1. Wypycha agenta na maszynie źródłowej. Kopiowanie agenta na maszynie źródłowej może zakończyć się niepowodzeniem z powodu wielu błędów środowiska. Odwiedź stronę [nasze wskazówki](vmware-azure-troubleshoot-push-install.md) rozwiązywania problemów z błędami instalacji wypychanej.
2. Po pomyślnie skopiowano agenta na sprawdzanie wymagań wstępnych serwera są wykonywane na serwerze. Instalacja kończy się niepowodzeniem, jeśli co najmniej jeden [wymagania wstępne](vmware-physical-azure-support-matrix.md) nie są spełnione. Jeśli wszystkie wymagania wstępne są spełnione, instalacja zostanie wywołany.
3. Dostawca usługi Azure Site Recovery VSS jest zainstalowany na serwerze jako część instalacji agenta mobilności. Ten dostawca jest używany do generowania punkty spójne aplikacji. W przypadku niepowodzenia instalacji dostawcy usługi VSS, ten krok zostanie pominięty, i będzie kontynuować instalację agenta.
4. Jeśli instalacja agenta zakończy się pomyślnie, ale instalacja dostawcy VSS nie powiedzie się, stan zadania jest oznaczona jako "Ostrzeżenie". Nie ma to wpływu na generowanie punktów spójności awarii.
    a. Aby wygenerować punkty spójne aplikacji, zapoznaj się [nasze wskazówki](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) do ukończenia instalacji dostawcy usługi VSS programu Site Recovery ręcznie.
    b.  Jeśli nie chcesz, aby punkty spójne aplikacji zostanie wygenerowany [zmodyfikować zasady replikacji](vmware-azure-set-up-replication.md#create-a-policy) wyłączyć punkty spójne aplikacji.

### <a name="before-922-versions"></a>Przed 9.22 wersji

1. Wypycha agenta na maszynie źródłowej. Kopiowanie agenta na maszynie źródłowej może zakończyć się niepowodzeniem z powodu wielu błędów środowiska. Odwiedź stronę [nasze wskazówki](vmware-azure-troubleshoot-push-install.md) rozwiązywania problemów z błędami instalacji wypychanej.
2. Po pomyślnie skopiowano agenta na sprawdzanie wymagań wstępnych serwera są wykonywane na serwerze. Instalacja kończy się niepowodzeniem, jeśli co najmniej jeden [wymagania wstępne](vmware-physical-azure-support-matrix.md) nie są spełnione. Jeśli wszystkie wymagania wstępne są spełnione, instalacja zostanie wywołany.
3. Dostawca usługi Azure Site Recovery VSS jest zainstalowany na serwerze jako część instalacji agenta mobilności. Ten dostawca jest używany do generowania punkty spójne aplikacji. Instalacja dostawcy VSS nie powiedzie się, następnie instalacja agenta zakończy się niepowodzeniem. Aby uniknąć niepowodzenia instalacji agenta mobilności, użyj [9.23 wersji](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) lub nowszej, aby wygenerować punktów spójnego na poziomie awarii i ręcznie zainstalować dostawcy usługi VSS.

## <a name="install-mobility-agent-through-ui"></a>Zainstaluj agenta mobilności przy użyciu interfejsu użytkownika

### <a name="prerequisite"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów są objęte [macierz obsługi oprogramowania VMware do scenariusza odzyskiwania po awarii z platformy Azure](vmware-physical-azure-support-matrix.md).
- [Znajdź Instalator](#locate-installer-files) oparty na systemie operacyjnym serwera.

>[!IMPORTANT]
> W przypadku replikacji maszyn wirtualnych IaaS platformy Azure między regionami platformy Azure do innego, nie używaj tej metody. Zamiast tego należy użyć metody instalacji za pośrednictwem wiersza polecenia.

1. Skopiuj plik instalacyjny na maszynie i uruchomimy ją.
2. W **opcji instalacji**, wybierz opcję **zainstalować usługi mobilności**.
3. Wybierz lokalizację instalacji > **zainstalować**.

    ![Strona opcji instalacji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitorowanie instalacji **postęp instalacji**. Po zakończeniu instalacji wybierz **przejść do konfiguracji** można zarejestrować usługi z serwerem konfiguracji.

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. w **szczegóły serwera konfiguracji**, podaj adres IP i hasło skonfigurowane.  

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Wybierz **zarejestrować** do zakończenia rejestracji.

    ![Strony końcowe rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Zainstaluj agenta mobilności za pomocą wiersza polecenia

### <a name="prerequisite"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów są objęte [macierz obsługi oprogramowania VMware do scenariusza odzyskiwania po awarii z platformy Azure](vmware-physical-azure-support-matrix.md).
- [Znajdź Instalator](#locate-installer-files) oparty na systemie operacyjnym serwera.

### <a name="on-a-windows-machine"></a>Na komputerze Windows

- Skopiuj Instalator do folderu lokalnego (np. C:\Temp) na serwerze, który chcesz chronić.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Należy zainstalować w następujący sposób:

    ``` 
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Zarejestruj agenta z serwerem konfiguracji.

    ``` 
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Ustawienia instalacji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | UnifiedAgent.exe/role < MS|MT > /InstallLocation  <Install Location> /silent "VmWare" opcji/platform
Dzienniki Instalatora | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parametr obowiązkowo instalowany. Określa, czy można zainstalować usługi mobilności (MS) lub główny serwer docelowy (MT).
/InstallLocation| Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności (dowolnego folderu).
/ Platform | Obowiązkowy. Określa platformę, na którym zainstalowano usługę mobilności. **VMware** dla serwerów fizycznych i maszyn wirtualnych VMware; **Azure** dla maszyn wirtualnych platformy Azure. 
/Silent| Opcjonalny. Określa, czy należy uruchomić Instalatora w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Dzienniki konfiguracji agenta | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
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
Sposób użycia | . / install -d <Install Location> - r < MS|MT> -v VmWare -q
-r | Parametr obowiązkowo instalowany. Określa, czy można zainstalować usługi mobilności (MS) lub główny serwer docelowy (MT).
-d | Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności: /usr/local/ASR.
-v | Obowiązkowy. Określa platformę, na którym zainstalowano usługę mobilności. **VMware** dla serwerów fizycznych i maszyn wirtualnych VMware; **Azure** dla maszyn wirtualnych platformy Azure. 
-q | Opcjonalny. Określa, czy należy uruchomić Instalatora w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolny prawidłowy adres IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisywane są hasła. Użyj dowolnej prawidłowy folder.

## <a name="azure-virtual-machine-agent"></a>Agent maszyny wirtualnej platformy Azure

- **Maszyny wirtualne z systemem Windows**: Z 9.7.0.0 wersję usługi mobilności [agenta maszyny Wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) jest instalowany przez Instalatora usługi mobilności. Gwarantuje to, czy po maszyny przejściu w tryb failover na platformie Azure, maszyny Wirtualnej platformy Azure spełnia wymagania wstępne dla przy użyciu każde rozszerzenie maszyny wirtualnej instalacji agenta.
- **Maszyny wirtualne z systemem Linux**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) musi być zainstalowany ręcznie na maszynie Wirtualnej platformy Azure po włączeniu trybu failover.

## <a name="locate-installer-files"></a>Znajdź pliki Instalatora

Przejdź do folderu %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na serwerze konfiguracji. Sprawdź, które należy Instalator oparty na systemie operacyjnym. Poniższa tabela zawiera podsumowanie pliki Instalatora dla każdej maszyny Wirtualnej VMware i serwera fizycznego systemu operacyjnego. Możesz przejrzeć [obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines) przed rozpoczęciem.

**Plik Instalatora** | **System operacyjny (tylko 64-bitowy)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serwer Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie instalacji wypychanej usługi mobilności](vmware-azure-install-mobility-service.md).
