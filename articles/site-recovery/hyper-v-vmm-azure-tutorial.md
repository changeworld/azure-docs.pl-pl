---
title: Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V w chmurach programu VMM na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V w chmurach programu System Center VMM do platformy Azure z usługą Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361295"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V w chmurach programu VMM na platformie Azure

W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych funkcji Hyper-V zarządzane przez System Center Virtual Machine Manager (VMM), dla odzyskiwania po awarii na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi. Jeśli używasz programu VMM, następnie [wykonaj kroki tego samouczka](hyper-v-azure-tutorial.md).

Jest to trzeci samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych programu VMware w środowisku lokalnym. W poprzednim samouczku mamy [przygotowany w lokalnym środowisku funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md) do odzyskiwania po awarii na platformie Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji wraz z lokalnymi składnikami usługi Site Recovery oraz środowiska docelowego replikacji.
> * Skonfiguruj mapowanie sieci do mapowania między sieciami maszyny Wirtualnej VMM i sieci wirtualne platformy Azure.
> * Tworzenie zasad replikacji
> * Włączanie replikacji maszyny wirtualnej


> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Szczegółowe informacje na ten temat można znaleźć w artykule w sekcji jak tabeli odzyskiwania lokacji zawartości.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md) to trzeci samouczek z serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:


## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W obszarze **Magazyny usługi Recovery Services** wybierz magazyn. Możemy przygotować magazyn **ContosoVMVault** w poprzednim samouczku.
2. W obszarze **Wprowadzenie** kliknij pozycję **Site Recovery**. Następnie kliknij pozycję **Przygotowywanie infrastruktury**
3. W **cel ochrony** > **których znajdują się maszyny?**, wybierz opcję **On-premises**.
4. W **gdzie chcesz zreplikować maszyny?**, wybierz opcję **na platformę Azure**.
5. W **są maszynach zwirtualizowane?** wybierz **tak, przy użyciu funkcji Hyper-V**.
6. W **używasz programu System Center VMM**, wybierz opcję **tak**. Następnie kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W **planowania wdrożenia**, jeśli planowane jest dużych wdrożeniach, Pobierz planista wdrażania funkcji Hyper-v przy użyciu linku na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrożenia funkcji Hyper-V.
2. Na potrzeby tego samouczka nie potrzebujemy planista wdrażania. W **czy ukończono Planowanie wdrożenia?**, wybierz opcję **zrobię to później**. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Po skonfigurowaniu środowiska źródłowego Zainstaluj dostawcę usługi Azure Site Recovery na serwerze programu VMM i Zarejestruj serwer w magazynie. Możesz zainstalować agenta usług odzyskiwania Azure na każdym hoście funkcji Hyper-V. 

1. W obszarze **Przygotowanie infrastruktury**, kliknij przycisk **Źródło**.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ VMM**, aby dodać serwer programu VMM. W obszarze **Dodaj serwer** sprawdź, czy pozycja **Serwer System Center VMM** pojawia się jako **Typ serwera**.
3. Pobierz Instalator dostawcy Microsoft Azure Site Recovery.
4. Pobierz klucz rejestracji magazynu. Będzie potrzebny podczas instalacji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.
5. Pobierz Instalator agenta usług odzyskiwania Microsoft Azure.

    ![Do pobrania](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalowanie dostawcy na serwerze VMM

1. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
2. W **instalacji**, zaakceptuj domyślną lokalizację instalacji dostawcy i kliknij przycisk **zainstalować**. 
3. Po zakończeniu instalacji w Kreatorze rejestracji usługi Microsoft Azure lokacji odzyskiwania > **ustawienia magazynu**, kliknij przycisk **Przeglądaj**, a następnie w **plik klucza**, wybierz pozycję plik klucza magazynu, który pobrane.
4. Określ subskrypcję usługi Azure Site Recovery oraz nazwę magazynu (**ContosoVMVault**). Określ przyjazną nazwę serwera programu VMM, aby zidentyfikować go w magazynie.
5. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
6. Zaakceptuj domyślną lokalizację dla certyfikatu, który jest używany do szyfrowania danych. Zaszyfrowane dane będą odszyfrowywane po przełączeniu w tryb failover.
7. W **synchronizacja metadanych chmury**, wybierz opcję **Synchronizuj metadane chmury z portalu Site Recovery**. To działanie ma miejsce tylko raz na każdym serwerze. Następnie kliknij przycisk **zarejestrować**.
8. Po zarejestrowaniu serwera w magazynie, kliknij przycisk **Zakończ**.

Po zakończeniu rejestracji metadane z serwera są pobierane przez usługę Azure Site Recovery, a serwer programu VMM są wyświetlane w **infrastruktura usługi Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Zainstaluj agenta usług Recovery Services na hostach funkcji Hyper-V

Zainstaluj agenta na każdym hoście funkcji Hyper-V zawierające maszyny wirtualne mają być replikowane.

1. W Kreatorze instalacji agenta usług odzyskiwania Microsoft Azure > **Sprawdzanie wymagań wstępnych**, kliknij przycisk **dalej**. Brakujące wstępnie wymagane składniki zostaną zainstalowane automatycznie.
2. W **ustawienia instalacji**, zaakceptuj lokalizację instalacji i lokalizację pamięci podręcznej. Dysk pamięci podręcznej wymaga co najmniej 5 GB pamięci masowej. Firma Microsoft zaleca najmniej 600 GB wolnego miejsca na dysku. Następnie kliknij pozycję **Zainstaluj**.
3. W **instalacji**, po zakończeniu instalacji, kliknij przycisk **Zamknij** aby zakończyć działanie kreatora.

    ![Instalowanie agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcję i grupę zasobów (**ContosoRG**), w której maszyny wirtualne platformy Azure zostanie utworzony po włączeniu trybu failover.
3. Wybierz **usługi Resource Manager** modelu wdrażania.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. W obszarze **Infrastruktura usługi Site Recovery** > **Mapowania sieci** > **Mapowanie sieci** kliknij ikonę **+ Mapowanie sieci**.
2. W **Dodawanie mapowania sieci**, wybierz źródłowy serwer programu VMM. Wybierz **Azure** jako element docelowy.
3. Sprawdź subskrypcję i model wdrożenia po przejściu do trybu failover.
4. W **Sieć źródłowa**, wybierz źródłowej sieci maszyny Wirtualnej w środowisku lokalnym.
5. W **sieci docelowej**, wybierz sieć platformy Azure, w których repliki maszyn wirtualnych platformy Azure zostaną umieszczone w przypadku ich utworzenia po włączeniu trybu failover. Następnie kliknij przycisk **OK**.

    ![Mapowanie sieci](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** określ nazwę zasad. Firma Microsoft korzysta z **ContosoReplicationPolicy**.
3. Pozostaw ustawienia domyślne i kliknij przycisk **OK**.
    - Ustawienie **Częstotliwość kopiowania** wskazuje, że dane różnicowe (po replikacji początkowej) będą replikowane co pięć minut.
    - **Czas przechowywania punktu odzyskiwania** wskazuje dla każdego punktu odzyskiwania będą przechowywane przez dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - Ustawienie **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
    - **Szyfrowanie danych przechowywanych na platformie Azure** — domyślnie **poza** ustawienie wskazuje, że magazynowanych danych na platformie Azure nie jest zaszyfrowany.
4. Po utworzeniu zasad kliknij przycisk **OK**. Jeśli utworzysz nowe zasady, zostaną one automatycznie skojarzone z chmurą VMM.

## <a name="enable-replication"></a>Włączanie replikacji

1. W obszarze **Replikowanie aplikacji** kliknij przycisk **Źródło**. 
2. W **źródła**, wybierz chmurę VMM. Następnie kliknij przycisk **OK**.
3. W **docelowej**, sprawdź Azure jako środowisko docelowe oraz subskrypcję magazynu i wybierz **usługi Resource Manager** modelu.
4. Wybierz **contosovmsacct1910171607** konta magazynu i **ContosoASRnet** sieci platformy Azure.
5. W obszarze **Maszyny wirtualne** > **Wybierz** wybierz maszynę wirtualną, którą chcesz replikować. Następnie kliknij przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalizuj ochronę** replikacja początkowa zostanie zakończona, a maszyna wirtualna będzie gotowa do pracy w trybie failover.



## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania](tutorial-dr-drill-azure.md)
