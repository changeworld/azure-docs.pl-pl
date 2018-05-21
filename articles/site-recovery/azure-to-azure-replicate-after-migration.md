---
title: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych Azure po zakończeniu migracji do platformy Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania maszyny, aby skonfigurować odzyskiwania po awarii między regiony platformy Azure po zakończeniu migracji na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: ponatara
ms.openlocfilehash: f64d61134f61476bcdd803f92019830b7cecf867
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych Azure po zakończeniu migracji na platformie Azure 

>[!NOTE]
> Odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery jest obecnie w przeglądzie.

Użyj w tym artykule, po wprowadzeniu [zmigrowane maszyny lokalnymi maszynami wirtualnymi Azure](tutorial-migrate-on-premises-to-azure.md) przy użyciu [usługi Site Recovery](site-recovery-overview.md) usługi. Ten artykuł pomaga przygotować maszyn wirtualnych platformy Azure do konfigurowania odzyskiwania po awarii do dodatkowej regionu Azure, przy użyciu usługi Site Recovery.



## <a name="before-you-start"></a>Przed rozpoczęciem

Przed skonfigurowaniem odzyskiwania po awarii, upewnij się, że migracja została zakończona, zgodnie z oczekiwaniami. Pomyślnie migracji, po przejściu w tryb failover, należy wybrać **przeprowadzenia migracji** opcji dla każdego komputera, które chcesz migrować. 



## <a name="install-the-azure-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej Azure

Azure [agenta maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) musi być zainstalowany na Maszynie wirtualnej, dzięki czemu usługa Site Recovery może replikować go.


1. Aby zainstalować agenta maszyny Wirtualnej na maszynach wirtualnych z systemem Windows, Pobierz i uruchom [Instalatora agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Potrzebne są uprawnienia administratora na maszynie Wirtualnej, aby zakończyć instalację.
2. Aby zainstalować agenta maszyny Wirtualnej na maszynach wirtualnych z systemem Linux, zainstaluj najnowszą [agenta systemu Linux](../virtual-machines/extensions/agent-linux.md). Potrzebne są uprawnienia administratora, aby zakończyć instalację. Zaleca się instalowania z repozytorium dystrybucji. Nie zaleca się zainstalowanie agenta maszyny Wirtualnej systemu Linux bezpośrednio z usługi GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Sprawdź poprawność instalacji na maszynach wirtualnych systemu Windows

1. Na maszynie Wirtualnej Azure, w folderze C:\WindowsAzure\Packages należy znaleźć w pliku WaAppAgent.exe.
2. Kliknij prawym przyciskiem myszy plik, a następnie w **właściwości**, wybierz pozycję **szczegóły** kartę.
3. Sprawdź, czy **wersji produktu** pola pokazuje 2.6.1198.718 lub nowszej.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migracja z maszyn wirtualnych VMware lub serwerów fizycznych

W przypadku migrowania maszyn wirtualnych VMware lokalnie (lub serwerów fizycznych) na platformie Azure, należy pamiętać, że:

- Musisz zainstalować agenta maszyny Wirtualnej platformy Azure, jeśli zainstalowana na zmigrowanej maszynie usługa mobilności jest v9.6 lub wcześniej.
- Na maszynach wirtualnych systemu Windows jest uruchomiona wersja 9.7.0.0 usługi mobilności i jego nowszych wersjach Instalatora usługi instaluje najnowsze dostępne Azure agenta maszyny Wirtualnej. Podczas migracji, te maszyny wirtualne spełnia już wymagań wstępnych dla dowolnego rozszerzenia maszyny Wirtualnej, w tym rozszerzenia usługi Site Recovery instalacji agenta.
- Należy ręcznie odinstalować usługi mobilności z maszyny Wirtualnej Azure, przy użyciu jednej z poniższych metod. Przed skonfigurowaniem replikacji, należy ponownie uruchomić maszyny Wirtualnej.
    - Dla systemu Windows w Panelu sterowania > **Dodaj lub usuń programy**, odinstaluj **Microsoft Azure Site odzyskiwania mobilności usługi/główny serwer docelowy**. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux, po zalogowaniu użytkownika głównego. W terminalu, przejdź do **/user/local/ASR**, i uruchom następujące polecenie:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Kolejne kroki

[Szybko replikować](azure-to-azure-quickstart.md) maszyny Wirtualnej platformy Azure w regionie pomocniczym.
