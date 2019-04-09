---
title: Integrowanie usługi Azure ExpressRoute za pomocą odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 895b53d4091f04f324b28a148c7937159997fa84
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272771"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrowanie usługi Azure ExpressRoute za pomocą odzyskiwania po awarii dla maszyn wirtualnych platformy Azure


W tym artykule opisano sposób integracji usługi Azure ExpressRoute za pomocą [usługi Azure Site Recovery](site-recovery-overview.md), podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure.

Usługa Site Recovery umożliwia odzyskiwanie po awarii maszyn wirtualnych platformy Azure przez replikację danych maszyny Wirtualnej platformy Azure do platformy Azure.

- Jeśli używasz maszyn wirtualnych platformy Azure [usługi Azure managed disks](../virtual-machines/windows/managed-disks-overview.md), dane maszyny Wirtualnej są replikowane do replikowanego dysku zarządzanego w regionie pomocniczym.
- Jeśli maszyny wirtualne platformy Azure nie korzystają z dysków zarządzanych, maszyna wirtualna dane są replikowane do konta usługi Azure storage.
- Punkty końcowe replikacji są publiczne, ale ruch związany z replikacją maszyn wirtualnych platformy Azure nie cross przez internet.

Usługa ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft Azure za pośrednictwem połączenia prywatnego wspieranego przez dostawcę połączenia. W przypadku usługi ExpressRoute skonfigurować ją integruje się z usługą Site Recovery w następujący sposób:

- **Podczas replikacji między regionami platformy Azure**: Ruch związany z replikacją dla odzyskiwania po awarii maszyny Wirtualnej platformy Azure jest tylko w obrębie platformy Azure i usługi ExpressRoute nie jest wymagane lub używane na potrzeby replikacji. Jednak jeśli łączysz z lokacji lokalnej do maszyn wirtualnych platformy Azure w lokacji głównej platformy Azure, istnieje szereg zagadnień, które należy zwrócić uwagę podczas konfigurowania odzyskiwania po awarii dla tych maszyn wirtualnych platformy Azure.
- **Tryb failover między regionami platformy Azure**: W przypadku wystąpienia awarii, trybu failover maszyny wirtualne platformy Azure z serwera podstawowego na regionie pomocniczym platformy Azure. Po awarii do regionu pomocniczego, istnieje kilka kroków do wykonania w celu uzyskania dostępu do maszyn wirtualnych platformy Azure w dodatkowym regionie, przy użyciu usługi ExpressRoute.


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem upewnij się, że rozumiesz następujące pojęcia:

- Usługa ExpressRoute [obwodów](../expressroute/expressroute-circuit-peerings.md)
- Usługa ExpressRoute [domen routingu](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- Usługa ExpressRoute [lokalizacje](../expressroute/expressroute-locations.md).
- Maszyna wirtualna platformy Azure [architektura replikacji](azure-to-azure-architecture.md)
- Jak [skonfigurowania replikacji](azure-to-azure-tutorial-enable-replication.md) maszyn wirtualnych platformy Azure.
- Jak [w trybie Failover](azure-to-azure-tutorial-failover-failback.md) maszyn wirtualnych platformy Azure.


## <a name="general-recommendations"></a>Ogólne zalecenia

Najlepszym rozwiązaniem jest, aby zapewnić i cele czasu efektywnego odzyskiwania (RTO) do odzyskiwania po awarii zaleca się, wykonaj następujące czynności, po skonfigurowaniu Site Recovery, aby zintegrować z usługą ExpressRoute:

- Zapewnij składniki sieciowe przed włączeniem trybu failover do regionu pomocniczego:
    - Po włączeniu replikacji dla maszyn wirtualnych platformy Azure Usługa Site Recovery można automatycznie wdrażać zasoby sieciowe, takie jak sieci, podsieci i bram w docelowym regionie platformy Azure, zgodnie z ustawieniami sieci źródłowej.
    - Usługa Site Recovery nie może automatycznie skonfigurować zasoby sieciowe, takie jak bramy sieci wirtualnej.
    - Zaleca się, że możesz aprowizować dodatkowe zasoby sieciowe przed włączeniem trybu failover. Małe przestój jest skojarzony z tym wdrożeniem i może wpłynąć na czas odzyskiwania ogólne, jeśli nie zostały uwzględnione go podczas planowania wdrożenia.
- Uruchom po awarii w regularnych próbne operacje odzyskiwania:
    - Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Pomaga uniknąć problemów z konfiguracją ostatniej chwili, które może niekorzystnie wpłynąć na cel czasu odzyskiwania.
    - Po uruchomieniu testu trybu failover do testowania odzyskiwania po awarii, zaleca się użyć oddzielnej sieci maszyn wirtualnych platformy Azure, zamiast domyślnej sieci skonfigurowanej podczas włączania replikacji.
- W przypadku pojedynczego obwodu usługi ExpressRoute, należy użyć innej przestrzeni adresów IP.
    - Zalecamy użycie innej przestrzeni adresów IP dla docelowej sieci wirtualnej. Pozwala to uniknąć problemów podczas ustanawiania połączenia podczas awarii regionalnego.
    - Nie można użyć oddzielnych przestrzeń adresowa, należy uruchomić tryb failover awaryjnego odzyskiwania testowania odzyskiwania po awarii testu na osobna sieć testowa z różnymi adresami IP. Nie można połączyć dwie sieci wirtualne z nakładającymi się przestrzenią adresów IP z tym samym obwodem usługi ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikowanie maszyn wirtualnych platformy Azure, korzystając z usługi ExpressRoute


Jeśli chcesz skonfigurować replikację dla maszyn wirtualnych platformy Azure w lokacji głównej i łączysz się te maszyny wirtualne z lokacji lokalnej za pośrednictwem usługi ExpressRoute, Oto co należy zrobić:

1. [Włącz replikację](azure-to-azure-tutorial-enable-replication.md) dla każdej maszyny Wirtualnej platformy Azure.
2. Opcjonalnie zezwolić Site Recovery, konfigurowanie sieci:
    - Podczas konfigurowania i włączania replikacji usługa Site Recovery konfiguruje sieci, podsieci i podsieć bramy w docelowym regionie platformy Azure, aby odpowiadały tym w regionie źródłowym. Usługa Site Recovery również mapuje między sieciami wirtualnymi źródłowym i docelowym.
    - Jeśli nie chcesz, aby Usługa Site Recovery, aby to zrobić automatycznie, należy utworzyć zasoby sieciowe po stronie docelowej przed włączeniem replikacji.
3. Utwórz innymi elementami sieci:
    - Usługa Site Recovery nie tworzy tabele tras, bramy sieci wirtualnej, połączeń bramy sieci wirtualnej, wirtualne sieci równorzędne, lub innych sieci zasobów i połączeń w regionie pomocniczym.
    - Musisz utworzyć te dodatkowe elementy sieci w regionie pomocniczym w dowolnym momencie przed uruchomieniem trybu failover z regionu podstawowego.
    - Możesz użyć [planów odzyskiwania](site-recovery-create-recovery-plans.md) i skryptów automatyzacji, aby skonfigurować i połączyć je z zasobów sieciowych.
1. Jeśli masz wirtualnego urządzenia sieciowego (WUS) wdrożone do sterowania przepływem ruchu sieciowego, pamiętaj, że:
    - Domyślną trasę systemową platformy Azure do replikacji maszyn wirtualnych platformy Azure jest 0.0.0.0/0.
    - Zazwyczaj urządzenia WUS wdrożeń również zdefiniować trasy domyślnej (0.0.0.0/0), która wymusza ruch wychodzący z Internetu do przepływu przez urządzenie WUS. Trasy domyślnej jest używany, gdy znajduje się żadna inna konfiguracja określoną trasę.
    - Jeśli jest to możliwe, może być przeciążony urządzenie WUS, jeśli cały ruch replikacji przechodzą przez urządzenie WUS.
    - Takie samo ograniczenie występuje ma również zastosowanie w przypadku używania tras domyślnych dla routingu cały ruch maszyny Wirtualnej platformy Azure do lokalnych wdrożeń.
    - W tym scenariuszu zaleca się, że możesz [Tworzenie punktu końcowego usługi sieci](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w Twojej sieci wirtualnej dla usługi Microsoft.Storage tak, aby ruch związany z replikacją nie opuszczają platformy Azure.

## <a name="replication-example"></a>Przykład replikacji

Zazwyczaj wdrożeń w przedsiębiorstwach mają obciążeń podzielone między wieloma sieciami wirtualnymi platformy Azure, przy użyciu koncentratora centralny łączności zewnętrznej łączności z Internetem i lokacjami lokalnymi. Topologia gwiazdy jest zwykle używana wraz z usługi ExpressRoute.

![W lokalnym — z platformą Azure przy użyciu usługi ExpressRoute przed włączeniem trybu failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Aplikacje są wdrażane w regionie Azja Wschodnia platformy Azure.
- **Sieci wirtualne będące szprychami**. Aplikacje są wdrażane w dwie sieci wirtualne będące szprychami:
    - **Źródło vNet1**: 10.1.0.0/24.
    - **Źródło vNet2**: 10.2.0.0/24.
    - Każda sieć wirtualna gwiazdy jest podłączony do **piastą**.
- **Piastą**. Brak sieci wirtualnej koncentratora **źródła piastą**: 10.10.10.0/24.
  - Tej sieci wirtualnej koncentratora pełnić rolę strażnika.
  - Cała komunikacja między podsieciami przechodzą przez tego koncentratora.
    - Centrum sieci wirtualnej podsieci **. Sieć wirtualna Centrum ma dwie podsieci:
    - **Urządzenie WUS podsieci**: 10.10.10.0/25. Ta podsieć zawiera NVA (. 10.10.10.10).
    - **Podsieć bramy**: 10.10.10.128/25. Ta podsieć zawiera bramę usługi ExpressRoute, nawiązanie połączenia usługi ExpressRoute, która kieruje do lokacji lokalnej za pośrednictwem prywatnej komunikacji równorzędnej domeny routingu.
- W lokalnym centrum danych ma połączenie obwodu usługi ExpressRoute za pośrednictwem przewagę partnera w Hongkongu.
- Wszystkie routing jest kontrolowany przy użyciu tabel tras platformy Azure (UDR).
- Cały ruch wychodzący między sieciami wirtualnymi lub w lokalnym centrum danych odbywa się za pośrednictwem urządzenia NVA.

### <a name="hub-and-spoke-peering-settings"></a>Gwiazda komunikacji równorzędnej ustawienia

#### <a name="spoke-to-hub"></a>Szprycha do piasty

**Kierunek** | **Ustawienie** | **Stan**
--- | --- | ---
Szprycha do piasty | Zezwalaj na adres sieci wirtualnej | Enabled (Włączony)
Szprycha do piasty | Zezwalaj na ruch przesłany dalej | Enabled (Włączony)
Szprycha do piasty | Zezwalaj na tranzyt bramy | Disabled (Wyłączony)
Szprycha do piasty | Za pomocą bram remove | Enabled (Włączony)

 ![Typu gwiazda Centrum konfiguracji komunikacji równorzędnej](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Piasta do szprychy

**Kierunek** | **Ustawienie** | **Stan**
--- | --- | ---
Piasta do szprychy | Zezwalaj na adres sieci wirtualnej | Enabled (Włączony)
Piasta do szprychy | Zezwalaj na ruch przesłany dalej | Enabled (Włączony)
Piasta do szprychy | Zezwalaj na tranzyt bramy | Enabled (Włączony)
Piasta do szprychy | Za pomocą bram remove | Disabled (Wyłączony)

 ![Centrum typu gwiazda konfiguracji komunikacji równorzędnej](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Przykład kroki

W naszym przykładzie następujące powinno mieć miejsce po włączeniu replikacji dla maszyn wirtualnych platformy Azure w sieci źródłowej:

1. Możesz [włączyć replikację](azure-to-azure-tutorial-enable-replication.md) dla maszyny Wirtualnej.
2. Usługa Site Recovery utworzy repliki w sieciach wirtualnych, podsieci i podsieć bramy w regionie docelowym.
3. Usługa Site Recovery tworzy mapowania między sieciami źródła i sieci docelowych repliki, który on generuje.
4. Ręcznie utworzyć bramy sieci wirtualnej, połączeń bramy sieci wirtualnej, wirtualnych sieci równorzędnych, lub wszelkich innych sieci zasobów lub połączenia.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Tryb failover maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute

Po maszyny wirtualne platformy Azure przejścia w tryb failover do docelowego regionu platformy Azure przy użyciu Site Recovery, można z nich korzystać przy użyciu usługi ExpressRoute [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- Należy połączyć usługi ExpressRoute do docelowej sieci wirtualnej przy użyciu nowego połączenia. Istniejące połączenie usługi ExpressRoute nie są automatycznie przekazywane.
- Sposób, w którym można ustawić połączenie usługi ExpressRoute w docelowej sieci wirtualnej zależy od topologii usługi ExpressRoute.


### <a name="access-with-two-circuits"></a>Dostęp za pomocą dwóch obwodów

#### <a name="two-circuits-with-two-peering-locations"></a>Dwa obwody z dwóch lokalizacji komunikacji równorzędnej

Dzięki temu konfiguracja chroni obwodów usługi ExpressRoute względem regionalnej awarii. Jeśli lokalizacji głównej komunikacji równorzędnej ulegnie awarii, nadal połączenia z innej lokalizacji.

- Obwód podłączone do środowiska produkcyjnego jest zazwyczaj podstawowym. Obwód pomocniczy ma zazwyczaj mniejszej przepustowości, która może być zwiększana w przypadku wystąpienia awarii.
- Po włączeniu trybu failover można ustanowić połączenia z pomocniczego obwodu usługi ExpressRoute w docelowej sieci wirtualnej. Alternatywnie możesz mieć połączenia skonfigurowane i można w razie awarii, aby zmniejszyć ogólny czas odzyskiwania.
- Jednoczesnych połączeń podstawowych i docelowych, sieciami wirtualnymi upewnij się, że lokalne tylko routing używa obwód pomocniczy i połączenia po włączeniu trybu failover.
- Sieci wirtualne źródłowych i docelowych może odbierać nowe adresy IP, lub zachować te same po włączeniu trybu failover. W obu przypadkach dodatkowych połączeń, może zostać nawiązana przed trybu failover.


#### <a name="two-circuits-with-single-peering-location"></a>Dwa obwody z jednej lokalizacji komunikacji równorzędnej

Ta konfiguracja wpiera ochronę przed awariami głównej obwodu usługi ExpressRoute, ale nie wtedy, gdy ulegnie awarii jednej lokalizacji komunikacji równorzędnej usługi ExpressRoute, wpływ na obu obwodów.

- Może mieć jednocześnie połączenia z lokalnym centrum danych, źródłowa sieć wirtualna za pomocą obwód podstawowy i docelowej sieci wirtualnej przy użyciu pomocniczego obwodu.
- Jednoczesnych połączeń z podstawowego, jak i obiektem docelowym upewnij się, że lokalne tylko routing używa pomocniczego obwodu i połączenia po włączeniu trybu failover.
-   Nie można połączyć zarówno obwodów tej samej sieci wirtualnej, gdy obwodów tworzonych w tej samej lokalizacji komunikacji równorzędnej.

### <a name="access-with-a-single-circuit"></a>Dostęp za pomocą jednego obwodu

W tej konfiguracji jest tylko jeden obwód usługi Expressroute. Mimo, że obwód jest nadmiarowe połączenie w przypadku, gdy ulegnie awarii jednego, obwodu jedną trasę nie zapewnia odporności na błędy, jeśli ulegnie awarii komunikacji równorzędnej regionu. Należy pamiętać, że:

- Teraz można replikować maszyny wirtualne platformy Azure do dowolnego regionu Azure w [tej samej lokalizacji geograficznej](azure-to-azure-support-matrix.md#region-support). Jeśli docelowy region platformy Azure nie jest w tej samej lokalizacji co źródłowa, musisz włączyć ExpressRoute Premium, jeśli używasz jednego obwodu usługi ExpressRoute. Dowiedz się więcej o [lokalizacji usługi ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) i [ExpressRoute — cennik](https://azure.microsoft.com/pricing/details/expressroute/).
- Nie można nawiązać połączenia sieci wirtualnych źródłowych i docelowych jednocześnie obwodu Jeśli tą samą przestrzenią adresów IP jest używany w regionie docelowym. W tym scenariuszu:    
    -  Rozłącz połączenie po stronie źródła, a następnie ustanawiać połączenie po stronie docelowej. Ta zmiana połączenia umożliwia pisanie skryptów, w ramach planu odzyskiwania Usługa Site Recovery. Należy pamiętać, że:
        - W przypadku regionalnej awarii jeśli region podstawowy jest niedostępny, może nie działać operację rozłączania. Może to mieć wpływ na region docelowy tworzenia połączenia.
        - Jeśli połączenie jest tworzone w regionie docelowym, i regionu podstawowego odzyskuje później, może wystąpić pakietów docelowych, jeśli dwa połączenia próbują nawiązać połączenia z tą samą przestrzenią adresów.
        - Aby tego uniknąć, należy natychmiast zakończyć połączenia podstawowego.
        - Po maszyny Wirtualnej powrotu po awarii do regionu podstawowego połączenia podstawowego można ponownie ustanowić, po odłączeniu połączenia pomocniczego.
-   Jeśli różne przestrzenie adresowe jest używany w docelowej sieci wirtualnej, możesz jednocześnie nawiązać źródłowych i docelowych sieci wirtualnych z tego samego obwodu usługi ExpressRoute.


## <a name="failover-example"></a>Przykład trybu failover

W naszym przykładzie używamy następujących topologii:

- Dwa różne obwody usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej.
- Zachowaj prywatnych adresów IP maszyn wirtualnych platformy Azure po włączeniu trybu failover.
- Docelowy region odzyskiwania to wersja platformy Azure, Azja południowo-wschodnia.
- Połączenia pomocniczego obwodu usługi ExpressRoute zostanie nawiązane za pośrednictwem przewagę partnera w Singapurze.

Dla prostą topologię, która używa jednego obwodu usługi ExpressRoute za pomocą tego samego adresu IP po przejściu w tryb failover [zapoznaj się z tym artykułem](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Przykład kroki
Aby zautomatyzować odzyskiwanie, w tym przykładzie tutaj firmy co należy zrobić:

1. Wykonaj kroki, aby skonfigurować replikację.
2. [Maszyny wirtualne platformy Azure w tryb failover](azure-to-azure-tutorial-failover-failback.md), za pomocą następujące dodatkowe czynności w trakcie lub po zakończeniu pracy w trybie failover.

    a. Utwórz bramę usługi ExpressRoute platformy Azure w sieci wirtualnej serwera centralnego regionu docelowego. To jest konieczne jest połączenie sieci wirtualnej koncentratora docelowy z obwodem usługi ExpressRoute.

    b. Utwórz połączenie z sieci wirtualnej koncentratora docelowy do obiektu docelowego obwód usługi ExpressRoute.

    c. Konfigurowanie komunikacji równorzędnej sieci wirtualnej między centrum regionu docelowego i sieci wirtualne będące szprychami. Właściwości komunikacji równorzędnej w regionie docelowym będą takie same, jak w regionie źródłowym.

    d. Konfigurowanie tras zdefiniowanych przez użytkownika w sieci wirtualnej serwera centralnego i dwie sieci wirtualne będące szprychami.

    - Właściwości po stronie docelowej tras zdefiniowanych przez użytkownika są takie same, jak po stronie źródła podczas przy użyciu tego samego adresu IP adresów.
    - Za pomocą różnych docelowych adresów IP trasy Udr należy odpowiednio zmodyfikować.


Powyższe kroki umożliwia pisanie skryptów, jako część [planu odzyskiwania](site-recovery-create-recovery-plans.md). W zależności od aplikacji łączności i wymagań w zakresie czasu odzyskiwania powyższe kroki mogą być wykonywane przed rozpoczęciem pracy w trybie failover.

#### <a name="after-recovery"></a>Po odzyskaniu

Po odzyskiwaniu maszyn wirtualnych i kończenie łączności, środowisko odzyskiwania jest w następujący sposób.

![W lokalnym — z platformą Azure przy użyciu usługi ExpressRoute, po włączeniu trybu Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o korzystaniu z [planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzowania trybu failover w aplikacji.
