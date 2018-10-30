---
title: Instalowanie usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować agenta usługi mobilności do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 145affbcff128e0ec599ad1f97c79260b0dcae5a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212696"
---
# <a name="install-the-mobility-service-for-disaster-recovery-of-vmware-vms-and-physical-servers"></a>Instalowanie usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych

Usługa mobilności Azure Site Recovery jest zainstalowany na maszynach wirtualnych VMware i serwerów fizycznych, które mają być replikowane na platformie Azure. Ta usługa służy do przechwytywania zapisów danych na komputerze i przekazuje je do serwera przetwarzania. Wdrażanie usługi mobilności na każdym komputerze (maszyna wirtualna VMware lub serwera fizycznego), który ma zostać zreplikowana na platformę Azure. Można wdrożyć usługi mobilności na serwerach i maszyny wirtualne VMware, które chcesz chronić za pomocą następujących metod:


* [Instalowanie za pomocą narzędzi wdrażania oprogramowania takich jak System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Instalowanie przy użyciu usługi Azure Automation i Desired State Configuration (DSC usługi Automation)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Zainstaluj ręcznie z poziomu interfejsu użytkownika](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Zainstaluj ręcznie z poziomu wiersza polecenia](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Instalowanie przy użyciu instalacji wypychanej Site Recovery](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Począwszy od wersji 9.7.0.0, **na maszynach wirtualnych Windows**, Instalator usługi mobilności instaluje najnowszy dostępny [agenta maszyny Wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent). Po komputer przejściu w tryb failover na platformie Azure, komputer spełnia wymagania wstępne dla przy użyciu każde rozszerzenie maszyny Wirtualnej instalacji agenta.
> </br>Na **maszyn wirtualnych systemu Linux**, WALinuxAgent musi zostać zainstalowany ręcznie.

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj następujące kroki wymagań wstępnych, aby ręcznie zainstalować usługę mobilności na serwerze:
1. Zaloguj się do serwera konfiguracji, a następnie otwórz okno wiersza polecenia jako administrator.
2. Zmień katalog na bin folder, a następnie utwórz pliku hasła.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store pliku hasła w bezpiecznym miejscu. Możesz użyć pliku podczas instalacji usługi mobilności.
4. Instalatory usługi mobilności dla wszystkich obsługiwanych systemów operacyjnych znajdują się w folderze %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mapowania systemu Instalatora działające usługi mobilności

Aby wyświetlić listę systemów operacyjnych wersje przy użyciu zgodnych pakietu usługi mobilności odnoszą się do listy [obsługiwanych systemów operacyjnych dla maszyn wirtualnych VMware i serwerów fizycznych](vmware-physical-azure-support-matrix.md#replicated-machines).

| Nazwa szablonu pliku Instalatora| System operacyjny |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 z dodatkiem SP1 (64-bitowy) </br> Windows Server 2012 (64-bitowy) </br> Windows Server 2012 R2 (64-bitowy) </br> System Windows Server 2016 (64-bitowy) |
|Usługa ASR Microsoft\_UA\*RHEL6 64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* (tylko 64-bitowy) </br> CentOS 6.* (tylko 64-bitowy) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* (tylko 64-bitowy) </br> CentOS 7.* (tylko 64-bitowy) |
|Usługa ASR Microsoft\_UA\*SLES12 64\*release.tar.gz | SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, z dodatkiem SP3 (tylko 64-bitowy)|
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (tylko 64-bitowy)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 z dodatkiem SP4 (tylko 64-bitowy)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (tylko 64-bitowy)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (tylko 64-bitowy)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serwer Ubuntu Linux 16.04 LTS (tylko 64-bitowy)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (tylko 64-bitowy)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (tylko 64-bitowy)|

## <a name="install-mobility-service-manually-by-using-the-gui"></a>Ręcznie zainstalować usługę mobilności przy użyciu graficznego interfejsu użytkownika

>[!IMPORTANT]
> Jeśli serwer konfiguracji umożliwia replikowanie maszyn wirtualnych IaaS platformy Azure między regionami platformy Azure subskrypcji/do innego, należy użyć metody instalacji za pośrednictwem wiersza polecenia.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Ręcznie zainstalować usługę mobilności w wierszu polecenia

### <a name="command-line-installation-on-a-windows-computer"></a>Instalacji z wiersza polecenia na komputerze Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalacji z wiersza polecenia na komputerze z systemem Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Instalowanie usługi mobilności przy użyciu instalacji wypychanej z usługi Azure Site Recovery
Możesz tworzyć instalacji wypychanej usługi mobilności przy użyciu usługi Site Recovery. Wszystkie komputery docelowy musi spełniać następujące wymagania wstępne.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Po zainstalowaniu usługi mobilności w witrynie Azure portal wybierz **+ Replikuj** aby rozpocząć ochronę tych maszyn wirtualnych.

## <a name="update-mobility-service"></a>Aktualizacja usługi mobilności

> [!WARNING]
> Upewnij się, że serwer konfiguracji, serwerów przetwarzania skalowalnego w poziomie oraz wszystkie główne serwery docelowe, które są częścią wdrożenia są aktualizowane, przed rozpoczęciem aktualizacji usługi mobilności na serwerach chronionych.

1. W witrynie Azure portal przejdź do *nazwa magazynu* > **zreplikowane elementy** widoku.
2. Jeśli serwer konfiguracji został już zaktualizowany do najnowszej wersji, zostanie wyświetlone powiadomienie, że odczytuje "dostępna jest nowa aktualizacja agenta replikacji lokacji odzyskiwania. Kliknij, aby zainstalować. "

     ![Okno replikowane elementy](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Wybierz powiadomienie, aby otworzyć stronę wyboru maszyny wirtualnej.
4. Wybierz maszyny wirtualne, które chcesz uaktualnić usługę mobilności na, a następnie wybierz pozycję **OK**.

     ![Zreplikowane elementy listy maszyn wirtualnych](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Uruchamia zadanie Aktualizacja usługi mobilności dla każdego z wybranych maszyn wirtualnych.

> [!NOTE]
> [Dowiedz się więcej](vmware-azure-manage-configuration-server.md) na temat aktualizacji hasła dla konta używane do instalowania usługi mobilności.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Odinstaluj usługę mobilności na komputerze z systemem Windows Server
Aby odinstalować usługę mobilności na komputerze z systemem Windows Server, użyj jednej z następujących metod.

### <a name="uninstall-by-using-the-gui"></a>Odinstalowywanie za pomocą graficznego interfejsu użytkownika
1. W Panelu sterowania wybierz **programy**.
2. Wybierz **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy**, a następnie wybierz pozycję **Odinstaluj**.

### <a name="uninstall-at-a-command-prompt"></a>Odinstaluj polecenie w wierszu polecenia
1. Otwórz okno wiersza polecenia jako administrator.
2. Aby odinstalować usługę mobilności, uruchom następujące polecenie:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Odinstaluj usługę mobilności na komputerze z systemem Linux
1. Na serwerze systemu Linux, zaloguj się jako **głównego** użytkownika.
2. W terminalu przejdź do /user/local/ASR.
3. Aby odinstalować usługę mobilności, uruchom następujące polecenie:

    ```
    uninstall.sh -Y
    ```
