---
title: Migrowanie maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fc15db91b8f4cc6dbdecd0e7321abdbf81744f08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193907"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrowanie maszyn lokalnych do platformy Azure


W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). Ogólnie rzecz biorąc Usługa Site Recovery jest używany do zarządzania i Organizuj odzyskiwanie po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure. Jednak może również służyć do migracji. Migracja używa te same czynności co odzyskiwania po awarii z jednym wyjątkiem. W przypadku migracji przechodzenie maszyn w tryb failover z lokacji lokalnej jest to ostatni krok. W przeciwieństwie do odzyskiwania po awarii nie powrotu po awarii do środowiska lokalnego w scenariuszu migracji.


Ten samouczek pokazuje, jak przeprowadzić migrację lokalnych maszyn wirtualnych i serwerów fizycznych na platformę Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie środowiska źródłowego i docelowego do migracji
> * Konfigurowanie zasad replikacji
> * Włączanie replikacji
> * Uruchamianie testowania migracji w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami
> * Uruchamianie jednokrotnego przejścia w tryb failover na platformie Azure


> [!TIP]
> Usługa Azure Migrate oferuje obecnie nowe środowisko bez agenta w wersji zapoznawczej dla migracji maszyn wirtualnych VMware na platformę Azure. [Więcej informacji można znaleźć](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>Przed rozpoczęciem

Należy pamiętać, że urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.


## <a name="prepare-azure-and-on-premises"></a>Przygotowywanie platformy Azure i w środowisku lokalnym

1. Przygotowywanie platformy Azure, zgodnie z opisem w [w tym artykule](tutorial-prepare-azure.md). Mimo że w tym artykule opisano kroki przygotowania do odzyskiwania po awarii, kroki również są prawidłowe dla migracji.
2. Przygotuj lokalne serwery [programu VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md). W przypadku migrowania maszyn fizycznych, nie ma potrzeby przygotowawczych. Sprawdź, po prostu [macierz obsługi](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.
1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu zasobów kliknij pozycję **Site Recovery** > **Przygotowanie infrastruktury** > **Cel ochrony**.
3. W obszarze **Cel ochrony** wybierz elementy do migracji.
    - **VMware**: wybierz kolejno pozycje **Na platformę Azure** > **Tak, przy użyciu funkcji VMWare vSphere Hypervisor**.
    - **Maszyna fizyczna**: wybierz kolejno pozycje **Na platformę Azure** > **Niezwirtualizowane/inne**.
    - **Hyper-V**: wybierz kolejno pozycje **Na platformę Azure** > **Tak, przy użyciu funkcji Hyper-V**. Jeśli program VMM zarządza maszynami wirtualnymi funkcji Hyper-V, wybierz pozycję **Tak**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | Konfigurowanie [środowisko źródłowe](vmware-azure-set-up-source.md)i skonfiguruj [serwera konfiguracji](vmware-azure-deploy-configuration-server.md).
Maszyna fizyczna | [Konfigurowanie](physical-azure-set-up-source.md) środowiska i konfiguracji serwera źródłowego.
Funkcja Hyper-V | Konfigurowanie [środowiska źródłowego](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Konfigurowanie [środowisko źródłowe](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) funkcji Hyper-v wdrożone za pomocą programu System Center VMM.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ model wdrażania usługi Resource Manager.
3. Usługa Site Recovery sprawdza zasobów platformy Azure.
    - W przypadku migrowania maszyn wirtualnych VMware lub serwery fizyczne, Usługa Site Recovery sprawdza, czy masz siecią platformy Azure, w której maszyny wirtualne Azure zostaną umieszczone po ich utworzeniu po włączeniu trybu failover.
    - W przypadku migrowania maszyn wirtualnych funkcji Hyper-V, Usługa Site Recovery sprawdza, czy masz konto magazynu platformy Azure zgodny i sieci.
4. W przypadku migrowania maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center VMM, skonfigurować [mapowania sieci](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | Konfigurowanie [zasad replikacji](vmware-azure-set-up-replication.md) dla maszyn wirtualnych VMware.
Maszyna fizyczna | Konfigurowanie [zasad replikacji](physical-azure-disaster-recovery.md#create-a-replication-policy) maszyn fizycznych.
Funkcja Hyper-V | Konfigurowanie [zasad replikacji](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Konfigurowanie [zasad replikacji](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) funkcji Hyper-v wdrożone za pomocą programu System Center VMM.

## <a name="enable-replication"></a>Włączanie replikacji

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | [Włącz replikację](vmware-azure-enable-replication.md) dla maszyn wirtualnych oprogramowania VMware.
Maszyna fizyczna | [Włącz replikację](physical-azure-disaster-recovery.md#enable-replication) maszyn fizycznych.
Funkcja Hyper-V | [Włączanie replikacji](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Włącz replikację](hyper-v-vmm-azure-tutorial.md#enable-replication) funkcji Hyper-v wdrożone za pomocą programu System Center VMM.


## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Uruchom [testowanie trybu failover](tutorial-dr-drill-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom tryb failover dla maszyn, które chcesz migrować.

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Wybierz najnowszy punkt odzyskiwania.
3. Ustawienie klucza szyfrowania nie ma znaczenia w przypadku tego scenariusza.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje zamknąć maszyny wirtualne przed przejściem do trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
5. Sprawdź, czy maszyna wirtualna Azure jest wyświetlana na platformie Azure zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zakończ migrację**. Spowoduje to wykonanie następujących czynności:

   - Zakończenie procesu migracji, zatrzymanie replikacji maszyny Wirtualnej w środowisku lokalnym i zatrzymanie naliczania opłat za Site Recovery dla maszyny Wirtualnej.
   - W tym kroku oczyszczane są dane replikacji. Nie są jednak usuwane migrowane maszyny wirtualne.

     ![Kończenie migracji](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nie anuluj trybu failover, który jest w toku**: Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testu trybu failover może występować w przypadku serwerów fizycznych; maszyn VMware z systemem Linux; maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

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
- Usuń ręcznie z maszyny wirtualnej wszelkie programy typu dostawca/agent usługi Site Recovery. W przypadku migrowania maszyn wirtualnych VMware lub serwery fizyczne, odinstaluj usługę mobilności z maszyny Wirtualnej.
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




## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przeprowadzono migrację lokalnych maszyn wirtualnych do maszyn wirtualnych platformy Azure. Teraz

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii](azure-to-azure-replicate-after-migration.md) do regionu pomocniczego platformy Azure dla maszyn wirtualnych platformy Azure.

  
