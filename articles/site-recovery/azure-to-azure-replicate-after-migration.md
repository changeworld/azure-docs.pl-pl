---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure po migracji na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przygotować maszyny, aby skonfigurować odzyskiwanie po awarii między regionami platformy Azure po migracji na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 274a69c6a2c23caf391a636ce53a9bb3897c0aa2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836070"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych platformy Azure po migracji na platformę Azure 


Skorzystaj z tego artykułu po [migrację maszyn lokalnych do maszyn wirtualnych platformy Azure](tutorial-migrate-on-premises-to-azure.md) przy użyciu [Site Recovery](site-recovery-overview.md) usługi. Ten artykuł pomaga przygotować maszyny wirtualne platformy Azure do konfigurowania odzyskiwania po awarii do regionu pomocniczego platformy Azure, za pomocą Site Recovery.



## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem konfigurowania odzyskiwania po awarii, upewnij się, że migracja została zakończona, zgodnie z oczekiwaniami. Pomyślnie migracji, po włączeniu trybu failover, należy wybrać **kończenia migracji** opcji dla każdej maszyny, które mają zostać zmigrowane. 



## <a name="install-the-azure-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej platformy Azure

Azure [agenta maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) musi być zainstalowany na maszynie Wirtualnej, dzięki czemu usługa Site Recovery może replikować go.


1. Aby zainstalować agenta maszyny Wirtualnej na maszynach wirtualnych z systemem Windows, Pobierz i uruchom [Instalatora agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Niezbędne są uprawnienia administratora na maszynie Wirtualnej, aby ukończyć instalację.
2. Aby zainstalować agenta maszyny Wirtualnej na maszynach wirtualnych z systemem Linux, zainstaluj najnowszą wersję [agenta systemu Linux](../virtual-machines/extensions/agent-linux.md). Niezbędne są uprawnienia administratora, aby ukończyć instalację. Zaleca się, że zainstalować ze swojego repozytorium dystrybucji. Nie zaleca się zainstalowanie agenta maszyny Wirtualnej systemu Linux bezpośrednio z serwisu GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Zweryfikuj instalację na maszynach wirtualnych Windows

1. Na maszynie Wirtualnej platformy Azure, w folderze C:\WindowsAzure\Packages powinny być widoczne w nim plik WaAppAgent.exe.
2. Kliknij prawym przyciskiem myszy plik, a następnie w **właściwości**, wybierz opcję **szczegóły** kartę.
3. Upewnij się, że **wersji produktu** pola pokazuje się wartość 2.6.1198.718 lub wyższa.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migracja z maszyn wirtualnych VMware lub serwery fizyczne

W przypadku migrowania lokalnych maszyn wirtualnych z programu VMware (lub serwery fizyczne) do platformy Azure należy pamiętać, że:

- Musisz zainstalować agenta maszyny Wirtualnej platformy Azure, jeśli zainstalowana na zmigrowanej maszynie usługa mobilności jest v9.6 lub wcześniej.
- Na maszynach wirtualnych Windows lub nowszym systemem 9.7.0.0 wersję usługi mobilności Instalatora usługi instaluje najnowsze dostępne maszyny Wirtualnej platformy Azure agenta. Podczas migracji, te maszyny wirtualne spełnia już wymagania wstępne dla wszelkich rozszerzeń maszyny Wirtualnej, w tym rozszerzenie usługi Site Recovery instalacji agenta.
- Należy ręcznie odinstalować usługę mobilności z maszyny Wirtualnej platformy Azure, przy użyciu jednej z następujących metod. Uruchom ponownie maszynę Wirtualną, przed skonfigurowaniem replikacji.
    - Dla Windows, w Panelu sterowania > **Dodaj/Usuń programy**, odinstaluj **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy**. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Dla systemu Linux Zaloguj się jako użytkownik root. W terminalu przejdź do **/user/local/ASR**, i uruchom następujące polecenie:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Kolejne kroki

[Szybkie replikowanie](azure-to-azure-quickstart.md) Maszynie wirtualnej platformy Azure do regionu pomocniczego.
