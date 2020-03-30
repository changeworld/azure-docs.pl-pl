---
title: Informacje o usłudze mobilności do odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych vmware za pomocą usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Dowiedz się więcej o agencie usługi mobilności do odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256837"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informacje o usłudze mobilności dla maszyn wirtualnych VMware i serwerów fizycznych

Po skonfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md)należy zainstalować usługę mobilności odzyskiwania witryny na każdej lokalnej maszynie wirtualnej VMware i na serwerze fizycznym.  Usługa mobilności przechwytuje zapisy danych na komputerze i przekazuje je do serwera przetwarzania odzyskiwania lokacji. Usługę mobilności można wdrożyć przy użyciu następujących metod:

- [Instalacja wypychania:](#push-installation)Usługa Site Recovery instaluje agenta mobilności na serwerze, gdy ochrona jest włączona za pośrednictwem witryny Azure portal.
- Zainstaluj ręcznie: Usługę mobilności można zainstalować ręcznie na każdym komputerze za pomocą [interfejsu użytkownika](#install-mobility-agent-through-ui) lub [wiersza polecenia](#install-mobility-agent-through-command-prompt).
- [Automatyczne wdrażanie:](vmware-azure-mobility-install-configuration-mgr.md)Instalację można zautomatyzować za pomocą narzędzi do wdrażania oprogramowania, takich jak program Menedżer konfiguracji.

> [!NOTE]
> Agent mobilności zużywa około 6%-10% pamięci na maszynach źródłowych dla maszyn wirtualnych VMware lub komputerów fizycznych.

## <a name="anti-virus-on-replicated-machines"></a>Ochrona antywirusowa na replikowanych maszynach

Jeśli maszyny, które chcesz replikować, mają uruchomione aktywne oprogramowanie antywirusowe, upewnij się, że folder instalacyjny usługi mobilności został wykluczony z operacji antywirusowych (*C:\ProgramData\ASR\agent*). Gwarantuje to, że replikacja działa zgodnie z oczekiwaniami.

## <a name="push-installation"></a>Instalacja wypychania

Instalacja wypychania jest integralną częścią zadania "[Włącz replikację](vmware-azure-enable-replication.md#enable-replication)" wyzwalanego w portalu. Po wybraniu zestawu maszyn wirtualnych, które chcesz chronić i wyzwolić "Włącz replikację", serwer konfiguracyjny wypycha agenta mobilności na serwery, instaluje agenta i pełną rejestrację agenta z serwerem konfiguracji. W celu pomyślnego zakończenia tej operacji

- Upewnij się, że wszystkie [wymagania wstępne instalacji wypychania](vmware-azure-install-mobility-service.md) są spełnione.
- Upewnij się, że wszystkie konfiguracje serwerów są objęte [macierzą obsługi VMware do scenariusza odzyskiwania po awarii platformy Azure.](vmware-physical-azure-support-matrix.md)

Szczegóły przepływu pracy instalacji wypychanej zostały opisane w poniższych sekcjach.

### <a name="from-923-version-onwards"></a>Od [wersji 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

Podczas instalacji push agenta mobilności wykonywane są następujące kroki

1. Wypycha agenta na maszynę źródłową. Kopiowanie agenta na komputerze źródłowym może zakończyć się niepowodzeniem z powodu wielu błędów środowiskowych. Zapoznaj się z [naszymi wskazówkami,](vmware-azure-troubleshoot-push-install.md) aby rozwiązywać problemy z awariami instalacji wypychanych.
2. Po pomyślnym skopiowaniu agenta na serwerze są wykonywane kontrole wymagań wstępnych na serwerze. Instalacja kończy się niepowodzeniem, jeśli co najmniej jedno [z wymagań wstępnych](vmware-physical-azure-support-matrix.md) nie jest spełnione. Jeśli spełnione są wszystkie wymagania wstępne, instalacja jest wyzwalana.
3. Dostawca usługi Azure Site Recovery VSS jest zainstalowany na serwerze w ramach instalacji agenta mobilności. Ten dostawca jest używany do generowania aplikacji spójne punkty. Jeśli instalacja dostawcy usługi VSS nie powiedzie się, ten krok zostanie pominięty i instalacja agenta będzie kontynuowana.
4. Jeśli instalacja agenta zakończy się pomyślnie, ale instalacja dostawcy usługi VSS nie powiedzie się, stan zadania jest oznaczony jako "Ostrzeżenie". Nie ma to wpływu na generowanie punktów spójności awarii.

    a. Aby wygenerować spójne punkty aplikacji, zapoznaj się [z naszymi wskazówkami,](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) aby ręcznie ukończyć instalację dostawcy usługi SITE Recovery VSS. </br>
    b.  Jeśli nie chcesz, aby punkty spójne aplikacji zostały wygenerowane, [zmodyfikuj zasady replikacji,](vmware-azure-set-up-replication.md#create-a-policy) aby wyłączyć punkty spójne aplikacji.

### <a name="before-922-versions"></a>Przed wersjami 9.22

1. Wypycha agenta na maszynę źródłową. Kopiowanie agenta na komputerze źródłowym może zakończyć się niepowodzeniem z powodu wielu błędów środowiskowych. Zapoznaj się z [naszymi wskazówkami,](vmware-azure-troubleshoot-push-install.md) aby rozwiązywać problemy z awariami instalacji wypychanych.
2. Po pomyślnym skopiowaniu agenta na serwerze są wykonywane kontrole wymagań wstępnych na serwerze. Instalacja kończy się niepowodzeniem, jeśli co najmniej jedno [z wymagań wstępnych](vmware-physical-azure-support-matrix.md) nie jest spełnione. Jeśli spełnione są wszystkie wymagania wstępne, instalacja jest wyzwalana.
3. Dostawca usługi Azure Site Recovery VSS jest zainstalowany na serwerze w ramach instalacji agenta mobilności. Ten dostawca jest używany do generowania aplikacji spójne punkty. Jeśli instalacja dostawcy usługi VSS nie powiedzie się, instalacja agenta zakończy się niepowodzeniem. Aby uniknąć awarii instalacji agenta mobilności, użyj [wersji 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) lub nowszej, aby wygenerować punkty spójne z awarią i ręcznie zainstalować dostawcę usługi VSS.

## <a name="install-mobility-agent-through-ui"></a>Instalowanie agenta mobilności za pośrednictwem interfejsu użytkownika

### <a name="prerequisite"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów są objęte [macierzą obsługi VMware do scenariusza odzyskiwania po awarii platformy Azure.](vmware-physical-azure-support-matrix.md)
- [Znajdź instalatora](#locate-installer-files) na podstawie systemu operacyjnego serwera.

>[!IMPORTANT]
> Jeśli replikujesz maszynę wirtualną usługi Azure IaaS z jednego regionu platformy Azure do innego, nie używaj tej metody. Zamiast tego należy użyć metody instalacji opartej na wierszu polecenia.

1. Skopiuj plik instalacyjny do urządzenia i uruchom go.
2. W **opcji instalacji**wybierz pozycję **Zainstaluj usługę mobilności**.
3. Wybierz lokalizację instalacji > **Zainstaluj**.

    ![Strona opcji instalacji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitorowanie instalacji w **toku instalacji**. Po zakończeniu instalacji wybierz **pozycję Przejdź do konfiguracji,** aby zarejestrować usługę na serwerze konfiguracji.

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. W **obszarze Szczegóły serwera konfiguracji**określ skonfigurowany adres IP i hasło.

    ![Strona rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Wybierz **zarejestruj się,** aby zakończyć rejestrację.

    ![Strona końcowa rejestracji usługi mobilności](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalowanie agenta mobilności za pomocą wiersza polecenia

### <a name="prerequisite"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów są objęte [macierzą obsługi VMware do scenariusza odzyskiwania po awarii platformy Azure.](vmware-physical-azure-support-matrix.md)
- [Znajdź instalatora](#locate-installer-files) na podstawie systemu operacyjnego serwera.

### <a name="on-a-windows-machine"></a>Na komputerze z systemem Windows

- Skopiuj instalatora do folderu lokalnego (na przykład C:\Temp) na serwerze, który chcesz chronić.

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
Sposób użycia | UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent
Dzienniki instalacji | W obszarze %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Rola | Obowiązkowy parametr instalacji. Określa, czy usługa mobilności (MS) lub główny obiekt docelowy (MT) powinny być zainstalowane.
/InstallLocation| Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności (dowolny folder).
/Platforma | Obowiązkowy. Określa platformę, na której jest zainstalowana usługa mobilności. **VMware** dla maszyn wirtualnych VMware/serwerów fizycznych; **Maszyny** wirtualne platformy Azure dla platformy Azure.<br/><br/> Jeśli traktujesz maszyny wirtualne platformy Azure jako maszyny fizyczne, określ **program VMware**.
/Silent| Element opcjonalny. Określa, czy instalator ma być uruchamiany w trybie cichym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath PassphraseFilePath \<>Path
Dzienniki konfiguracji agenta | W obszarze %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parametr obowiązkowy. Określa adres IP serwera konfiguracji. Użyj dowolnego prawidłowego adresu IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasła. Użyj dowolnej prawidłowej ścieżki pliku UNC lub lokalnego.

### <a name="on-a-linux-machine"></a>Na komputerze z systemem Linux

1. Skopiuj instalatora do folderu lokalnego (na przykład /tmp) na serwerze, który chcesz chronić. W terminalu uruchom następujące polecenia:

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
Sposób użycia | ./install -d \<Zainstaluj> lokalizacji \<-r MS/MT> -v VmWare -q
-r | Obowiązkowy parametr instalacji. Określa, czy usługa mobilności (MS) lub główny obiekt docelowy (MT) powinny być zainstalowane.
-d | Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności: /usr/local/ASR.
-v | Obowiązkowy. Określa platformę, na której jest zainstalowana usługa mobilności. **VMware** dla maszyn wirtualnych VMware/serwerów fizycznych; **Maszyny** wirtualne platformy Azure dla platformy Azure.
-q | Element opcjonalny. Określa, czy instalator ma być uruchamiany w trybie cichym.

#### <a name="registration-settings"></a>Ustawienia rejestracji
**Ustawienie** | **Szczegóły**
--- | ---
Sposób użycia | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Parametr obowiązkowy. Określa adres IP serwera konfiguracji. Użyj dowolnego prawidłowego adresu IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym jest zapisywane hasło. Użyj dowolnego prawidłowego folderu.

## <a name="azure-virtual-machine-agent"></a>Agent maszyny wirtualnej platformy Azure

- **Maszyny wirtualne systemu Windows:** Od wersji 9.7.0.0 usługi mobilności [agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) jest instalowany przez instalatora usługi mobilności. Gwarantuje to, że gdy komputer działa awaryjnie na platformie Azure, maszyna wirtualna platformy Azure spełnia wymagania wstępne instalacji agenta przy użyciu dowolnego rozszerzenia maszyny Wirtualnej.
- **Maszyny wirtualne z systemem Linux:** [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) musi być zainstalowany ręcznie na maszynie Wirtualnej platformy Azure po przemijanym trybie failover.

## <a name="locate-installer-files"></a>Lokalizowanie plików instalatora

Przejdź do folderu %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na serwerze konfiguracji. Sprawdź, którego instalatora potrzebujesz w oparciu o system operacyjny. W poniższej tabeli podsumowano pliki instalacyjne dla każdej maszyny wirtualnej VMware i fizycznego systemu operacyjnego serwera. Przed uruchomieniem można przejrzeć [obsługiwane systemy operacyjne.](vmware-physical-azure-support-matrix.md#replicated-machines)

**Plik instalatora** | **System operacyjny (tylko 64-bitowy)**
--- | ---
Program\_Microsoft-ASR\*\*UA Windows release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 zw.
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serwer Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj instalację wypychaną dla usługi Mobilności](vmware-azure-install-mobility-service.md).
