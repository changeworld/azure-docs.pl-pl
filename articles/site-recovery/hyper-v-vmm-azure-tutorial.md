---
title: Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V w chmurach VMM do platformy Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V w chmurach programu System Center VMM do platformy Azure, z usługą Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8bbbe0a5aca20222ff7385be9d0ecf0a4224d5c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896150"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V w chmurach VMM do platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

Ten samouczek przedstawia sposób konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper V na platformie Azure. Samouczek ma zastosowanie w przypadku maszyn wirtualnych funkcji Hyper-V, które są zarządzane przez System Center Virtual Machine Manager (VMM). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji wraz z lokalnymi składnikami usługi Site Recovery oraz środowiska docelowego replikacji.
> * Skonfigurowanie mapowania sieci, do mapowania sieci maszyny Wirtualnej VMM i sieci wirtualnych platformy Azure.
> * Tworzenie zasad replikacji
> * Włączanie replikacji maszyny wirtualnej

Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Przed rozpoczęciem warto [zapoznać się z architekturą](concepts-hyper-v-to-azure-architecture.md) tego scenariusza odzyskiwania po awarii.



## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W **wszystkie usługi** > **Magazyny usług odzyskiwania**, kliknij nazwę magazynu używamy w tych samouczkach **ContosoVMVault**.
2. W obszarze **Wprowadzenie** kliknij pozycję **Site Recovery**. Następnie kliknij pozycję **Przygotowywanie infrastruktury**
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W **są maszynach zwirtualizowanych**, wybierz pozycję **tak, z funkcją Hyper-V**.
6. W **korzystasz z programu System Center VMM**, wybierz pozycję **tak**. Następnie kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Po skonfigurowaniu środowiska źródłowego Zainstaluj dostawcę usługi Azure Site Recovery i agent usług odzyskiwania Azure i zarejestrować lokalnych serwerów w magazynie. 

1. W obszarze **Przygotowanie infrastruktury**, kliknij przycisk **Źródło**.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ VMM**, aby dodać serwer programu VMM. W **Dodaj serwer**, sprawdź, czy **serwera programu System Center VMM** pojawia się w **typ serwera**.
3. Pobierz Instalator dostawcy usługi Microsoft Azure Site Recovery.
4. Pobierz klucz rejestracji magazynu. Będzie potrzebny podczas instalacji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.
5. Pobierz agenta usług odzyskiwania.

    ![Do pobrania](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalowanie dostawcy na serwerze VMM

1. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
2. W **instalacji**, zaakceptuj domyślną lokalizację instalacji dostawcy i kliknij przycisk **zainstalować**. 
3. Po zakończeniu instalacji w Kreator rejestracji Microsoft Azure Site Recovery > **ustawienia magazynu**, kliknij przycisk **Przeglądaj**i w **plik klucza**, wybierz plik klucza magazynu, który pobrana.
4. Określ subskrypcję usługi Azure Site Recovery oraz nazwę magazynu (**ContosoVMVault**). Określ przyjazną nazwę dla serwera VMM, aby zidentyfikować go w magazynie.
5. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
6. Zaakceptuj lokalizację domyślną dla certyfikatu, który jest używany do szyfrowania danych. Zaszyfrowane dane zostaną odszyfrowane, podczas przejścia w tryb failover.
7. W **Synchronizuj metadane chmury**, wybierz pozycję **Synchronizuj metadane chmury do portalu usługi Site Recovery**. To działanie ma miejsce tylko raz na każdym serwerze. Następnie kliknij przycisk **zarejestrować**.
8. Po serwer jest zarejestrowany w magazynie, kliknij przycisk **Zakończ**.

Po zakończeniu rejestracji, metadane z serwera są pobierane przez usługę Azure Site Recovery, a serwer programu VMM są wyświetlane w **infrastruktura usługi Site Recovery**.

### <a name="install-the-recovery-services-agent"></a>Zainstaluj agenta usług odzyskiwania

Zainstaluj agenta na każdym hoście funkcji Hyper-V zawierające maszyny wirtualne mają być replikowane.

1. W Kreatorze instalacji agenta usług odzyskiwania Microsoft Azure > **Sprawdzanie wymagań wstępnych**, kliknij przycisk **dalej**. Wszystkie brakujące wymagania wstępne zostaną zainstalowane automatycznie.
2. W **ustawienia instalacji**, zaakceptuj lokalizację instalacji i lokalizację pamięci podręcznej. Pamięć podręczna potrzebuje co najmniej 5 GB przestrzeni dyskowej. Firma Microsoft zaleca dysku z najmniej 600 GB wolnego miejsca. Następnie kliknij pozycję **Zainstaluj**.
3. W **instalacji**, po zakończeniu instalacji, kliknij przycisk **Zamknij** aby zakończyć pracę kreatora.

    ![Zainstaluj agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcji i grupy zasobów (**ContosoRG**), w której maszyny wirtualne Azure zostanie utworzony po pracy awaryjnej.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. W obszarze **Infrastruktura usługi Site Recovery** > **Mapowania sieci** > **Mapowanie sieci** kliknij ikonę **+ Mapowanie sieci**.
2. W **Dodaj mapowanie sieci**, wybierz źródłowy serwer programu VMM. Wybierz **Azure** jako element docelowy.
3. Sprawdź subskrypcję i model wdrożenia po przejściu do trybu failover.
4. W **Sieć źródłowa**, wybierz sieć źródłową lokalnej maszyny Wirtualnej.
5. W **Sieć docelowa**, wybierz sieć platformy Azure, w których repliki maszyn wirtualnych Azure zostaną umieszczone podczas są tworzone po pracy awaryjnej. Następnie kliknij przycisk **OK**.

    ![Mapowanie sieci](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Tworzenie i kojarzenie zasad** podaj nazwę zasad: **ContosoReplicationPolicy**.
3. Pozostaw ustawienia domyślne i kliknij przycisk **OK**.
    - Ustawienie **Częstotliwość kopiowania** wskazuje, że dane różnicowe (po replikacji początkowej) będą replikowane co pięć minut.
    - Ustawienie **Przechowywanie punktów odzyskiwania** wskazuje, że okna przechowywania dla każdego punktu odzyskiwania będą wynosiły dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - Ustawienie **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
    - **Szyfruj dane przechowywane na platformie Azure** — wartość domyślna **poza** ustawienie wskazuje, że przechowywanych danych na platformie Azure nie jest zaszyfrowany.
4. Po utworzeniu zasad kliknij przycisk **OK**. Jeśli utworzysz nowe zasady, zostaną one automatycznie skojarzone z chmurą VMM.

## <a name="enable-replication"></a>Włączanie replikacji

1. W obszarze **Replikowanie aplikacji** kliknij przycisk **Źródło**. 
2. W **źródła**, wybierz chmurę VMM. Następnie kliknij przycisk **OK**.
3. W **docelowej**, sprawdź Azure jako element docelowy subskrypcji magazynu i wybierz **Resource Manager** modelu.
4. Wybierz **contosovmsacct1910171607** konta magazynu i **ContosoASRnet** sieć platformy Azure.
5. W obszarze **Maszyny wirtualne** > **Wybierz** wybierz maszynę wirtualną, którą chcesz replikować. Następnie kliknij przycisk **OK**.

 Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po **zakończenia ochrony** zadanie zostało ukończone, Replikacja początkowa została zakończona i maszyna wirtualna jest gotowa do pracy awaryjnej.


## <a name="next-steps"></a>Kolejne kroki
[Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
