---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure po migracji na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przygotować maszyny, aby skonfigurować odzyskiwanie po awarii między regionami platformy Azure po migracji na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 019c6ec776277a9102cb95cd685bbae0fc660d66
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615918"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych platformy Azure po migracji na platformę Azure 


Postępuj zgodnie z tego artykułu, jeśli masz [migrację maszyn lokalnych do maszyn wirtualnych platformy Azure](tutorial-migrate-on-premises-to-azure.md) przy użyciu [Site Recovery](site-recovery-overview.md) usługi, a teraz chcesz uzyskać maszyn wirtualnych, konfigurowanie odzyskiwania po awarii do regionu pomocniczego platformy Azure. W artykule opisano, jak upewnić się, że agent maszyny Wirtualnej platformy Azure jest zainstalowany na maszynach wirtualnych migrowanych oraz jak usunąć usługę Site Recovery Mobility po migracji nie są już potrzebne.



## <a name="verify-migration"></a>Weryfikowanie migracji

Przed rozpoczęciem konfigurowania odzyskiwania po awarii, upewnij się, że migracja została zakończona, zgodnie z oczekiwaniami. Pomyślnie migracji, po włączeniu trybu failover, należy wybrać **kończenia migracji** opcji dla każdej maszyny, które mają zostać zmigrowane. 

## <a name="verify-the-azure-vm-agent"></a>Sprawdź, agent maszyny Wirtualnej platformy Azure

Każda maszyna wirtualna platformy Azure musi mieć [agenta maszyny Wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) zainstalowane. Aby replikować maszyny wirtualne platformy Azure, Usługa Site Recovery instaluje rozszerzenie na agencie.

- Jeśli komputer jest uruchomiona wersja 9.7.0.0 lub później usługi Site Recovery Mobility agent maszyny Wirtualnej platformy Azure jest automatycznie instalowany przez usługę mobilności na maszynach wirtualnych Windows. We wcześniejszych wersjach usługi mobilności musisz zainstalować agenta automatycznie.
- W przypadku maszyn wirtualnych systemu Linux trzeba ręcznie zainstalować agenta maszyny Wirtualnej platformy Azure. Musisz zainstalować agenta maszyny Wirtualnej platformy Azure, jeśli zainstalowana na zmigrowanej maszynie usługa mobilności jest v9.6 lub wcześniej.


### <a name="install-the-agent-on-windows-vms"></a>Zainstaluj agenta na maszynach wirtualnych Windows

Jeśli używasz wersji wcześniejszej niż 9.7.0.0 usługa mobilności Site Recovery lub niektórych innych trzeba zainstalować agenta ręcznie, wykonaj następujące czynności:  

1. Upewnij się, że masz uprawnienia administratora na maszynie Wirtualnej.
2. Pobierz [Instalatora agenta maszyny Wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Uruchom plik Instalatora.

#### <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji
Aby sprawdzić, czy agent jest zainstalowany:

1. Na maszynie Wirtualnej platformy Azure, w folderze C:\WindowsAzure\Packages powinny być widoczne w nim plik WaAppAgent.exe.
2. Kliknij prawym przyciskiem myszy plik, a następnie w **właściwości**, wybierz opcję **szczegóły** kartę.
3. Upewnij się, że **wersji produktu** pola pokazuje się wartość 2.6.1198.718 lub wyższa.

[Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) informacje dotyczące instalacji agent for Windows.

### <a name="install-the-agent-on-linux-vms"></a>Zainstaluj agenta na maszynach wirtualnych z systemem Linux

Zainstaluj [maszyny Wirtualnej systemu Linux platformy Azure](../virtual-machines/extensions/agent-linux.md) agenta ręcznie w następujący sposób:

1. Upewnij się, że masz uprawnienia administratora na komputerze.
2. Zdecydowanie zaleca się zainstalowanie agenta maszyny Wirtualnej systemu Linux przy użyciu RPM lub DEB pakietu z repozytorium pakietów w Twojej dystrybucji. Wszystkie [zatwierdzonego dla dostawców dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Zintegruj pakiet Azure Linux agent repozytoriów i obrazów.
    - Zdecydowanie zaleca się zaktualizowanie agenta wyłącznie za pośrednictwem repozytorium dystrybucji.
    - Nie zaleca się zainstalowanie agenta maszyny Wirtualnej systemu Linux bezpośrednio z serwisu GitHub i aktualizowania.
    -  Jeśli najnowszą wersję agenta dla Twojej dystrybucji nie jest obsługiwana dystrybucja dostępnej, skontaktuj się z pomocą instrukcje dotyczące sposobu jego instalacji. 

#### <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji 

1. Uruchom następujące polecenie: **ps -e** aby upewnić się, że Azure agent jest uruchomiony na maszynie Wirtualnej systemu Linux.
2. Jeśli proces nie jest uruchomiona, uruchom go ponownie przy użyciu następujących poleceń:
    - Dla systemu Ubuntu: **usługi walinuxagent start**
    - Dla innych dystrybucji: **usługi waagent start**


## <a name="uninstall-the-mobility-service"></a>Odinstaluj usługę mobilności

1. Ręcznie odinstaluj usługę mobilności z maszyny Wirtualnej platformy Azure, przy użyciu jednej z następujących metod. 
    - Dla Windows, w Panelu sterowania > **Dodaj/Usuń programy**, odinstaluj **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy**. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Dla systemu Linux Zaloguj się jako użytkownik root. W terminalu przejdź do **/user/local/ASR**, i uruchom następujące polecenie:
        ```
        ./uninstall.sh -Y
        ```
2. Uruchom ponownie maszynę Wirtualną, przed skonfigurowaniem replikacji.

## <a name="next-steps"></a>Kolejne kroki

[Rozwiązywanie problemów z przeglądu](site-recovery-extension-troubleshoot.md) dla rozszerzenie usługi Site Recovery na agencie maszyny Wirtualnej platformy Azure.
[Szybkie replikowanie](azure-to-azure-quickstart.md) Maszynie wirtualnej platformy Azure do regionu pomocniczego.
