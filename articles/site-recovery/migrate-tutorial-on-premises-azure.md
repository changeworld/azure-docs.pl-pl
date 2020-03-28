---
title: Migrowanie komputerów lokalnych za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 24015810a295ef88b7d3e63bfc464ddddef6b55f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73939634"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrowanie maszyn lokalnych do platformy Azure


W tym artykule opisano sposób migracji komputerów lokalnych na platformę Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md) Ogólnie rzecz biorąc usługa Odzysk lokacji służy do zarządzania odzyskiwaniem po awarii na komputerach lokalnych i maszynach wirtualnych platformy Azure i organizowania odzyskiwania po awarii. Jednak może być również używany do migracji. Migracja używa tych samych kroków co odzyskiwanie po awarii z jednym wyjątkiem. W przypadku migracji niepowodzenie maszyn z lokacji lokalnej jest ostatnim krokiem. W przeciwieństwie do odzyskiwania po awarii nie można zakończyć się niepowodzeniem z powrotem do lokalnego w scenariuszu migracji.


Ten samouczek pokazuje, jak przeprowadzić migrację lokalnych maszyn wirtualnych i serwerów fizycznych na platformę Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie środowiska źródłowego i docelowego dla migracji
> * Konfigurowanie zasad replikacji
> * Włączanie replikacji
> * Uruchamianie testowania migracji w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami
> * Uruchamianie jednokrotnego przejścia w tryb failover na platformie Azure


> [!TIP]
> Teraz można migrować serwery lokalne na platformę Azure przy użyciu usługi Azure Migrate. [Dowiedz się więcej](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

Należy pamiętać, że urządzenia eksportowane przez parawirtualizowane sterowniki nie są obsługiwane.


## <a name="prepare-azure-and-on-premises"></a>Przygotowywanie platformy Azure i lokalnie

1. Przygotuj platformę Azure zgodnie z opisem w [tym artykule](tutorial-prepare-azure.md). Chociaż w tym artykule opisano kroki przygotowania do odzyskiwania po awarii, kroki są również prawidłowe dla migracji.
2. Przygotuj lokalne serwery [programu VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md). Jeśli migrujesz fizyczne maszyny, nie musisz niczego przygotowywać. Wystarczy sprawdzić [matrycę wsparcia](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.
1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu Zasobów kliknij polecenie Przygotowanie**środowiska do** **odzyskiwania** > **witryny** > .
3. W obszarze **Cel ochrony** wybierz elementy do migracji.
    - **VMware**: wybierz kolejno pozycje **Na platformę Azure** > **Tak, przy użyciu funkcji VMWare vSphere Hypervisor**.
    - **Komputer fizyczny**: wybierz kolejno pozycje **Na platformę Azure** > **Niezwirtualizowane/inne**.
    - **Hyper-V**: wybierz kolejno pozycje **Azure** > **Tak, przy użyciu funkcji Hyper-V**. Jeśli program VMM zarządza maszynami wirtualnymi funkcji Hyper-V, wybierz pozycję **Tak**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | Skonfiguruj [środowisko źródłowe](vmware-azure-set-up-source.md)i skonfiguruj [serwer konfiguracji](vmware-azure-deploy-configuration-server.md).
Maszyna fizyczna | [Skonfiguruj](physical-azure-set-up-source.md) środowisko źródłowe i serwer konfiguracji.
Funkcja Hyper-V | Konfigurowanie [środowiska źródłowego](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Skonfiguruj [środowisko źródłowe](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) dla funkcji Hyper-V wdrożonej za pomocą programu System Center VMM.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij **pozycję Przygotuj** > **obiekt docelowy**infrastruktury i wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ model wdrażania usługi Resource Manager.
3. Usługa Site Recovery sprawdza zasoby platformy Azure.
    - Jeśli przeprowadzasz migrację maszyn wirtualnych VMware lub serwerów fizycznych, usługa Site Recovery sprawdza, czy masz sieć platformy Azure, w której będą znajdować się maszyny wirtualne platformy Azure, gdy zostaną utworzone po przełączeniu w tryb failover.
    - Jeśli przeprowadzasz migrację maszyn wirtualnych z funkcją Hyper-V, usługa Site Recovery sprawdza, czy masz zgodne konto magazynu platformy Azure i sieć.
4. Jeśli przeprowadzasz migrację maszyn wirtualnych z programem Hyper-V zarządzanych przez program System Center VMM, [skonfiguruj mapowanie sieci](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | Konfigurowanie [zasad replikacji](vmware-azure-set-up-replication.md) maszyn wirtualnych VMware.
Maszyna fizyczna | Konfigurowanie [zasad replikacji](physical-azure-disaster-recovery.md#create-a-replication-policy) dla komputerów fizycznych.
Funkcja Hyper-V | Konfigurowanie [zasad replikacji](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Konfigurowanie [zasad replikacji](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) funkcji Hyper-V wdrożonych za pomocą programu System Center VMM.

## <a name="enable-replication"></a>Włączanie replikacji

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | [Włącz replikację](vmware-azure-enable-replication.md) dla maszyn wirtualnych oprogramowania VMware.
Maszyna fizyczna | [Włącz replikację](physical-azure-disaster-recovery.md#enable-replication) dla komputerów fizycznych.
Funkcja Hyper-V | [Włącz replikację](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Włącz replikację](hyper-v-vmm-azure-tutorial.md#enable-replication) funkcji Hyper-V wdrożonej za pomocą programu System Center VMM.


## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Uruchom [testowanie trybu failover](tutorial-dr-drill-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom tryb failover dla maszyn, które chcesz migrować.

1. W **ustawieniach** > **replikowane elementy** kliknij komputer > pracy **awaryjnej**.
2. W **obszarze Przewijanie awaryjne** wybierz **punkt odzyskiwania,** do który chcesz awaryjnie. Wybierz najnowszy punkt odzyskiwania.
3. Ustawienie klucza szyfrowania nie ma znaczenia w przypadku tego scenariusza.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje zamknąć maszyny wirtualne przed przejściem do trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
5. Sprawdź, czy maszyna wirtualna Azure jest wyświetlana na platformie Azure zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zakończ migrację**. Spowoduje to wykonanie następujących czynności:

   - Kończy proces migracji, zatrzymuje replikację lokalnej maszyny Wirtualnej i zatrzymuje rozliczenia usługi Site Recovery dla maszyny Wirtualnej.
   - W tym kroku oczyszczane są dane replikacji. Nie są jednak usuwane migrowane maszyny wirtualne.

     ![Kończenie migracji](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: replikacja maszyny wirtualnej zostanie zatrzymana przed uruchomieniem trybu failover. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Można zauważyć dłuższe czasy pracy awaryjnej testów dla serwerów fizycznych, maszyn VMware z systemem Linux, maszyn VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Po migracji

Po zmigrowaniu maszyn do platformy Azure należy wykonać kilka czynności.

Niektóre czynności można zautomatyzować w ramach procesu migracji przy użyciu wbudowanej funkcji skryptów automatyzacji w [planach odzyskiwania](site-recovery-runbook-automation.md).   


### <a name="post-migration-steps-in-azure"></a>Czynności wykonywane na platformie Azure po migracji

- Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web. 
- Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
- [Agent maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) umożliwia zarządzanie interakcją maszyny wirtualnej z usługą Azure Fabric Controller. Jest to wymagane w przypadku niektórych usług platformy Azure, takich jak Azure Backup, Site Recovery i Azure Security.
    - Podczas migrowania maszyn VMware i serwerów fizycznych razem z usługą mobilności na maszynach z systemem Windows jest instalowany dostępny agent maszyny wirtualnej platformy Azure. Na maszynach wirtualnych z systemem Linux zaleca się zainstalowanie agenta po włączeniu trybu failover.
    - Podczas migrowania maszyn wirtualnych platformy Azure do regionu pomocniczego agenta maszyny Wirtualnej platformy Azure należy ustanowić na maszynie wirtualnej przed migracją.
    - Podczas migrowania maszyn wirtualnych funkcji Hyper-V do platformy Azure agenta maszyny wirtualnej platformy Azure na maszynie wirtualnej platformy Azure instaluje się po zakończeniu migracji.
- Usuń ręcznie z maszyny wirtualnej wszelkie programy typu dostawca/agent usługi Site Recovery. W przypadku migracji maszyn wirtualnych lub serwerów fizycznych należy odinstalować usługę mobilności z maszyny Wirtualnej.
- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](azure-to-azure-quickstart.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i ogranicz dostęp do ruchu przychodzącego za pomocą funkcji [administrowania na czas]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) usługi Azure Security Center.
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.

### <a name="post-migration-steps-on-premises"></a>Czynności wykonywane lokalnie po migracji

- Przenieś ruch aplikacji do aplikacji uruchomionej na zmigrowanym wystąpieniu maszyny wirtualnej platformy Azure.
- Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
- Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
- Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.




## <a name="next-steps"></a>Następne kroki

W tym samouczku przeprowadzono migrację lokalnych maszyn wirtualnych do maszyn wirtualnych platformy Azure. Now

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii](azure-to-azure-replicate-after-migration.md) w pomocniczym regionie platformy Azure dla maszyn wirtualnych platformy Azure.

  
