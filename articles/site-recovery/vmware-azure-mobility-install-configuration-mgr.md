---
title: Proces instalacji usługi mobilności dla usługi Azure Site Recovery przy użyciu programu System Center Configuration Manager | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga zautomatyzować instalacji usługi mobilności za pomocą programu System Center Configuration Manager.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 8382fadc02a7e80b6f28bd777f423013aed9add3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Proces instalacji usługi mobilności z System Center Configuration Manager

Zainstalowano usługę mobilności na maszynach wirtualnych VMware i serwerów fizycznych, które chcesz replikować do platformy Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md)

Ten artykuł zawiera przykład sposobu użycia programu System Center Configuration Manager do wdrażania usługi Azure Site Recovery mobilności na maszynie Wirtualnej VMware. Przy użyciu narzędzia wdrażania oprogramowania, np. programu Configuration Manager ma następujące zalety:

* Planowanie i aktualizacji podczas okna zaplanowanej konserwacji dla aktualizacji oprogramowania
* Jednocześnie skalowania wdrożenia setki serwerów

W tym artykule używa programu System Center Configuration Manager 2012 R2 do zaprezentowania działania wdrożenia. Firma Microsoft zakłada, że używasz wersji **9.9.4510.1** lub nowszej usługi mobilności.

Alternatywnie można zautomatyzować instalacji usługi mobilności z [Konfiguracja DSC automatyzacji Azure ](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. Narzędzia wdrażania oprogramowania, takie jak programu Configuration Manager, który jest już wdrożony w środowisku.
2. Należy utworzyć dwa [kolekcje urządzeń](https://technet.microsoft.com/library/gg682169.aspx), jeden dla wszystkich **serwerów z systemem Windows**i drugi dla wszystkich **serwerów z systemem Linux**, chcesz chronić za pomocą usługi Site Recovery.
3. Serwer konfiguracji jest już zarejestrowany w magazynie usług odzyskiwania.
4. Bezpieczne sieciowego udziału plików (udział SMB) dostępnej przez komputer Menedżera konfiguracji.

## <a name="deploy-on-windows-machines"></a>Wdrażanie na komputerach z systemem Windows
> [!NOTE]
> W tym artykule przyjęto założenie, że adres IP serwera konfiguracji jest 192.168.3.121 i czy bezpiecznego sieciowego udziału plików jest \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia
1. Utwórz folder w udziale sieciowym i nadaj jej nazwę **MobSvcWindows**.
2. Zaloguj się do konfiguracji serwera, a następnie otwórz administracyjny wiersz polecenia.
3. Uruchom następujące polecenia, aby wygenerować plik hasło:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopiuj **MobSvc.passphrase** pliku do **MobSvcWindows** folderu w udziale sieciowym.
5. Przejdź do repozytorium Instalatora na serwerze konfiguracji, uruchamiając następujące polecenie:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Kopia **Microsoft ASR\_UA\_*wersji*\_Windows\_GA\_*data*\_Release.exe**  do **MobSvcWindows** folderu w udziale sieciowym.
7. Skopiuj poniższy kod i zapisz go jako **install.bat** do **MobSvcWindows** folderu.

   > [!NOTE]
   > Zastąp symbole zastępcze [CSIP] w tym skrypcie rzeczywiste wartości adresu IP serwera konfiguracji.

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

1. Zaloguj się do konsoli programu Configuration Manager.
2. Przejdź do **Biblioteka oprogramowania** > **Zarządzanie aplikacjami** > **pakiety**.
3. Kliknij prawym przyciskiem myszy **pakiety**i wybierz **tworzenia pakietu**.
4. Podaj wartości dla nazwy, opisu, producenta, język i wersję.
5. Wybierz **ten pakiet zawiera pliki źródłowe** pole wyboru.
6. Kliknij przycisk **Przeglądaj**i wybierz udziału sieciowego, w którym przechowywana jest Instalator (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Na **wybierz typ programu, który chcesz utworzyć** wybierz pozycję **Program standardowy**i kliknij przycisk **dalej**.

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na **Podaj informacje dotyczące tego programu standardowego** , podaj następujące dane wejściowe i kliknij przycisk **dalej**. (Inne dane wejściowe można użyć wartości domyślnych).

  | **Nazwa parametru** | **Wartość** |
  |--|--|
  | Name (Nazwa) | Zainstaluj usługę mobilności platformy Microsoft Azure (system Windows) |
  | Wiersz polecenia | install.bat |
  | Program można uruchomić | Określa, czy użytkownik jest zalogowany |

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Na następnej stronie wybierz systemów operacyjnych. Tylko w systemie Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2 można zainstalować usługi mobilności.

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Aby ukończyć pracę kreatora, kliknij przycisk **dalej** dwa razy.


> [!NOTE]
> Skrypt obsługuje zarówno nowe instalacje agentów usługi mobilności i aktualizacji agentów, które są już zainstalowane.

### <a name="deploy-the-package"></a>Wdrażanie pakietu
1. W konsoli programu Configuration Manager, kliknij prawym przyciskiem myszy pakiet, a następnie wybierz **Dystrybuuj zawartość**.
  ![Zrzut ekranu programu Configuration Manager konsoli](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Wybierz **[punktów dystrybucji](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , do którego powinien zostać skopiowany pakiety.
3. Ukończ pracę kreatora. Pakiet następnie rozpoczyna replikację do określonych punktach dystrybucji.
4. Po ukończeniu dystrybucji pakietu, kliknij prawym przyciskiem myszy pakiet i wybierz **Wdróż**.
  ![Zrzut ekranu programu Configuration Manager konsoli](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Zaznacz kolekcję urządzeń z systemem Windows Server, utworzony w sekcji wymagania wstępne jako kolekcję docelową dla wdrożenia.

  ![Kreator zrzut ekranu wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Na **określ miejsce docelowe zawartości** wybierz opcję sieci **punktów dystrybucji**.
7. Na **Określ ustawienia określające sposób wdrażania tego oprogramowania** upewnij się, że celem jest **wymagane**.

  ![Kreator zrzut ekranu wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na **Określ harmonogram tego wdrożenia** Określ harmonogram. Aby uzyskać więcej informacji, zobacz [planowania pakietów](https://technet.microsoft.com/library/gg682178.aspx).
9. Na **punktów dystrybucji** Skonfiguruj właściwości odpowiednio do potrzeb centrum danych. Następnie Zakończ pracę kreatora.

> [!TIP]
> Aby uniknąć niepotrzebnych ponowne uruchomienia, należy zaplanować instalacji pakietu podczas z comiesięcznej konserwacji lub okna aktualizacji oprogramowania.

Możesz monitorować postęp wdrażania przy użyciu konsoli programu Configuration Manager. Przejdź do **monitorowanie** > **wdrożeń** > *[nazwa pakietu]*.

  ![Zrzut ekranu Menedżera konfiguracji możliwość monitorowania wdrożeń](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Wdrażanie na komputerach z systemem Linux
> [!NOTE]
> W tym artykule przyjęto założenie, że adres IP serwera konfiguracji jest 192.168.3.121 i czy bezpiecznego sieciowego udziału plików jest \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia
1. Utwórz folder w udziale sieciowym i nazywa je jako **MobSvcLinux**.
2. Zaloguj się do konfiguracji serwera, a następnie otwórz administracyjny wiersz polecenia.
3. Uruchom następujące polecenia, aby wygenerować plik hasło:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopiuj **MobSvc.passphrase** pliku do **MobSvcLinux** folderu w udziale sieciowym.
5. Przejdź do repozytorium Instalatora na serwerze konfiguracji, uruchamiając polecenie:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Skopiuj następujące pliki do **MobSvcLinux** folderu w udziale sieciowym:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Skopiuj poniższy kod i zapisz go jako **install_linux.sh** do **MobSvcLinux** folderu.
   > [!NOTE]
   > Zastąp symbole zastępcze [CSIP] w tym skrypcie rzeczywiste wartości adresu IP serwera konfiguracji.

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

1. Zaloguj się do konsoli programu Configuration Manager.
2. Przejdź do **Biblioteka oprogramowania** > **Zarządzanie aplikacjami** > **pakiety**.
3. Kliknij prawym przyciskiem myszy **pakiety**i wybierz **tworzenia pakietu**.
4. Podaj wartości dla nazwy, opisu, producenta, język i wersję.
5. Wybierz **ten pakiet zawiera pliki źródłowe** pole wyboru.
6. Kliknij przycisk **Przeglądaj**i wybierz udziału sieciowego, w którym przechowywana jest Instalator (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Na **wybierz typ programu, który chcesz utworzyć** wybierz pozycję **Program standardowy**i kliknij przycisk **dalej**.

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na **Podaj informacje dotyczące tego programu standardowego** , podaj następujące dane wejściowe i kliknij przycisk **dalej**. (Inne dane wejściowe można użyć wartości domyślnych).

    | **Nazwa parametru** | **Wartość** |
  |--|--|
  | Name (Nazwa) | Zainstaluj usługę mobilności platformy Microsoft Azure (Linux) |
  | Wiersz polecenia | ./install_linux.sh |
  | Program można uruchomić | Określa, czy użytkownik jest zalogowany |

  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Na następnej stronie wybierz **ten program można uruchomić na dowolnej platformie**.
  ![Kreator zrzut ekranu tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Aby ukończyć pracę kreatora, kliknij przycisk **dalej** dwa razy.

> [!NOTE]
> Skrypt obsługuje zarówno nowe instalacje agentów usługi mobilności i aktualizacji agentów, które są już zainstalowane.

### <a name="deploy-the-package"></a>Wdrażanie pakietu
1. W konsoli programu Configuration Manager, kliknij prawym przyciskiem myszy pakiet, a następnie wybierz **Dystrybuuj zawartość**.
  ![Zrzut ekranu programu Configuration Manager konsoli](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Wybierz **[punktów dystrybucji](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , do którego powinien zostać skopiowany pakiety.
3. Ukończ pracę kreatora. Pakiet następnie rozpoczyna replikację do określonych punktach dystrybucji.
4. Po ukończeniu dystrybucji pakietu, kliknij prawym przyciskiem myszy pakiet i wybierz **Wdróż**.
  ![Zrzut ekranu programu Configuration Manager konsoli](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Zaznacz kolekcję urządzeń Linux Server utworzonego w sekcji wymagania wstępne jako kolekcję docelową dla wdrożenia.

  ![Kreator zrzut ekranu wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Na **określ miejsce docelowe zawartości** wybierz opcję sieci **punktów dystrybucji**.
7. Na **Określ ustawienia określające sposób wdrażania tego oprogramowania** upewnij się, że celem jest **wymagane**.

  ![Kreator zrzut ekranu wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na **Określ harmonogram tego wdrożenia** Określ harmonogram. Aby uzyskać więcej informacji, zobacz [planowania pakietów](https://technet.microsoft.com/library/gg682178.aspx).
9. Na **punktów dystrybucji** Skonfiguruj właściwości odpowiednio do potrzeb centrum danych. Następnie Zakończ pracę kreatora.

Pobiera zainstalować usługi mobilności na kolekcji urządzeń serwera z systemem Linux zgodnie z harmonogramem, które zostały skonfigurowane.


## <a name="uninstall-the-mobility-service"></a>Odinstaluj usługę mobilności
Można utworzyć pakiety programu Configuration Manager można odinstalować usługi mobilności. Aby to zrobić, użyj następującego skryptu:

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

## <a name="next-steps"></a>Kolejne kroki
Teraz można przystąpić do [Włącz ochronę](vmware-azure-enable-replication.md) maszyn wirtualnych.
