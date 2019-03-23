---
title: Migrowanie maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 31d08c0dac63662568bf55a021e85ec414c61e52
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360371"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrowanie maszyn lokalnych do platformy Azure

Oprócz używania usługi [Azure Site Recovery](site-recovery-overview.md) do zarządzania odzyskiwaniem maszyn lokalnych i maszyn wirtualnych platformy Azure po awarii i ich organizowania dla celów zapewniania ciągłości działania i odzyskiwania po awarii (BCDR, business continuity and disaster recovery) można ją stosować również do zarządzania migracją maszyn lokalnych na platformę Azure.


Ten samouczek pokazuje, jak przeprowadzić migrację lokalnych maszyn wirtualnych i serwerów fizycznych na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie celu replikacji
> * Konfigurowanie środowiska źródłowego i docelowego
> * Konfigurowanie zasad replikacji
> * Włączanie replikacji
> * Uruchamianie testowania migracji w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami
> * Uruchamianie jednokrotnego przejścia w tryb failover na platformie Azure

Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. Przygotuj lokalne serwery [programu VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md).

Przed rozpoczęciem warto zapoznać się z architekturami [VMware](vmware-azure-architecture.md) lub [Hyper-V](hyper-v-azure-architecture.md) na potrzeby odzyskiwania po awarii.

> [!TIP]
> Czy chcesz uczestniczyć w naszego nowego środowiska bez wykorzystania agentów dla migracji maszyn wirtualnych VMware na platformę Azure? [Więcej informacji można znaleźć](https://aka.ms/migrateVMs-signup).

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Narzędzia do zarządzania** > **Backup i Site Recovery**.
3. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, kliknij pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

   ![Nowy magazyn](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.


## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.
1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu zasobów kliknij pozycję **Site Recovery** > **Przygotowanie infrastruktury** > **Cel ochrony**.
3. W obszarze **Cel ochrony** wybierz elementy do migracji.
    - **VMware**: wybierz kolejno pozycje **Na platformę Azure** > **Tak, przy użyciu funkcji VMWare vSphere Hypervisor**.
    - **Maszyna fizyczna**: wybierz kolejno pozycje **Na platformę Azure** > **Niezwirtualizowane/inne**.
    - **Hyper-V**: wybierz kolejno pozycje **Na platformę Azure** > **Tak, przy użyciu funkcji Hyper-V**. Jeśli program VMM zarządza maszynami wirtualnymi funkcji Hyper-V, wybierz pozycję **Tak**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

- [Skonfiguruj](vmware-azure-tutorial.md#set-up-the-source-environment) środowisko źródłowe dla maszyn wirtualnych oprogramowania VMware.
- [Skonfiguruj](physical-azure-disaster-recovery.md#set-up-the-source-environment) środowisko źródłowe dla serwerów fizycznych.
- [Skonfiguruj](hyper-v-azure-tutorial.md#set-up-the-source-environment) środowisko źródłowe dla maszyn wirtualnych funkcji Hyper-V.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ model wdrażania usługi Resource Manager.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

- [Skonfiguruj zasady replikacji](vmware-azure-tutorial.md#create-a-replication-policy) dla maszyn wirtualnych oprogramowania VMware.
- [Skonfiguruj zasady replikacji](physical-azure-disaster-recovery.md#create-a-replication-policy) dla serwerów fizycznych.
- [Skonfiguruj zasady replikacji](hyper-v-azure-tutorial.md#set-up-a-replication-policy) dla maszyn wirtualnych funkcji Hyper-V.


## <a name="enable-replication"></a>Włączanie replikacji

- [Włącz replikację](vmware-azure-tutorial.md#enable-replication) dla maszyn wirtualnych oprogramowania VMware.
- [Włącz replikację](physical-azure-disaster-recovery.md#enable-replication) dla serwerów fizycznych.
- Włącz replikację dla maszyn wirtualnych funkcji Hyper-V [z programem VMM](hyper-v-vmm-azure-tutorial.md#enable-replication) lub [bez programu VMM](hyper-v-azure-tutorial.md#enable-replication).


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

W tym samouczku przeprowadzono migrację lokalnych maszyn wirtualnych do maszyn wirtualnych platformy Azure. Teraz można [skonfigurować odzyskiwanie po awarii](azure-to-azure-replicate-after-migration.md) w regionie pomocniczym platformy Azure dla maszyn wirtualnych platformy Azure.

  
