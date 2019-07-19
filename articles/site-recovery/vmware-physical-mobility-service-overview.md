---
title: Informacje o usłudze mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych przy użyciu Azure Site Recovery | Microsoft Docs
description: Dowiedz się więcej o agencie usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c692b1c5b77b95e5487a847b46473906135c3d86
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261148"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informacje o usłudze mobilności dla maszyn wirtualnych VMware i serwerów fizycznych

Po skonfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych przy użyciu [Azure Site Recovery](site-recovery-overview.md)należy zainstalować usługę mobilności Site Recovery na wszystkich lokalnych maszynach wirtualnych VMware i na serwerze fizycznym.  Usługa mobilności przechwytuje operacje zapisu danych na komputerze i przekazuje je do serwera przetwarzania Site Recovery. Usługę mobilności można wdrożyć przy użyciu następujących metod:

- [Instalacja wypychana](#push-installation): Site Recovery instaluje agenta mobilności na serwerze, gdy ochrona jest włączona za pośrednictwem Azure Portal.
- Zainstaluj ręcznie: Usługę mobilności można zainstalować ręcznie na każdym komputerze za pomocą [interfejsu użytkownika](#install-mobility-agent-through-ui) lub [wiersza polecenia](#install-mobility-agent-through-command-prompt).
- [Automatyczne wdrażanie](vmware-azure-mobility-install-configuration-mgr.md): Instalację można zautomatyzować za pomocą narzędzi do wdrażania oprogramowania, takich jak System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Oprogramowanie antywirusowe na replikowanych maszynach

Jeśli na maszynach, które mają być replikowane, działa aktywne oprogramowanie chroniące przed wirusami, upewnij się, że folder instalacji usługi mobilności został wykluczony z operacji programu antywirusowego (*C:\ProgramData\ASR\agent*). Dzięki temu replikacja działa zgodnie z oczekiwaniami.

## <a name="push-installation"></a>Instalacja wypychana

Instalacja wypychana jest integralną częścią zadania "[Włącz replikację](vmware-azure-enable-replication.md#enable-replication)" wyzwalaną w portalu. Po wybraniu zestawu maszyn wirtualnych, które mają być chronione, i wyzwalaczem "Włącz replikację" serwer konfiguracji Wypychaj agenta mobilności na serwerach, zainstaluje agenta i ukończy rejestrację agenta z serwerem konfiguracji. W celu pomyślnego ukończenia tej operacji,

- Upewnij się, że spełniono wszystkie [wymagania wstępne](vmware-azure-install-mobility-service.md) instalacji wypychanej.
- Upewnij się, że wszystkie konfiguracje serwerów znajdują się w obszarze [macierzy obsługi programu VMware na platformie Azure](vmware-physical-azure-support-matrix.md).

Szczegóły przepływu pracy instalacji wypychanej zostały opisane w poniższych sekcjach.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Od [9,23 wersji](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) do wewnątrz

Podczas instalacji wypychanej agenta mobilności wykonywane są następujące czynności:

1. Wypchnięcie agenta do maszyny źródłowej. Kopiowanie agenta na maszynę źródłową może zakończyć się niepowodzeniem z powodu wielu błędów środowiska. Odwiedź [nasze wskazówki](vmware-azure-troubleshoot-push-install.md) , aby rozwiązać problemy z błędami instalacji wypychanej.
2. Po pomyślnym skopiowaniu agenta do sprawdzenia wymagań wstępnych serwera są wykonywane na serwerze. Instalacja nie powiedzie się, jeśli co najmniej jedno z [wymagań wstępnych](vmware-physical-azure-support-matrix.md) nie zostanie spełnione. Jeśli spełniono wszystkie wymagania wstępne, instalacja zostanie wyzwolona.
3. Azure Site Recovery Dostawca usługi VSS jest instalowany na serwerze w ramach instalacji agenta mobilności. Ten dostawca służy do generowania punktów spójnych z aplikacjami. Jeśli instalacja dostawcy VSS nie powiedzie się, ten krok zostanie pominięty, a Instalacja agenta będzie kontynuowana.
4. Jeśli instalacja agenta zakończy się pomyślnie, ale instalacja dostawcy VSS nie powiedzie się, stan zadania zostanie oznaczony jako "ostrzeżenie". Nie ma to wpływu na generowanie punktów spójności awarii.

    a. Aby wygenerować punkty spójne z aplikacjami, zapoznaj się z [naszymi wskazówkami dotyczącymi](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) ręcznego instalowania dostawcy usługi VSS Site Recovery. </br>
    b.  Aby nie generować punktów spójnych w aplikacji, należy [zmodyfikować zasady replikacji](vmware-azure-set-up-replication.md#create-a-policy) w celu wyłączenia punktów spójności aplikacji.

### <a name="before-922-versions"></a>Przed 9,22 wersji

1. Wypchnięcie agenta do maszyny źródłowej. Kopiowanie agenta na maszynę źródłową może zakończyć się niepowodzeniem z powodu wielu błędów środowiska. Odwiedź [nasze wskazówki](vmware-azure-troubleshoot-push-install.md) , aby rozwiązać problemy z błędami instalacji wypychanej.
2. Po pomyślnym skopiowaniu agenta do sprawdzenia wymagań wstępnych serwera są wykonywane na serwerze. Instalacja nie powiedzie się, jeśli co najmniej jedno z [wymagań wstępnych](vmware-physical-azure-support-matrix.md) nie zostanie spełnione. Jeśli spełniono wszystkie wymagania wstępne, instalacja zostanie wyzwolona.
3. Azure Site Recovery Dostawca usługi VSS jest instalowany na serwerze w ramach instalacji agenta mobilności. Ten dostawca służy do generowania punktów spójnych z aplikacjami. Jeśli instalacja dostawcy VSS nie powiedzie się, Instalacja agenta zakończy się niepowodzeniem. Aby uniknąć niepowodzenia instalacji agenta mobilności, należy użyć [wersji 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) lub nowszej w celu wygenerowania spójnych punktów awarii i ręcznej instalacji dostawcy usługi VSS.

## <a name="install-mobility-agent-through-ui"></a>Instalowanie agenta mobilności przy użyciu interfejsu użytkownika

### <a name="prerequisite"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów znajdują się w obszarze [macierzy obsługi programu VMware na platformie Azure](vmware-physical-azure-support-matrix.md).
- [Zlokalizuj Instalatora](#locate-installer-files) na podstawie systemu operacyjnego serwera.

>[!IMPORTANT]
> W przypadku replikacji maszyny wirtualnej Azure IaaS z jednego regionu platformy Azure do innego nie należy używać tej metody. Zamiast tego użyj metody instalacji opartej na wierszu polecenia.

1. Skopiuj plik instalacyjny na komputer i uruchom go.
2. W obszarze **Instalacja**wybierz opcję **Zainstaluj usługę mobilności**.
3. Wybierz lokalizację instalacji > **Zainstaluj**.

    ![Strona opcji instalacji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitoruj instalację w **trakcie instalacji**. Po zakończeniu instalacji wybierz pozycję **Kontynuuj do konfiguracji** , aby zarejestrować usługę na serwerze konfiguracji.

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. W obszarze **Szczegóły serwera konfiguracji**Określ adres IP i hasło, które zostały skonfigurowane.  

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Wybierz pozycję **zarejestruj** , aby zakończyć rejestrację.

    ![Ostatnia strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalowanie agenta mobilności przy użyciu wiersza polecenia

### <a name="prerequisite"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów znajdują się w obszarze [macierzy obsługi programu VMware na platformie Azure](vmware-physical-azure-support-matrix.md).
- [Zlokalizuj Instalatora](#locate-installer-files) na podstawie systemu operacyjnego serwera.

### <a name="on-a-windows-machine"></a>Na komputerze z systemem Windows

- Skopiuj Instalatora do folderu lokalnego (na przykład C:\Temp) na serwerze, który ma być chroniony.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Zainstaluj w następujący sposób:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Zarejestruj agenta na serwerze konfiguracji.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Ustawienia instalacji
**Ustawienie** | **Szczegóły**
--- | ---
Użycie | UnifiedAgent. exe/role \<MS/MT >/InstallLocation \<lokalizacja instalacji >/platform "VMware"/Silent
Dzienniki instalacji | W obszarze%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Obowiązkowy parametr instalacji. Określa, czy należy zainstalować usługę mobilności (MS), czy główny cel (MT).
/InstallLocation| Opcjonalny parametr. Określa lokalizację instalacji usługi mobilności (dowolny folder).
/Platform | Obowiązkowy. Określa platformę, na której zainstalowano usługę mobilności. **Oprogramowanie VMware** dla maszyn wirtualnych VMware/serwerów fizycznych; **Platforma Azure** dla maszyn wirtualnych platformy Azure.
/Silent| Opcjonalny. Określa, czy Instalator ma być uruchamiany w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Użycie | UnifiedAgentConfigurator. exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
Dzienniki konfiguracji agenta | W obszarze%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolnych prawidłowych adresów IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasła. Użyj dowolnej prawidłowej ścieżki UNC lub pliku lokalnego.

### <a name="on-a-linux-machine"></a>Na komputerze z systemem Linux

1. Skopiuj Instalatora do folderu lokalnego (na przykład/tmp) na serwerze, który ma być chroniony. W terminalu uruchom następujące polecenia:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Zainstaluj w następujący sposób:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Po zakończeniu instalacji usługa mobilności musi być zarejestrowana na serwerze konfiguracji. Uruchom następujące polecenie, aby zarejestrować usługę mobilności na serwerze konfiguracji:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Ustawienia instalacji
**Ustawienie** | **Szczegóły**
--- | ---
Użycie | ./Install-d \<lokalizacja instalacji >-r \<MS/MT >-v VMware-q
-r | Obowiązkowy parametr instalacji. Określa, czy należy zainstalować usługę mobilności (MS), czy główny cel (MT).
-c | Opcjonalny parametr. Określa lokalizację instalacji usługi mobilności:/usr/local/ASR.
-v | Obowiązkowy. Określa platformę, na której zainstalowano usługę mobilności. **Oprogramowanie VMware** dla maszyn wirtualnych VMware/serwerów fizycznych; **Platforma Azure** dla maszyn wirtualnych platformy Azure.
-q | Opcjonalny. Określa, czy Instalator ma być uruchamiany w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Użycie | /usr/local/ASR/Vx/bin CD<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolnych prawidłowych adresów IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisano hasło. Użyj dowolnego prawidłowego folderu.

## <a name="azure-virtual-machine-agent"></a>Agent maszyny wirtualnej platformy Azure

- **Maszyny wirtualne z systemem Windows**: W wersji 9.7.0.0 usługi mobilności [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) jest instalowany przez Instalatora usługi mobilności. Dzięki temu w przypadku przełączenia maszyny do trybu failover na platformie Azure maszyna wirtualna platformy Azure spełnia wymagania wstępne instalacji agenta dotyczące korzystania z dowolnego rozszerzenia maszyny wirtualnej.
- **Maszyny wirtualne z systemem Linux**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) należy ręcznie zainstalować na maszynie wirtualnej platformy Azure po przejściu do trybu failover.

## <a name="locate-installer-files"></a>Lokalizowanie plików Instalatora

Przejdź do folderu%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na serwerze konfiguracji. Sprawdź, jakiego Instalatora potrzebujesz w zależności od systemu operacyjnego. Poniższa tabela zawiera podsumowanie plików Instalatora dla każdej maszyny wirtualnej VMware i systemu operacyjnego serwera fizycznego. Możesz przejrzeć [obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines) przed rozpoczęciem.

**Plik Instalatora** | **System operacyjny (tylko 64-bitowy)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*Release. tar. gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14,04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16,04 LTS Server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj instalację wypychaną dla usługi mobilności](vmware-azure-install-mobility-service.md).
