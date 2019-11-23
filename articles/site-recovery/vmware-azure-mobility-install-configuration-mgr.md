---
title: Automatyzowanie instalacji usługi mobilności Azure Site Recovery na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu System Center Configuration Manager | Microsoft Docs
description: W tym artykule opisano automatyzację instalacji usługi mobilności z System Center Configuration Manager, na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ee92ad6e0687018f69044bf3edde76b9f98cee52
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255588"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatyzowanie instalacji usługi mobilności za pomocą System Center Configuration Manager

Usługa mobilności jest instalowana na maszynach wirtualnych VMware i serwerach fizycznych, które mają być replikowane na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md)

Ten artykuł zawiera przykład użycia System Center Configuration Manager do wdrożenia usługi mobilności Azure Site Recovery na maszynie wirtualnej VMware. Używanie narzędzia do wdrażania oprogramowania, takiego jak Configuration Manager, ma następujące zalety:

* Zaplanuj nowe instalacje i uaktualnienia podczas planowanego okna obsługi aktualizacji oprogramowania
* Skalowanie wdrożenia na setki serwerów jednocześnie

W tym artykule jest wykorzystywany System Center Configuration Manager 2012 R2 do zademonstrowania działania wdrożenia. Przyjęto założenie, że używasz wersji **9.9.4510.1** lub nowszej usługi mobilności.

Alternatywnie można zautomatyzować instalację usługi mobilności za pomocą [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. Narzędzie do wdrażania oprogramowania, takie jak Configuration Manager, które jest już wdrożone w Twoim środowisku.
2. Należy utworzyć dwie [Kolekcje urządzeń](https://technet.microsoft.com/library/gg682169.aspx), jeden dla wszystkich **serwerów z systemem Windows**i drugi dla wszystkich **serwerów z systemem Linux**, które mają być chronione przy użyciu Site Recovery.
3. Serwer konfiguracji, który jest już zarejestrowany w magazynie Recovery Services.
4. Bezpieczny, sieciowy udział plików (udział SMB), do którego można uzyskać dostęp za pomocą komputera programu Configuration Manager.

## <a name="deploy-on-windows-machines"></a>Wdrażanie na maszynach z systemem Windows
> [!NOTE]
> W tym artykule przyjęto założenie, że adres IP serwera konfiguracji to 192.168.3.121, a bezpieczny sieciowy udział plików jest \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia
1. Utwórz folder w udziale sieciowym, a następnie nadaj mu nazwę **MobSvcWindows**.
2. Zaloguj się do serwera konfiguracji, a następnie otwórz wiersz polecenia z uprawnieniami administracyjnymi.
3. Uruchom następujące polecenia, aby wygenerować plik z hasłem:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Skopiuj plik **MobSvc. hasło** do folderu **MobSvcWindows** w udziale sieciowym.
5. Przejdź do repozytorium Instalatora na serwerze konfiguracji, uruchamiając następujące polecenie:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Skopiuj **program Microsoft-ASR\_UA\_*wersja*\_Windows\_GA\_*Data*\_Release. exe** do folderu **MobSvcWindows** w udziale sieciowym.
7. Skopiuj poniższy kod i Zapisz go jako **install. bat** w folderze **MobSvcWindows** .

   > [!NOTE]
   > Zastąp symbole zastępcze [CSIP] w tym skrypcie wartościami rzeczywistymi adresu IP serwera konfiguracji.

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

### <a name="create-a-package"></a>Tworzenie pakietu

1. Zaloguj się do konsoli Configuration Manager.
2. Przejdź do **biblioteki oprogramowania** >  > **pakiety** **zarządzania aplikacjami** .
3. Kliknij prawym przyciskiem myszy pozycję **pakiety**, a następnie wybierz pozycję **Utwórz pakiet**.
4. Podaj wartości w polu Nazwa, opis, producent, język i wersja.
5. Zaznacz pole wyboru **ten pakiet zawiera pliki źródłowe** .
6. Kliknij przycisk **Przeglądaj**i wybierz udział sieciowy, w którym przechowywany jest instalator (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Na stronie **Wybierz typ programu, który chcesz utworzyć** wybierz opcję **program standardowy**, a następnie kliknij przycisk **dalej**.

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na stronie **Określ informacje dotyczące tego programu standardowego** podaj następujące dane wejściowe, a następnie kliknij przycisk **dalej**. (Inne dane wejściowe mogą używać ich wartości domyślnych).

   | **Nazwa parametru** | **Wartość** |
   |--|--|
   | Nazwa | Instalowanie usługi mobilności Microsoft Azure (Windows) |
   | Wiersz polecenia | install.bat |
   | Program może zostać uruchomiony | Bez względu na to, czy użytkownik jest zalogowany |

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Na następnej stronie wybierz docelowe systemy operacyjne. 
10. Aby zakończyć pracę kreatora, kliknij przycisk **dalej** dwa razy.


> [!NOTE]
> Skrypt obsługuje zarówno nowe instalacje agentów usługi mobilności, jak i aktualizacje agentów, którzy są już zainstalowani.

### <a name="deploy-the-package"></a>Wdróż pakiet
1. W konsoli Configuration Manager kliknij prawym przyciskiem myszy pakiet, a następnie wybierz pozycję **Dystrybuuj zawartość**.
   ![zrzut ekranu konsoli Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Wybierz **[punkty dystrybucji](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , do których mają zostać skopiowane pakiety.
3. Ukończ pracę kreatora. Pakiet rozpocznie replikację do określonych punktów dystrybucji.
4. Po zakończeniu dystrybucji pakietu kliknij prawym przyciskiem myszy pakiet, a następnie wybierz polecenie **Wdróż**.
   ![zrzut ekranu konsoli Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Wybierz kolekcję urządzeń z systemem Windows Server utworzoną w sekcji wymagania wstępne jako kolekcję docelową wdrożenia.

   ![Zrzut ekranu przedstawiający Kreatora wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Na stronie **Określanie miejsca docelowego zawartości** wybierz **punkty dystrybucji**.
7. Na stronie **Określanie ustawień w celu kontrolowania sposobu wdrażania tego oprogramowania** upewnij się, że przeznaczenie ma być **wymagane**.

   ![Zrzut ekranu przedstawiający Kreatora wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na stronie **Określanie harmonogramu dla tego wdrożenia** Określ harmonogram. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów](https://technet.microsoft.com/library/gg682178.aspx).
9. Na stronie **punkty dystrybucji** skonfiguruj właściwości zgodnie z potrzebami centrum danych. Następnie Ukończ pracę kreatora.

> [!TIP]
> Aby uniknąć niepotrzebnych ponownych uruchomień, zaplanuj instalację pakietu w trakcie miesięcznego okna obsługi lub aktualizacji oprogramowania.

Postęp wdrażania można monitorować przy użyciu konsoli Configuration Manager. Przejdź do pozycji **monitorowanie** > **wdrożenia** >  *[nazwa pakietu]* .

  ![Zrzut ekranu przedstawiający opcję Configuration Manager, aby monitorować wdrożenia](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Wdrażanie na maszynach z systemem Linux
> [!NOTE]
> W tym artykule przyjęto założenie, że adres IP serwera konfiguracji to 192.168.3.121, a bezpieczny sieciowy udział plików jest \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia
1. Utwórz folder w udziale sieciowym, a następnie nadaj mu nazwę **MobSvcLinux**.
2. Zaloguj się do serwera konfiguracji, a następnie otwórz wiersz polecenia z uprawnieniami administracyjnymi.
3. Uruchom następujące polecenia, aby wygenerować plik z hasłem:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Skopiuj plik **MobSvc. hasło** do folderu **MobSvcLinux** w udziale sieciowym.
5. Przejdź do repozytorium Instalatora na serwerze konfiguracji, uruchamiając polecenie:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Skopiuj następujące pliki do folderu **MobSvcLinux** w udziale sieciowym:
   * Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Skopiuj poniższy kod i Zapisz go jako **install_linux. sh** w folderze **MobSvcLinux** .
   > [!NOTE]
   > Zastąp symbole zastępcze [CSIP] w tym skrypcie wartościami rzeczywistymi adresu IP serwera konfiguracji.

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

1. Zaloguj się do konsoli Configuration Manager.
2. Przejdź do **biblioteki oprogramowania** >  > **pakiety** **zarządzania aplikacjami** .
3. Kliknij prawym przyciskiem myszy pozycję **pakiety**, a następnie wybierz pozycję **Utwórz pakiet**.
4. Podaj wartości w polu Nazwa, opis, producent, język i wersja.
5. Zaznacz pole wyboru **ten pakiet zawiera pliki źródłowe** .
6. Kliknij przycisk **Przeglądaj**i wybierz udział sieciowy, w którym przechowywany jest instalator (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Na stronie **Wybierz typ programu, który chcesz utworzyć** wybierz opcję **program standardowy**, a następnie kliknij przycisk **dalej**.

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na stronie **Określ informacje dotyczące tego programu standardowego** podaj następujące dane wejściowe, a następnie kliknij przycisk **dalej**. (Inne dane wejściowe mogą używać ich wartości domyślnych).

    | **Nazwa parametru** | **Wartość** |
   |--|--|
   | Nazwa | Zainstaluj usługę mobilności Microsoft Azure (Linux) |
   | Wiersz polecenia | ./install_linux.sh |
   | Program może zostać uruchomiony | Bez względu na to, czy użytkownik jest zalogowany |

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Na następnej stronie wybierz opcję **ten program można uruchomić na dowolnej platformie**.
   Zrzut ekranu przedstawiający ![Kreatora tworzenia pakietów i programów](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Aby zakończyć pracę kreatora, kliknij przycisk **dalej** dwa razy.

> [!NOTE]
> Skrypt obsługuje zarówno nowe instalacje agentów usługi mobilności, jak i aktualizacje agentów, którzy są już zainstalowani.

### <a name="deploy-the-package"></a>Wdróż pakiet
1. W konsoli Configuration Manager kliknij prawym przyciskiem myszy pakiet, a następnie wybierz pozycję **Dystrybuuj zawartość**.
   ![zrzut ekranu konsoli Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Wybierz **[punkty dystrybucji](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , do których mają zostać skopiowane pakiety.
3. Ukończ pracę kreatora. Pakiet rozpocznie replikację do określonych punktów dystrybucji.
4. Po zakończeniu dystrybucji pakietu kliknij prawym przyciskiem myszy pakiet, a następnie wybierz polecenie **Wdróż**.
   ![zrzut ekranu konsoli Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Wybierz kolekcję urządzeń serwera z systemem Linux utworzoną w sekcji wymagania wstępne jako kolekcję docelową wdrożenia.

   ![Zrzut ekranu przedstawiający Kreatora wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Na stronie **Określanie miejsca docelowego zawartości** wybierz **punkty dystrybucji**.
7. Na stronie **Określanie ustawień w celu kontrolowania sposobu wdrażania tego oprogramowania** upewnij się, że przeznaczenie ma być **wymagane**.

   ![Zrzut ekranu przedstawiający Kreatora wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na stronie **Określanie harmonogramu dla tego wdrożenia** Określ harmonogram. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów](https://technet.microsoft.com/library/gg682178.aspx).
9. Na stronie **punkty dystrybucji** skonfiguruj właściwości zgodnie z potrzebami centrum danych. Następnie Ukończ pracę kreatora.

Usługa mobilności jest instalowana w kolekcji urządzeń serwera z systemem Linux zgodnie z skonfigurowanym harmonogramem.


## <a name="uninstall-the-mobility-service"></a>Odinstalowywanie usługi mobilności
Aby odinstalować usługę mobilności, można utworzyć pakiety Configuration Manager. Aby to zrobić, użyj następującego skryptu:

```
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
Teraz można przystąpić do [włączania ochrony](vmware-azure-enable-replication.md) dla maszyn wirtualnych.
