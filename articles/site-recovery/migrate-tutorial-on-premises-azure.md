---
title: Migrowanie maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: bc04483c35162c0b461fd03c63aaa894b1bc199a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070681"
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
2. Przygotowywanie lokalnych serwerów [VMware](vmware-azure-tutorial-prepare-on-premises.md) lub serwerów funkcji [Hyper-V] (hyper-v-prepare-on-premises-tutorial.md).

Przed rozpoczęciem warto zapoznać się z architekturami [VMware](vmware-azure-architecture.md) lub [Hyper-V](hyper-v-azure-architecture.md) na potrzeby odzyskiwania po awarii.


## <a name="prerequisites"></a>Wymagania wstępne

- Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
 
> [!WARNING]
> Istnieje możliwość migrowania maszyn wirtualnych na inne (niż VMware i Hyper-V) platformy wirtualizacji, na przykład XenServer, poprzez traktowanie maszyn wirtualnych jak serwerów fizycznych. Ta metoda nie została jednak zbadana i zwalidowana przez firmę Microsoft i może nie działać. Na przykład maszyna wirtualna uruchomiona na platformie XenServer może nie działać na platformie Azure, chyba że narzędzia platformy XenServer i magazyn para-zwirtualizowany oraz sterowniki sieci zostaną odinstalowane z maszyny wirtualnej przed rozpoczęciem migracji.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Monitorowanie i zarządzanie** > **Backup and Site Recovery**.
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
    - **Komputer fizyczny**: wybierz kolejno pozycje **Na platformę Azure** > **Niezwirtualizowane/inne**.
    - **Hyper-V**: wybierz kolejno pozycje **Azure** > **Tak, przy użyciu funkcji Hyper-V**. Jeśli program VMM zarządza maszynami wirtualnymi funkcji Hyper-V, wybierz pozycję **Tak**.


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
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zakończ migrację**. Spowoduje to zakończenie procesu migracji, zatrzymanie replikacji maszyny wirtualnej oraz zatrzymanie naliczania opłat za usługę Site Recovery dla maszyny wirtualnej.

    ![Kończenie migracji](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: replikacja maszyny wirtualnej zostanie zatrzymana przed uruchomieniem trybu failover. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testu trybu failover może występować w przypadku serwerów fizycznych; maszyn VMware z systemem Linux; maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przeprowadzono migrację lokalnych maszyn wirtualnych do maszyn wirtualnych platformy Azure. Teraz po pomyślnej migracji maszyn wirtualnych możesz wykonać następujące czynności:
- [Set up disaster recovery (Konfigurowanie odzyskiwania po awarii)](azure-to-azure-replicate-after-migration.md) dla migrowanych maszyn wirtualnych.
- Skorzystaj z możliwości, jakie daje [bezpieczna i dobrze zarządzana usługa w chmurze](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), aby zarządzać maszynami wirtualnymi na platformie Azure.
  
