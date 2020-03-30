---
title: Konfigurowanie odzyskiwania po awarii po migracji na platformę Azure za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób przygotowania maszyn do konfigurowania odzyskiwania po awarii między regionami platformy Azure po migracji na platformę Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159111"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych platformy Azure po migracji na platformę Azure 


W tym artykule należy [wykonać migrację komputerów lokalnych do maszyn wirtualnych platformy Azure](tutorial-migrate-on-premises-to-azure.md) przy użyciu usługi Site [Recovery,](site-recovery-overview.md) a teraz chcesz skonfigurować maszyny wirtualne do odzyskiwania po awarii do pomocniczego regionu platformy Azure. W tym artykule opisano, jak upewnić się, że agent maszyny wirtualnej platformy Azure jest zainstalowany na zmigrowanych maszynach wirtualnych i jak usunąć usługę mobilności odzyskiwania witryny, która nie jest już potrzebna po migracji.



## <a name="verify-migration"></a>Weryfikowanie migracji

Przed skonfigurowaniem odzyskiwania po awarii upewnij się, że migracja została zakończona zgodnie z oczekiwaniami. Aby pomyślnie zakończyć migrację, po przemijeniu awaryjnym należy wybrać opcję **Zakończ migrację** dla każdego komputera, który ma zostać zmigrowane. 

## <a name="verify-the-azure-vm-agent"></a>Weryfikowanie agenta maszyny wirtualnej platformy Azure

Każda maszyna wirtualna platformy Azure musi mieć zainstalowany [agent maszyny Wirtualnej platformy Azure.](../virtual-machines/extensions/agent-windows.md) Aby replikować maszyny wirtualne platformy Azure, usługa Site Recovery instaluje rozszerzenie w agencie.

- Jeśli na komputerze jest uruchomiona wersja 9.7.0.0 lub nowsza usługi mobilności odzyskiwania lokacji, agent maszyny wirtualnej platformy Azure jest automatycznie instalowany przez usługę mobilności na maszynach wirtualnych systemu Windows. We wcześniejszych wersjach usługi mobilności zostanie zainstalowany agent ręcznie.
- W przypadku maszyn wirtualnych z systemem Linux należy ręcznie zainstalować agenta maszyny wirtualnej platformy Azure. Agent maszyny wirtualnej platformy Azure musi być zainstalowany tylko wtedy, gdy usługa mobilności zainstalowana na zmigrowanym komputerze jest w wersji 9.6 lub wcześniejszej.


### <a name="install-the-agent-on-windows-vms"></a>Instalowanie agenta na maszynach wirtualnych systemu Windows

Jeśli korzystasz z wersji usługi mobilności odzyskiwania witryny wcześniej niż 9.7.0.0 lub masz inną potrzebę ręcznego zainstalowania agenta, wykonaj następujące czynności:  

1. Upewnij się, że masz uprawnienia administratora na maszynie wirtualnej.
2. Pobierz [instalator programu VM Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Uruchom plik instalatora.

#### <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji
Aby sprawdzić, czy agent jest zainstalowany:

1. Na maszynie Wirtualnej platformy Azure w folderze C:\WindowsAzure\Packages powinien zostać wyświetlony plik WaAppAgent.exe.
2. Kliknij prawym przyciskiem myszy plik, a następnie w polu **Właściwości**wybierz kartę **Szczegóły.**
3. Sprawdź, czy w polu Wersja produktu jest **wyświetlana** wersja 2.6.1198.718 lub nowsza.

[Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o instalacji agenta dla systemu Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instalowanie agenta na maszynach wirtualnych z systemem Linux

Zainstaluj agenta [maszyny Wirtualnej systemu Azure z systemem Linux](../virtual-machines/extensions/agent-linux.md) ręcznie w następujący sposób:

1. Upewnij się, że masz uprawnienia administratora na komputerze.
2. Zdecydowanie zaleca się zainstalowanie agenta maszyny Wirtualnej systemu Linux przy użyciu rpm lub pakietu DEB z repozytorium pakietów dystrybucji. Wszyscy [dostawcy zatwierdzonych dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrują pakiet agenta usługi Azure Linux z ich obrazami i repozytoriami.
    - Zdecydowanie zaleca się aktualizowanie agenta tylko za pośrednictwem repozytorium dystrybucji.
    - Nie zaleca się instalowania agenta maszyny Wirtualnej systemu Linux bezpośrednio z usługi GitHub i aktualizowania go.
    -  Jeśli najnowszy agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną dystrybucji, aby uzyskać instrukcje dotyczące sposobu jej instalowania. 

#### <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji 

1. Uruchom to polecenie: **ps -e,** aby upewnić się, że agent platformy Azure jest uruchomiony na maszynie Wirtualnej systemu Linux.
2. Jeśli proces nie jest uruchomiony, uruchom go ponownie za pomocą następujących poleceń:
    - Dla Ubuntu: **uruchomienie walinuxagent usługi**
    - W przypadku innych dystrybucji: **uruchomienie waagenta serwisowego**


## <a name="uninstall-the-mobility-service"></a>Odinstaluj usługę mobilności

1. Ręcznie odinstaluj usługę mobilności z maszyny Wirtualnej platformy Azure, korzystając z jednej z następujących metod. 
    - W przypadku systemu Windows w Panelu sterowania > **dodawanie/usuwanie programów**odinstalowywania **usługi mobilności odzyskiwania witryny platformy Microsoft Azure/serwera docelowego głównego**. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - W przypadku systemu Linux zaloguj się jako użytkownik root. W terminalu przejdź do **/user/local/ASR**i uruchom następujące polecenie:
        ```
        ./uninstall.sh -Y
        ```
2. Uruchom ponownie maszynę wirtualną przed skonfigurowaniem replikacji.

## <a name="next-steps"></a>Następne kroki

[Przejrzyj rozwiązywanie problemów](site-recovery-extension-troubleshoot.md) z rozszerzeniem odzyskiwania lokacji w agencie maszyny Wirtualnej platformy Azure.
[Szybkie replikowanie](azure-to-azure-quickstart.md) maszyny Wirtualnej platformy Azure do regionu pomocniczego.
