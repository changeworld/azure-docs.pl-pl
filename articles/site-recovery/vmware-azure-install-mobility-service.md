---
title: Zainstaluj usługę mobilności (VMware lub fizycznych do platformy Azure) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługi mobilności agenta ochrony lokalnych maszyn wirtualnych VMware i serwery fizyczne z usługi Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 3279dbc2eeecd3a3f0f36a47d8dd51ef4f3f503f
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "33942139"
---
# <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności 

Usługa mobilności Azure Site Recovery jest zainstalowany na maszynach wirtualnych VMware i serwerów fizycznych, które chcesz replikować do platformy Azure. Usługa przechwytywania zapisów danych na komputerze i przekazuje je do serwera przetwarzania. Wdrażanie usługi mobilności na każdym komputerze (maszyny Wirtualnej VMware lub serwerów fizycznych), który chcesz replikować do platformy Azure. Można wdrożyć usługi mobilności na serwerach i maszyny wirtualne VMware, które chcesz chronić za pomocą następujących metod:


* [Instalacja przy użyciu narzędzia wdrażania oprogramowania takich jak System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Instalowanie przy użyciu usługi Automatyzacja Azure i konfiguracji żądanego stanu (DSC usługi Automatyzacja)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Zainstaluj ręcznie z interfejsu użytkownika](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Zainstaluj ręcznie z wiersza polecenia](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Instalowanie przy użyciu instalacji wypychanej usługi Site Recovery](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Począwszy od wersji 9.7.0.0, na maszynach wirtualnych systemu Windows, usługa mobilności Instalator instaluje również najnowszym dostępnym [agenta maszyny Wirtualnej Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent). W przypadku awarii komputera za pośrednictwem Azure komputer spełnia wymagań wstępnych dla dowolnego rozszerzenia maszyny Wirtualnej instalacji agenta.

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj następujące kroki wymagań wstępnych, aby ręcznie zainstalować usługi mobilności na serwerze:
1. Zaloguj się do konfiguracji serwera, a następnie otwórz okno wiersza polecenia z uprawnieniami administratora.
2. Zmień katalog na bin folder, a następnie utwórz plik hasło.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Przechowuj plik hasła w bezpiecznym miejscu. Możesz użyć pliku podczas instalacji usługi mobilności.
4. Instalatorzy usługę mobilności dla wszystkich obsługiwanych systemów operacyjnych znajdują się w folderze %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mapowanie systemu Instalator działania usługi mobilności

| Nazwa szablonu pliku Instalatora| System operacyjny |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 z dodatkiem SP1 (64-bitowe) </br> Windows Server 2012 (64-bitowe) </br> Windows Server 2012 R2 (64-bitowe) </br> Windows Server 2016 (64-bitowe) |
|Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6,9 (tylko wersja 64-bitowa) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6,9 (tylko wersja 64-bitowa) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (tylko wersja 64-bitowa) </br> CentOS 7.0, 7.1, 7.2, 7.3 (tylko wersja 64-bitowa) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 z dodatkiem SP3 (tylko wersja 64-bitowa)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 z dodatkiem SP4 (tylko wersja 64-bitowa)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Linux przedsiębiorstwa 6.4, 6.5 (tylko wersja 64-bitowa)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (tylko wersja 64-bitowa)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS serwera (tylko wersja 64-bitowa)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (tylko wersja 64-bitowa)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (tylko wersja 64-bitowa)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Ręcznie zainstalować usługi mobilności przy użyciu graficznego interfejsu użytkownika

>[!IMPORTANT]
> Serwer konfiguracji umożliwia replikowanie maszyn wirtualnych IaaS platformy Azure z jednej subskrypcji/region platformy Azure do innego, należy użyć metody instalacji opartej na wiersza polecenia.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Ręcznie zainstalować usługi mobilności w wierszu polecenia

### <a name="command-line-installation-on-a-windows-computer"></a>Instalacja wiersza polecenia na komputerze z systemem Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalacja wiersza polecenia na komputerze z systemem Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Zainstaluj usługę mobilności przez instalację wypychaną z usługi Azure Site Recovery
Instalacji wypychanej usługi mobilności może zrobić przy użyciu usługi Site Recovery. Wszystkich komputerów docelowych musi spełniać następujące wymagania wstępne.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Po zainstalowaniu usługi mobilności w portalu Azure wybierz **+ Replikuj** do uruchomienia ochrony tych maszyn wirtualnych.

## <a name="update-mobility-service"></a>Aktualizacja usługi mobilności

> [!WARNING]
> Upewnij się, że serwer konfiguracji, proces skalowania w poziomie serwerów i serwerom głównego celu, które są częścią wdrożenia zostały zaktualizowane przed rozpoczęciem aktualizacji usługi Mobility na chronionych serwerach.

1. W portalu Azure, przejdź do *nazwa Twojego magazynu* > **elementy replikowane** widoku.
2. Jeśli serwer konfiguracji został już zaktualizowany do najnowszej wersji, zobacz powiadomienie o treści "dostępna jest nowa aktualizacja agenta replikacji odzyskiwania lokacji. Kliknij, aby zainstalować. "

     ![Okno zreplikowanych elementów](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Wybierz powiadomienie, aby otworzyć stronę wyboru maszyny wirtualnej.
4. Wybierz maszyny wirtualne, które chcesz uaktualnić usługi mobilności na, a następnie wybierz **OK**.

     ![Replikacja maszyny Wirtualnej na liście elementów](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Uruchamia zadanie usługi mobilności aktualizacji dla każdej z wybranych maszyn wirtualnych.

> [!NOTE]
> [Dowiedz się więcej](vmware-azure-manage-configuration-server.md) o sposobie aktualizowania hasła dla konta używanego do zainstalowania usługi mobilności.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Odinstalować usługi mobilności na komputerze z systemem Windows Server
Użyj jednej z następujących metod można odinstalować usługi mobilności na komputerze z systemem Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Odinstalować przy użyciu graficznego interfejsu użytkownika
1. W Panelu sterowania, wybierz **programy**.
2. Wybierz **Microsoft Azure Site odzyskiwania mobilności usługi/główny serwer docelowy**, a następnie wybierz **Odinstaluj**.

### <a name="uninstall-at-a-command-prompt"></a>Odinstaluj w wierszu polecenia
1. Otwórz okno wiersza polecenia z uprawnieniami administratora.
2. Aby odinstalować usługi mobilności, uruchom następujące polecenie:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Odinstalować usługi mobilności na komputerze z systemem Linux
1. Na serwerze Linux, zaloguj się jako **głównego** użytkownika.
2. W terminalu przejdź do /user/local/ASR.
3. Aby odinstalować usługi mobilności, uruchom następujące polecenie:

    ```
    uninstall.sh -Y
    ```
