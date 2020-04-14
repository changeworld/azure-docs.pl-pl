---
title: Informacje o usłudze mobilności do odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych vmware za pomocą usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Dowiedz się więcej o agencie usługi mobilności do odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259808"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informacje o usłudze mobilności dla maszyn wirtualnych VMware i serwerów fizycznych

Po skonfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware (VM) i serwerów fizycznych przy użyciu [usługi Azure Site Recovery,](site-recovery-overview.md)należy zainstalować usługę mobilności odzyskiwania witryny na każdej lokalnej maszynie wirtualnej VMware i serwerze fizycznym. Usługa mobilności przechwytuje zapisy danych na komputerze i przekazuje je do serwera przetwarzania odzyskiwania lokacji. Usługa mobilności jest instalowana przez oprogramowanie agenta usługi mobilności, które można wdrożyć przy użyciu następujących metod:

- [Instalacja wypychania:](#push-installation)Gdy ochrona jest włączona za pośrednictwem portalu Azure, usługa Site Recovery instaluje usługę mobilności na serwerze.
- Instalacja ręczna: Usługę mobilności można zainstalować ręcznie na każdym komputerze za pośrednictwem [interfejsu użytkownika (UI)](#install-the-mobility-service-using-ui) lub [wiersza polecenia](#install-the-mobility-service-using-command-prompt).
- [Automatyczne wdrażanie:](vmware-azure-mobility-install-configuration-mgr.md)Instalację usługi mobilności można zautomatyzować za pomocą narzędzi do wdrażania oprogramowania, takich jak program Menedżer konfiguracji.

> [!NOTE]
> Usługa mobilności zużywa około 6%-10% pamięci na maszynach źródłowych dla maszyn wirtualnych VMware lub komputerów fizycznych.

## <a name="antivirus-on-replicated-machines"></a>Program antywirusowy na zreplikowanych komputerach

Jeśli na komputerach, które chcesz replikować, są uruchomione oprogramowanie antywirusowe, z operacji antywirusowych należy wykluczyć folder instalacyjny usługi mobilności _C:\ProgramData\ASR\agent._ To wykluczenie gwarantuje, że replikacja będzie działać zgodnie z oczekiwaniami.

## <a name="push-installation"></a>Instalacja wypychania

Instalacja wypychania jest integralną częścią zadania uruchamianego z witryny Azure Portal w celu [włączenia replikacji.](vmware-azure-enable-replication.md#enable-replication) Po wybraniu zestawu maszyn wirtualnych, które mają być chronione i włączyć replikację, serwer konfiguracji wypycha agenta usługi mobilności na serwery, instaluje agenta i kończy rejestrację agenta na serwerze konfiguracji.

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wszystkie [wymagania wstępne instalacji wypychania](vmware-azure-install-mobility-service.md) są spełnione.
- Upewnij się, że wszystkie konfiguracje serwerów spełniają kryteria [macierzy pomocy technicznej dotyczące odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.](vmware-physical-azure-support-matrix.md)

Przepływ pracy instalacji wypychanej jest opisany w następujących sekcjach:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agent usługi mobilności w wersji 9.23 lub nowszej

Aby uzyskać więcej informacji na temat wersji 9.23, zobacz [Pakiet zbiorczy aktualizacji 35 dla usługi Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Podczas instalacji wypychanej usługi Mobility wykonywane są następujące kroki:

1. Agent jest wypychany do komputera źródłowego. Kopiowanie agenta na komputer źródłowy może zakończyć się niepowodzeniem z powodu wielu błędów środowiskowych. Zapoznaj się z [naszymi wskazówkami,](vmware-azure-troubleshoot-push-install.md) aby rozwiązywać problemy z awariami instalacji wypychanych.
1. Po pomyślnym skopiowaniu agenta na serwer na serwerze jest wykonywane sprawdzanie wymagań wstępnych.
   - Jeśli spełnione są wszystkie wymagania wstępne, rozpocznie się instalacja.
   - Instalacja kończy się niepowodzeniem, jeśli co najmniej jeden z [wymagań wstępnych](vmware-physical-azure-support-matrix.md) nie są spełnione.
1. W ramach instalacji agenta jest zainstalowany dostawca usługi kopiowania woluminów w tle (VSS) dla usługi Azure Site Recovery. Dostawca usługi VSS służy do generowania punktów odzyskiwania spójnych z aplikacjami. Jeśli instalacja dostawcy usługi VSS nie powiedzie się, ten krok zostanie pominięty i instalacja agenta będzie kontynuowana.
1. Jeśli instalacja agenta zakończy się pomyślnie, ale instalacja dostawcy usługi VSS nie powiedzie się, stan zadania zostanie oznaczony jako **Ostrzeżenie**. Nie ma to wpływu na generowanie punktów odzyskiwania spójnych z awariami.

    - Aby wygenerować punkty odzyskiwania spójne z aplikacjami, zapoznaj się [z naszymi wskazówkami,](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) aby ukończyć ręczną instalację dostawcy usługi VSS usługi Site Recovery.
    - Jeśli nie chcesz generować punktów odzyskiwania spójnych z aplikacjami, [zmodyfikuj zasady replikacji,](vmware-azure-set-up-replication.md#create-a-policy) aby wyłączyć punkty odzyskiwania spójne z aplikacjami.

### <a name="mobility-service-agent-version-922-and-below"></a>Agent usługi mobilności w wersji 9.22 i poniżej

1. Agent jest wypychany do komputera źródłowego. Kopiowanie agenta na komputer źródłowy może zakończyć się niepowodzeniem z powodu wielu błędów środowiskowych. Zapoznaj się z [naszymi wskazówkami,](vmware-azure-troubleshoot-push-install.md) aby rozwiązać problemy z awariami instalacji wypychanej.
1. Po pomyślnym skopiowaniu agenta na serwer na serwerze jest wykonywane sprawdzanie wymagań wstępnych.
   - Jeśli spełnione są wszystkie wymagania wstępne, rozpocznie się instalacja.
   - Instalacja kończy się niepowodzeniem, jeśli co najmniej jeden z [wymagań wstępnych](vmware-physical-azure-support-matrix.md) nie są spełnione.

1. W ramach instalacji agenta jest zainstalowany dostawca usługi kopiowania woluminów w tle (VSS) dla usługi Azure Site Recovery. Dostawca usługi VSS służy do generowania punktów odzyskiwania spójnych z aplikacjami.
   - Jeśli instalacja dostawcy usługi VSS nie powiedzie się, instalacja agenta zakończy się niepowodzeniem. Aby uniknąć awarii instalacji agenta, należy użyć [wersji 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) lub nowszej, aby wygenerować punkty odzyskiwania zgodne z awariami i wykonać ręczną instalację dostawcy usługi VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instalowanie usługi mobilności przy użyciu interfejsu użytkownika

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów spełniają kryteria [macierzy pomocy technicznej dotyczące odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.](vmware-physical-azure-support-matrix.md)
- [Znajdź instalator](#locate-installer-files) systemu operacyjnego serwera.

>[!IMPORTANT]
> Nie używaj metody instalacji interfejsu użytkownika, jeśli replikujesz maszynę wirtualną infrastruktury platformy Azure jako usługi (IaaS) z jednego regionu platformy Azure do drugiego. Użyj instalacji [wiersza polecenia.](#install-the-mobility-service-using-command-prompt)

1. Skopiuj plik instalacyjny do urządzenia i uruchom go.
1. W **opcji instalacji**wybierz pozycję **Zainstaluj usługę mobilności**.
1. Wybierz lokalizację instalacji i wybierz pozycję **Zainstaluj**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Strona opcji instalacji usługi mobilności.":::

1. Monitorowanie instalacji w **toku instalacji**. Po zakończeniu instalacji wybierz **pozycję Przejdź do konfiguracji,** aby zarejestrować usługę na serwerze konfiguracji.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Strona rejestracji usługi mobilności.":::

1. W **obszarze Szczegóły serwera konfiguracji**określ adres IP i hasło skonfigurowane.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Strona rejestracji usługi mobilności.":::

1. Wybierz **zarejestruj się,** aby zakończyć rejestrację.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Strona końcowa rejestracji usługi mobilności.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instalowanie usługi mobilności przy użyciu wiersza polecenia

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwerów spełniają kryteria [macierzy pomocy technicznej dotyczące odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.](vmware-physical-azure-support-matrix.md)
- [Znajdź instalator](#locate-installer-files) systemu operacyjnego serwera.

### <a name="windows-machine"></a>Komputer z systemem Windows

- W wierszu polecenia uruchom następujące polecenia, aby skopiować instalatora do folderu lokalnego, takiego jak _C:\Temp,_ na serwerze, który chcesz chronić. Zastąp nazwę pliku instalatora rzeczywistą nazwą pliku.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Uruchom to polecenie, aby zainstalować agenta.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Uruchom te polecenia, aby zarejestrować agenta na serwerze konfiguracji.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Ustawienia instalacji

Ustawienie | Szczegóły
--- | ---
Składnia | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Dzienniki instalacji | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Obowiązkowy parametr instalacji. Określa, czy usługa mobilności (MS) lub główny obiekt docelowy (MT) powinny być zainstalowane.
`/InstallLocation`| Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności (dowolny folder).
`/Platform` | Obowiązkowy. Określa platformę, na której jest zainstalowana usługa mobilności: <br/> **VMware** dla VMware VM/serwerów fizycznych. <br/> **Maszyny** wirtualne platformy Azure dla platformy Azure.<br/><br/> Jeśli traktujesz maszyny wirtualne platformy Azure jako maszyny fizyczne, określ **program VMware**.
`/Silent`| Element opcjonalny. Określa, czy instalator ma być uruchamiany w trybie cichym.

#### <a name="registration-settings"></a>Ustawienia rejestracji

Ustawienie | Szczegóły
--- | ---
Składnia | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Dzienniki konfiguracji agenta | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parametr obowiązkowy. `<CSIP>`określa adres IP serwera konfiguracji. Użyj dowolnego prawidłowego adresu IP.
`/PassphraseFilePath` |  Obowiązkowy. Lokalizacja hasła. Użyj dowolnej prawidłowej ścieżki pliku UNC lub lokalnego.

### <a name="linux-machine"></a>Maszyna Linux

1. Z sesji terminalu skopiuj instalatora do folderu lokalnego, takiego jak _/tmp_ na serwerze, który chcesz chronić. Zastąp nazwę pliku instalatora rzeczywistą nazwą pliku dystrybucji systemu Linux, a następnie uruchom polecenia.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Zainstaluj w następujący sposób:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Po zakończeniu instalacji usługa mobilności musi być zarejestrowana na serwerze konfiguracji. Uruchom następujące polecenie, aby zarejestrować usługę mobilności na serwerze konfiguracji.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Ustawienia instalacji

Ustawienie | Szczegóły
--- | ---
Składnia | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Obowiązkowy parametr instalacji. Określa, czy usługa mobilności (MS) lub główny obiekt docelowy (MT) powinny być zainstalowane.
`-d` | Parametr opcjonalny. Określa lokalizację instalacji usługi `/usr/local/ASR`mobilności: .
`-v` | Obowiązkowy. Określa platformę, na której jest zainstalowana usługa mobilności. <br/> **VMware** dla VMware VM/serwerów fizycznych. <br/> **Maszyny** wirtualne platformy Azure dla platformy Azure.
`-q` | Element opcjonalny. Określa, czy instalator ma być uruchamiany w trybie cichym.

#### <a name="registration-settings"></a>Ustawienia rejestracji

Ustawienie | Szczegóły
--- | ---
Składnia | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parametr obowiązkowy. `<CSIP>`określa adres IP serwera konfiguracji. Użyj dowolnego prawidłowego adresu IP.
`-P` |  Obowiązkowy. Pełna ścieżka pliku, w którym jest zapisywane hasło. Użyj dowolnego prawidłowego folderu.

## <a name="azure-virtual-machine-agent"></a>Agent maszyny wirtualnej platformy Azure

- **Maszyny wirtualne systemu Windows:** Od wersji 9.7.0.0 usługi mobilności [agent maszyny wirtualnej platformy Azure](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) jest instalowany przez instalatora usługi mobilności. Gwarantuje to, że gdy komputer działa awaryjnie na platformie Azure, maszyna wirtualna platformy Azure spełnia wymagania wstępne instalacji agenta do korzystania z dowolnego rozszerzenia maszyny Wirtualnej.
- **Maszyny wirtualne z systemem Linux:** [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) musi być zainstalowany ręcznie na maszynie Wirtualnej platformy Azure po przemijanym trybie failover.

## <a name="locate-installer-files"></a>Lokalizowanie plików instalatora

Na serwerze konfiguracji przejdź do folderu _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repozytorium_. Sprawdź, którego instalatora potrzebujesz na podstawie systemu operacyjnego. W poniższej tabeli podsumowano pliki instalacyjne dla każdej maszyny wirtualnej VMware i fizycznego systemu operacyjnego serwera. Przed rozpoczęciem można przejrzeć [obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Nazwy plików używają składni pokazanej w poniższej tabeli z _wersją_ i _datą_ jako symbolami zastępczymi dla wartości rzeczywistych. Rzeczywiste nazwy plików będą wyglądać podobnie do tych przykładów:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Plik instalatora | System operacyjny (tylko 64-bitowy)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 zw. </br> Zawiera sp2 i SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Serwer Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj instalację wypychaną dla usługi Mobilności](vmware-azure-install-mobility-service.md).
