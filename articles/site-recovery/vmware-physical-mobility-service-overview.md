---
title: Temat usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agenta usługi mobilności do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: a3e695e8c238dd4bc333d5cd477e70b93231399b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51565024"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Temat usługi mobilności na potrzeby maszyn wirtualnych VMware i serwerów fizycznych

Podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md), zainstaluj usługę Site Recovery Mobility na każdej lokalnej maszyny Wirtualnej VMware i serwerów fizycznych.  Usługa mobilności służy do przechwytywania zapisów danych na maszynie i przekazuje je do serwera przetwarzania Site Recovery. Można wdrożyć usługę mobilności, za pomocą następujących metod:

[Instalacja wypychana](vmware-azure-install-mobility-service.md): Konfigurowanie Site Recovery do przeprowadzenia instalacji wypychanej usługi mobilności: Aby to zrobić, konfigurując odzyskiwanie po awarii, można również ustawić konta używanego przez serwer przetwarzania Site Recovery do uzyskania dostępu do maszyny Wirtualnej lub serwera fizycznego na potrzeby instalowania usługi.
[Zainstaluj ręcznie](vmware-physical-mobility-service-install-manual.md): można zainstalować usługi mobilności ręcznie na każdym komputerze, przy użyciu interfejsu użytkownika lub wiersza polecenia.
[Automatyczne wdrażanie](vmware-azure-mobility-install-configuration-mgr.md): można zautomatyzować instalację przy użyciu narzędzi wdrażania oprogramowania takich jak System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Agent maszyny wirtualnej platformy Azure

- **Maszyny wirtualne Windows**: Z 9.7.0.0 wersję usługi mobilności [agenta maszyny Wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) jest instalowany przez Instalatora usługi mobilności. Gwarantuje to, czy po maszyny przejściu w tryb failover na platformie Azure, maszyny Wirtualnej platformy Azure spełnia wymagania wstępne dla przy użyciu każde rozszerzenie maszyny wirtualnej instalacji agenta.
- **Maszyny wirtualne systemu Linux**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) musi być zainstalowany ręcznie na maszynie Wirtualnej platformy Azure po włączeniu trybu failover.

## <a name="installer-files"></a>Pliki Instalatora

W tabeli przedstawiono pliki Instalatora dla każdej maszyny Wirtualnej VMware i serwera fizycznego systemu operacyjnego. Możesz przejrzeć [obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines) przed rozpoczęciem.


**Plik Instalatora** | **System operacyjny (tylko 64-bitowy)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | System Windows Server 2016; Windows Server 2012 R2 Windows Server 2012; Windows Server 2008 R2 z dodatkiem SP1 
Usługa ASR Microsoft\_UA\*RHEL6 64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Usługa ASR Microsoft\_UA\*SLES12 64\*release.tar.gz | SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, z dodatkiem SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serwer Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>Oprogramowanie antywirusowe na replikowanych maszyn

Jeśli maszyn, które mają być replikowane active oprogramowanie antywirusowe uruchomione, upewnij się, folder instalacji usługi mobilności można wykluczyć z ochrony antywirusowej operacji (*C:\ProgramData\ASR\agent*). Gwarantuje to, że replikacja działa zgodnie z oczekiwaniami.

## <a name="update-the-mobility-service"></a>Aktualizacja usługi mobilności

1. Przed rozpoczęciem upewnij się, że serwer konfiguracji, serwerów przetwarzania skalowalnego w poziomie oraz wszystkie główne serwery docelowe, które są częścią wdrożenia są aktualizowane, przed uaktualnieniem usługi Mobility na chronionych komputerach.
2. W portalu można otworzyć magazynu > **zreplikowane elementy**.
3. Jeśli serwer konfiguracji jest najnowsza wersja, zobaczysz powiadomienie o treści "dostępna jest nowa aktualizacja agenta replikacji lokacji odzyskiwania. Kliknij, aby zainstalować. "

     ![Okno replikowane elementy](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)

4. Kliknij powiadomienie, a następnie w **aktualizacja agenta**, wybierz maszyny, na których chcesz uaktualnić usługę mobilności. Następnie kliknij przycisk **OK**.

     ![Zreplikowane elementy listy maszyn wirtualnych](.\media\vmware-azure-install-mobility-service\update-okpng.png)

5. Uruchamia zadanie Aktualizacja usługi mobilności dla każdego z wybranych maszyn.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Aktualizuj konto używane do instalacji wypychanej usługi mobilności

Po wdrożeniu Site Recovery, aby umożliwić instalację wypychaną usługi Mobility określono konta używanego przez serwer procesu odzyskiwania lokacji dostęp do maszyny i zainstalować usługę po włączeniu replikacji dla maszyny. Jeśli chcesz zaktualizować poświadczenia dla tego konta, postępuj zgodnie z [w instrukcjach](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Odinstaluj usługę mobilności

### <a name="on-a-windows-machine"></a>Na komputerze Windows

Odinstaluj z interfejsu użytkownika lub z wiersza polecenia.

- **W interfejsie użytkownika**: W Panelu sterowania komputera, wybierz opcję **programy**. Wybierz **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy** > **Odinstaluj**.
- **Z poziomu wiersza polecenia**: Otwórz okno wiersza polecenia z uprawnieniami administratora na komputerze. Uruchom następujące polecenie: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Na maszynie z systemem Linux
1. Na komputerze z systemem Linux, zaloguj się jako **głównego** użytkownika.
2. W terminalu przejdź do /user/local/ASR.
3. Uruchom następujące polecenie:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie instalacji wypychanej usługi mobilności](vmware-azure-install-mobility-service.md).
