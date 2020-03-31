---
title: Automatyzacja usługi mobilności w celu odzyskiwania po awarii instalacji w usłudze Azure Site Recovery
description: Jak automatycznie zainstalować usługę mobilności dla odzyskiwania po awarii VMware /physical server za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252231"
---
# <a name="automate-mobility-service-installation"></a>Automatyzacja instalacji usługi mobilności

W tym artykule opisano sposób automatyzacji instalacji i aktualizacji dla agenta usługi mobilności w [usłudze Azure Site Recovery](site-recovery-overview.md).

Podczas wdrażania odzyskiwania lokacji w celu odzyskiwania po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure, należy zainstalować agenta usługi mobilności na każdym komputerze, który chcesz replikować. Usługa mobilności przechwytuje zapisy danych na komputerze i przekazuje je do serwera przetwarzania odzyskiwania lokacji w celu replikacji. Usługę mobilności można wdrożyć na kilka sposobów:

- **Instalacja wypychania:** Pozwól odzyskiwaniu witryny zainstalować agenta usługi mobilności po włączeniu replikacji komputera w witrynie Azure portal.
- **Instalacja ręczna**: Zainstaluj usługę mobilności ręcznie na każdym komputerze. [Dowiedz się więcej](vmware-physical-mobility-service-overview.md) o wypychania i ręcznej instalacji.
- **Automatyczne wdrażanie:** Automatyzacja instalacji za pomocą narzędzi do wdrażania oprogramowania, takich jak program Microsoft Endpoint Configuration Manager, lub narzędzi innych firm, takich jak JetPatch.

Zautomatyzowana instalacja i aktualizacja stanowi rozwiązanie, jeśli:

- Organizacja nie zezwala na instalację wypychaną na chronionych serwerach.
- Zasady firmy wymagają okresowej zmiany haseł. Musisz określić hasło do instalacji wypychanej.
- Zasady zabezpieczeń nie zezwalają na dodawanie wyjątków zapory dla określonych komputerów.
- Działasz jako dostawca usług hostingowych i nie chcesz pokazywać poświadczeń komputera klienta, które są potrzebne do instalacji wypychanej za pomocą funkcji Site Recovery.
- Należy skalować instalacje agenta do wielu serwerów jednocześnie.
- Chcesz zaplanować instalacje i uaktualnienia podczas planowanych okien konserwacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do automatyzacji instalacji potrzebne są następujące elementy:

- Wdrożone rozwiązanie instalacyjne oprogramowania, takie jak [Menedżer konfiguracji](/configmgr/) lub [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Wymagania wstępne dotyczące wdrażania na [platformie Azure](tutorial-prepare-azure.md) i [lokalnie](vmware-azure-tutorial-prepare-on-premises.md) w przypadku odzyskiwania po awarii vmware lub odzyskiwania po awarii [serwera fizycznego.](physical-azure-disaster-recovery.md) Przejrzyj [wymagania dotyczące pomocy technicznej dotyczące](vmware-physical-azure-support-matrix.md) odzyskiwania po awarii.

## <a name="prepare-for-automated-deployment"></a>Przygotowanie do zautomatyzowanego wdrożenia

W poniższej tabeli podsumowano narzędzia i procesy automatyzacji wdrażania usługi mobilności.

**Narzędzie** | **Szczegóły** | **Instrukcje**
--- | --- | ---
**Configuration Manager** | 1. Sprawdź, czy masz [wymagania wstępne](#prerequisites) wymienione powyżej w miejscu. <br/><br/> 2. Wdrażanie odzyskiwania po awarii przez skonfigurowanie środowiska źródłowego, w tym pobieranie pliku OVA w celu wdrożenia serwera konfiguracji odzyskiwania lokacji jako maszyny wirtualnej VMware przy użyciu szablonu OVF.<br/><br/> 3. Zarejestruj serwer konfiguracji w usłudze Site Recovery, skonfigurujesz docelowe środowisko platformy Azure i skonfigurujesz zasady replikacji.<br/><br/> 4. W przypadku automatycznego wdrażania usługi mobilności należy utworzyć udział sieciowy zawierający hasło serwera konfiguracji i pliki instalacyjne usługi mobilności.<br/><br/> 5. Tworzenie pakietu programu Configuration Manager zawierającego instalację lub aktualizacje i przygotowanie do wdrożenia usługi mobilności.<br/><br/> 6. Następnie można włączyć replikację na platformę Azure dla komputerów, które mają zainstalowaną usługę mobilności. | [Automatyzacja za pomocą programu Configuration Manager](#automate-with-configuration-manager)
**JetPatch ( JetPatch )** | 1. Sprawdź, czy masz [wymagania wstępne](#prerequisites) wymienione powyżej w miejscu. <br/><br/> 2. Wdrażanie odzyskiwania po awarii przez skonfigurowanie środowiska źródłowego, w tym pobieranie i wdrażanie JetPatch Agent Manager dla usługi Azure Site Recovery w środowisku odzyskiwania witryny przy użyciu szablonu OVF.<br/><br/> 3. Zarejestruj serwer konfiguracji w usłudze Site Recovery, skonfigurujesz docelowe środowisko platformy Azure i skonfigurujesz zasady replikacji.<br/><br/> 4. W przypadku automatycznego wdrażania zaawaruj i ukończ konfigurację JetPatch Agent Manager.<br/><br/> 5. W JetPatch można utworzyć zasady odzyskiwania witryny w celu automatyzacji wdrażania i uaktualniania agenta usługi mobilności. <br/><br/> 6. Następnie można włączyć replikację na platformę Azure dla komputerów, które mają zainstalowaną usługę mobilności. | [Automatyzacja za pomocą Menedżera agenta JetPatch](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Rozwiązywanie problemów z instalacją agenta w JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatyzacja za pomocą programu Configuration Manager

### <a name="prepare-the-installation-files"></a>Przygotowywanie plików instalacyjnych

1. Upewnij się, że masz wymagania wstępne w miejscu.
1. Utwórz bezpieczny udział plików sieciowych (SMB), do który dostęp ma komputer z serwerem konfiguracji.
1. W programie Menedżer konfiguracji [kategoryzuj serwery,](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) na których chcesz zainstalować lub zaktualizować usługę mobilności. Jedna kolekcja powinna zawierać wszystkie serwery systemu Windows, a druga wszystkie serwery Linuksa.
1. W udziale sieciowym utwórz folder:

   - Aby uzyskać instalację na komputerach z systemem Windows, utwórz folder o nazwie _MobSvcWindows_.
   - Aby instalacja na komputerach z systemem Linux, utworzyć folder o nazwie _MobSvcLinux_.

1. Zaloguj się do komputera serwera konfiguracji.
1. Na komputerze serwera konfiguracji otwórz wiersz polecenia administracyjnego.
1. Aby wygenerować plik hasła, uruchom to polecenie:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Skopiuj plik _MobSvc.passphrase_ do folderu Windows i folderu Linux.
1. Aby przejść do folderu zawierającego pliki instalacyjne, uruchom następujące polecenie:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Skopiuj te pliki instalacyjne do udziału sieciowego:

   - W systemie Windows skopiuj _program Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ do _programu MobSvcWindows_.
   - W przypadku systemu Linux skopiuj następujące pliki do _MobSvcLinux:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Jak opisano w poniższych procedurach, skopiuj kod do folderów systemu Windows lub Linux. Zakładamy, że:

   - Adres IP serwera konfiguracji `192.168.3.121`to .
   - Bezpieczny udział plików `\\ContosoSecureFS\MobilityServiceInstallers`sieciowych to .

### <a name="copy-code-to-the-windows-folder"></a>Kopiowanie kodu do folderu systemu Windows

Skopiuj następujący kod:

- Zapisz kod w folderze _MobSvcWindows_ jako _install.bat_.
- Zastąp `[CSIP]` symbole zastępcze w tym skrypcie rzeczywistymi wartościami adresu IP serwera konfiguracji.
- Skrypt obsługuje nowe instalacje agenta usługi mobilności i aktualizacje agentów, które są już zainstalowane.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kopiowanie kodu do folderu Linux

Skopiuj następujący kod:

- Zapisz kod w folderze _MobSvcLinux_ jako _install_linux.sh_.
- Zastąp `[CSIP]` symbole zastępcze w tym skrypcie rzeczywistymi wartościami adresu IP serwera konfiguracji.
- Skrypt obsługuje nowe instalacje agenta usługi mobilności i aktualizacje agentów, które są już zainstalowane.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Tworzenie pakietu

1. Zaloguj się do konsoli programu Menedżer konfiguracji i przejdź do pakietów > **zarządzania aplikacjami** >  **biblioteki****oprogramowania**.
1. Kliknij prawym przyciskiem myszy **pozycję Tworzenie pakietów** > **Create Package**.
1. Podaj szczegóły pakietu, w tym nazwę, opis, producenta, język i wersję.
1. Wybierz **pozycję Ten pakiet zawiera pliki źródłowe**.
1. Kliknij **przycisk Przeglądaj**i wybierz udział sieciowy i folder zawierający odpowiedni instalator (_MobSvcWindows_ lub _MobSvcLinux_). Następnie wybierz przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. W **obszarze Wybierz typ programu, który chcesz utworzyć,** wybierz pozycję Następny program **standardowy** > **.**

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. W **obszarze Określanie informacji o tej standardowej** stronie programu określ następujące wartości:

    **Parametr** | **Wartość systemu Windows** | **Wartość linuksa**
    --- | --- | ---
    **Nazwa** | Instalowanie usługi mobilności platformy Microsoft Azure (Windows) | Zainstaluj usługę Microsoft Azure Mobility Service (Linux).
    **Wiersz polecenia** | Install.bat | ./install_linux.sh
    **Program można uruchomić** | Czy użytkownik jest zalogowany | Czy użytkownik jest zalogowany
    **Inne parametry** | Użyj ustawienia domyślnego | Użyj ustawienia domyślnego

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. W **obszarze Określ wymagania dla tego standardowego programu**wykonaj następujące czynności:

   - W przypadku komputerów z systemem Windows wybierz pozycję **Ten program może działać tylko na określonych platformach**. Następnie wybierz [obsługiwane systemy operacyjne Windows](vmware-physical-azure-support-matrix.md#replicated-machines) i wybierz przycisk **Dalej**.
   - W przypadku maszyn z systemem Linux wybierz **ten program może działać na dowolnej platformie.** Następnie wybierz **przycisk Dalej**.

1. Zakończ pracę kreatora.

### <a name="deploy-the-package"></a>Wdrażanie pakietu

1. W konsoli programu Menedżer konfiguracji kliknij prawym przyciskiem myszy pakiet i wybierz polecenie **Dystrybucja zawartości**.

   ![Zrzut ekranu przedstawiający konsolę programu Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Wybierz punkty dystrybucji, do których pakiety mają być kopiowane. [Dowiedz się więcej](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Wykonaj kroki kreatora. Następnie pakiet rozpoczyna replikowanie do określonych punktów dystrybucji.
1. Po zakończeniu dystrybucji pakietu kliknij prawym przyciskiem myszy pakiet > **Deploy**.

   ![Zrzut ekranu przedstawiający konsolę programu Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Wybierz utworzoną wcześniej kolekcję urządzeń z systemem Windows lub Linux.
1. Na stronie **Określanie miejsca docelowego zawartości** wybierz pozycję **Punkty dystrybucji**.
1. W **obszarze Określanie ustawień, aby kontrolować sposób wdrażania tego oprogramowania,** ustaw **opcję Cel** na **Wymagany**.

   ![Zrzut ekranu przedstawiający kreatora wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. W **obszarze Określ harmonogram dla tego wdrożenia,** skonfiguruj harmonogram. [Dowiedz się więcej](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Usługa mobilności jest instalowana zgodnie z określonym harmonogramem.
   - Aby uniknąć niepotrzebnego ponownego uruchamiania, zaplanuj instalację pakietu podczas miesięcznego okna konserwacji lub okna aktualizacji oprogramowania.

1. Na stronie **Punkty dystrybucji** skonfiguruj ustawienia i zakończ kreatora.
1. Monitorowanie postępu wdrażania w konsoli programu Menedżer konfiguracji. Przejdź do **monitorowania** > **wdrożeń** > _\<\>nazwę pakietu_.

### <a name="uninstall-the-mobility-service"></a>Odinstaluj usługę mobilności

Można utworzyć pakiety programu Configuration Manager, aby odinstalować usługę mobilności. Na przykład następujący skrypt odinstalowuje usługę mobilności:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Następne kroki

[Włącz replikację](vmware-azure-enable-replication.md) maszyn wirtualnych.
