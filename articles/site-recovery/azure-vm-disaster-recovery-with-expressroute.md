---
title: Integracja odzyskiwania po awarii usługi Azure AzureRoute VM z usługą Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i usługi Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954081"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integracja usługi ExpressRoute z odzyskiwaniem po awarii dla maszyn wirtualnych platformy Azure


W tym artykule opisano sposób integrowania usługi Azure ExpressRoute z [usługą Azure Site Recovery](site-recovery-overview.md), podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w pomocniczym regionie platformy Azure.

Usługa Site Recovery umożliwia odzyskiwanie po awarii maszyn wirtualnych platformy Azure przez replikowanie danych maszyny wirtualnej platformy Azure na platformę Azure.

- Jeśli maszyny wirtualne platformy Azure używają [dysków zarządzanych platformy Azure,](../virtual-machines/windows/managed-disks-overview.md)dane maszyny wirtualnej są replikowane na replikowany dysk zarządzany w regionie pomocniczym.
- Jeśli maszyny wirtualne platformy Azure nie używają dysków zarządzanych, dane maszyny wirtualnej są replikowane na konto magazynu platformy Azure.
- Punkty końcowe replikacji są publiczne, ale ruch replikacji dla maszyn wirtualnych platformy Azure nie przechodzi przez Internet.

Usługa ExpressRoute umożliwia rozszerzenie sieci lokalnych do chmury platformy Microsoft Azure za pośrednictwem połączenia prywatnego, ułatwione przez dostawcę łączności. Jeśli skonfigurowano program ExpressRoute, integruje się z odzyskiem witryny w następujący sposób:

- **Podczas replikacji między regionami platformy Azure:** ruch replikacji dla odzyskiwania po awarii maszyny Wirtualnej platformy Azure jest tylko na platformie Azure, a usługa ExpressRoute nie jest potrzebna ani używana do replikacji. Jednak jeśli łączysz się z lokacji lokalnej do maszyn wirtualnych platformy Azure w podstawowej witrynie platformy Azure, istnieje wiele problemów, o których należy pamiętać podczas konfigurowania odzyskiwania po awarii dla tych maszyn wirtualnych platformy Azure.
- **Przełączenie w stan failover między regionami platformy Azure:** w przypadku wystąpienia awarii, można awaryjnie maszyny wirtualne platformy Azure z podstawowego do pomocniczego regionu platformy Azure. Po przepełnieniu po awarii do regionu pomocniczego, istnieje wiele kroków do podjęcia w celu uzyskania dostępu do maszyn wirtualnych platformy Azure w regionie pomocniczym przy użyciu usługi ExpressRoute.


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem upewnij się, że rozumiesz następujące pojęcia:

- [Obwody](../expressroute/expressroute-circuit-peerings.md) usługi ExpressRoute
- [Domeny routingu](../expressroute/expressroute-circuit-peerings.md#routingdomains) usługi ExpressRoute
- [Lokalizacje](../expressroute/expressroute-locations.md)usługi ExpressRoute .
- [Architektura replikacji maszyny Wirtualnej](azure-to-azure-architecture.md) platformy Azure
- Jak [skonfigurować replikację](azure-to-azure-tutorial-enable-replication.md) dla maszyn wirtualnych platformy Azure.
- Jak [awaryjnie nad](azure-to-azure-tutorial-failover-failback.md) maszynami wirtualnymi platformy Azure.


## <a name="general-recommendations"></a>Zalecenia ogólne

Aby zapewnić skuteczne cele czasu odzyskiwania (RTO) do odzyskiwania po awarii, zaleca się wykonanie następujących czynności podczas konfigurowania usługi Site Recovery w celu integracji z usługą ExpressRoute:

- Aprowizuj składniki sieci przed przełączeniem pracy awaryjnej do regionu pomocniczego:
    - Po włączeniu replikacji dla maszyn wirtualnych platformy Azure usługa Site Recovery może automatycznie wdrażać zasoby sieciowe, takie jak sieci, podsieci i bramy w docelowym regionie platformy Azure, na podstawie ustawień sieci źródłowej.
    - Usługa Site Recovery nie może automatycznie skonfigurować zasobów sieciowych, takich jak bramy sieci wirtualnej.
    - Zaleca się aprowizowanie tych dodatkowych zasobów sieciowych przed przejściem awaryjnym. Z tym wdrożeniem jest skojarzony mały przestój, który może mieć wpływ na ogólny czas odzyskiwania, jeśli nie uwzględniłeś go podczas planowania wdrożenia.
- Uruchom regularne ćwiczenia odzyskiwania po awarii:
    - Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Pomaga uniknąć problemów z konfiguracją w ostatniej chwili, które mogą niekorzystnie wpłynąć na RTO.
    - Po uruchomieniu testowego trybu failover dla wiertła zaleca się użycie oddzielnej sieci maszyn wirtualnych platformy Azure, zamiast domyślnej sieci, która jest skonfigurowana po włączeniu replikacji.
- Użyj różnych przestrzeni adresów IP, jeśli masz jeden obwód usługi ExpressRoute.
    - Zaleca się użycie innej przestrzeni adresowej IP dla docelowej sieci wirtualnej. Pozwala to uniknąć problemów podczas ustanawiania połączeń podczas regionalnych awarii.
    - Jeśli nie można użyć oddzielnej przestrzeni adresowej, należy uruchomić test odzyskiwania po awarii w yłącze awaryjne w oddzielnej sieci testowej z różnymi adresami IP. Nie można połączyć dwóch sieci wirtualnych z nakładającą się przestrzenią adresów IP z tym samym obwodem usługi ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikowanie maszyn wirtualnych platformy Azure podczas korzystania z usługi ExpressRoute


Jeśli chcesz skonfigurować replikację dla maszyn wirtualnych platformy Azure w lokacji głównej i łączysz się z tymi maszynami wirtualnymi z witryny lokalnej za pomocą usługi ExpressRoute, oto, co musisz zrobić:

1. [Włącz replikację](azure-to-azure-tutorial-enable-replication.md) dla każdej maszyny Wirtualnej platformy Azure.
2. Opcjonalnie pozwól, aby usługa Site Recovery skonfigurowała sieć:
    - Po skonfigurowaniu i włączeniu replikacji usługa Site Recovery konfiguruje sieci, podsieci i podsieci bramy w docelowym regionie platformy Azure, aby dopasować je do sieci w regionie źródłowym. Usługa Site Recovery mapuje również między sieciami źródłowymi i docelowymi sieciami wirtualnymi.
    - Jeśli nie chcesz, aby usługa Site Recovery robiła to automatycznie, utwórz zasoby sieciowe po stronie docelowej przed włączeniem replikacji.
3. Tworzenie innych elementów sieciowych:
    - Usługa Site Recovery nie tworzy tabel tras, bram sieci wirtualnej, połączeń bramy sieci wirtualnej, komunikacji równorzędnej sieci wirtualnej ani innych zasobów i połączeń sieciowych w regionie pomocniczym.
    - Należy utworzyć te dodatkowe elementy sieci w regionie pomocniczym, w dowolnym momencie przed uruchomieniem pracy awaryjnej z regionu podstawowego.
    - [Planów odzyskiwania](site-recovery-create-recovery-plans.md) i skryptów automatyzacji można użyć do skonfigurowania i połączenia tych zasobów sieciowych.
1. Jeśli masz sieciowe urządzenie wirtualne (NVA) wdrożone w celu kontrolowania przepływu ruchu sieciowego, należy pamiętać, że:
    - Domyślna trasa systemowa platformy Azure dla replikacji maszyny Wirtualnej platformy Azure to 0.0.0.0/0.
    - Zazwyczaj wdrożenia urządzenia WUS definiują również trasę domyślną (0.0.0.0/0), która wymusza przepływ wychodzącego ruchu internetowego przez urządzenie WUS. Trasa domyślna jest używana, gdy nie można znaleźć innej konfiguracji określonej trasy.
    - W takim przypadku urządzenie WUS może być przeciążone, jeśli cały ruch replikacji przechodzi przez urządzenie WUS.
    - To samo ograniczenie ma również zastosowanie w przypadku używania tras domyślnych do routingu całego ruchu maszyn wirtualnych platformy Azure do wdrożeń lokalnych.
    - W tym scenariuszu zaleca się [utworzenie punktu końcowego usługi sieciowej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla usługi Microsoft.Storage, aby ruch replikacji nie opuszczał granicy platformy Azure.

## <a name="replication-example"></a>Przykład replikacji

Zazwyczaj wdrożenia w przedsiębiorstwie mają obciążenia podzielone na wiele sieci wirtualnych platformy Azure, z centralnym centrum łączności dla łączności zewnętrznej z Internetem i lokalnymi lokacjami. Topologia koncentratora i szprychy jest zwykle używana razem z programem ExpressRoute.

![Platforma Lokalna do platformy Azure z usługą ExpressRoute przed przejściem w pracy awaryjnej](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Aplikacje są wdrażane w regionie Azji Wschodniej platformy Azure.
- **Szprychy vNets**. Aplikacje są wdrażane w dwóch sieciach wirtualnych szprychy:
    - **Źródło vNet1**: 10.1.0.0/24.
    - **Źródło vNet2**: 10.2.0.0/24.
    - Każda szprychowa sieć wirtualna jest połączona z **hub vNet**.
- **Hub vNet**. Istnieje hub vNet **Source Hub vNet vNet:** 10.10.10.0/24.
  - Ta hub vNet działa jako strażnik.
  - Cała komunikacja między podsieciami przejść przez tego centrum.
    - **Podsieci sieci wirtualnej centrum**. Hub vNet ma dwie podsieci:
    - **Podsieć NVA**: 10.10.10.0/25. Ta podsieć zawiera urządzenie WUS (10.10.10.10).
    - **Podsieć bramy:** 10.10.10.128/25. Ta podsieć zawiera bramę usługi ExpressRoute połączoną z połączeniem usługi ExpressRoute, która kieruje do lokacji lokalnej za pośrednictwem prywatnej domeny routingu równorzędnego.
- Lokalne centrum danych ma połączenie obwodu usługi ExpressRoute za pośrednictwem krawędzi partnera w Hong Kongu.
- Wszystkie routing jest kontrolowany za pomocą tabel marszrut platformy Azure (UDR).
- Cały ruch wychodzący między sieciami wirtualnymi lub lokalnym centrum danych jest kierowany za pośrednictwem urządzenia WUS.

### <a name="hub-and-spoke-peering-settings"></a>Ustawienia komunikacji równorzędnej i komunikacji szprychy

#### <a name="spoke-to-hub"></a>Szprycha do piasty

**Kierunek** | **Ustawienie** | **Stan**
--- | --- | ---
Szprycha do piasty | Zezwalaj na adres sieci wirtualnej | Enabled (Włączony)
Szprycha do piasty | Zezwalaj na ruch przesyłany dalej | Enabled (Włączony)
Szprycha do piasty | Zezwalaj na przesyłanie bramy | Disabled (Wyłączony)
Szprycha do piasty | Użyj usuwania bram | Enabled (Włączony)

 ![Konfiguracja komunikacji równorzędnej rozmawianie z koncentratorem](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Piasta do szprychy

**Kierunek** | **Ustawienie** | **Stan**
--- | --- | ---
Piasta do szprychy | Zezwalaj na adres sieci wirtualnej | Enabled (Włączony)
Piasta do szprychy | Zezwalaj na ruch przesyłany dalej | Enabled (Włączony)
Piasta do szprychy | Zezwalaj na przesyłanie bramy | Enabled (Włączony)
Piasta do szprychy | Użyj usuwania bram | Disabled (Wyłączony)

 ![Konfiguracja komunikacji równorzędnej koncentratora do komunikacji szprychowej](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Przykładowe kroki

W naszym przykładzie podczas włączania replikacji dla maszyn wirtualnych platformy Azure w sieci źródłowej powinny mieć miejsce następujące czynności:

1. [Można włączyć replikację](azure-to-azure-tutorial-enable-replication.md) dla maszyny Wirtualnej.
2. Usługa Site Recovery utworzy repliki sieci wirtualnych, podsieci i podsieci bramy w regionie docelowym.
3. Usługa Site Recovery tworzy mapowania między sieciami źródłowymi a sieciami docelowymi replik, które tworzy.
4. Ręcznie tworzysz bramy sieci wirtualnej, połączenia bramy sieci wirtualnej, komunikację równorzędną sieci wirtualnej lub inne zasoby lub połączenia sieciowe.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Maszyny wirtualne platformy Azure w wersji Fail over podczas korzystania z usługi ExpressRoute

Po awarii maszyn wirtualnych platformy Azure do docelowego regionu platformy Azure przy użyciu usługi Site Recovery, można uzyskać do nich dostęp za pomocą usługi ExpressRoute [private peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- Musisz połączyć program ExpressRoute z docelową siecią wirtualną za pomocą nowego połączenia. Istniejące połączenie usługi ExpressRoute nie jest automatycznie przesyłane.
- Sposób konfigurowania połączenia usługi ExpressRoute z docelową siecią wirtualną zależy od topologii usługi ExpressRoute.


### <a name="access-with-two-circuits"></a>Dostęp z dwoma obwodami

#### <a name="two-circuits-with-two-peering-locations"></a>Dwa obwody z dwoma lokalizacjami komunikacji równorzędnej

Ta konfiguracja pomaga chroni obwody usługi ExpressRoute przed regionalną awarią. Jeśli podstawowa lokalizacja komunikacji równorzędnej uchyli w dół, połączenia mogą być kontynuowane z innej lokalizacji.

- Obwód podłączony do środowiska produkcyjnego jest zwykle podstawowym. Obwód pomocniczy zazwyczaj ma niższą przepustowość, która może zostać zwiększona w przypadku wystąpienia awarii.
- Po przełączeniu w błąd można ustanowić połączenia z pomocniczego obwodu usługi ExpressRoute do docelowej sieci wirtualnej. Alternatywnie można skonfigurować połączenia i gotowe w przypadku awarii, aby skrócić ogólny czas odzyskiwania.
- W przypadku jednoczesnych połączeń z sieciami wirtualnymi podstawowymi i docelowymi upewnij się, że routing lokalny używa tylko obwodu pomocniczego i połączenia po przełączeniu awaryjnym.
- Źródłowe i docelowe sieci wirtualne mogą odbierać nowe adresy IP lub zachować te same, po przejściu w stan failover. W obu przypadkach połączenia pomocnicze można ustanowić przed przełączeniem awaryjnym.


#### <a name="two-circuits-with-single-peering-location"></a>Dwa obwody z pojedynczą lokalizacją komunikacji równorzędnej

Ta konfiguracja pomaga chronić przed awarią podstawowego obwodu usługi ExpressRoute, ale nie wtedy, gdy pojedyncza lokalizacja komunikacji równorzędnej usługi ExpressRoute ustępuje, wpływając na oba obwody.

- Można mieć jednoczesne połączenia z lokalnego centrum danych do źródła vNEt z obwodu podstawowego i do docelowej sieci wirtualnej z obwodu pomocniczego.
- W przypadku jednoczesnych połączeń z podstawowym i docelowym upewnij się, że routing lokalny używa tylko obwodu pomocniczego i połączenia po przełączeniu awaryjnym.
-   Nie można połączyć obu obwodów z tą samą siecią wirtualną, gdy obwody są tworzone w tej samej lokalizacji komunikacji równorzędnej.

### <a name="access-with-a-single-circuit"></a>Dostęp za pomocą jednego obwodu

W tej konfiguracji jest tylko jeden obwód usługi Expressroute. Mimo że obwód ma nadmiarowe połączenie w przypadku, gdy jeden ustępuje, jeden obwód trasy nie zapewni odporności, jeśli region komunikacji równorzędnej ustępuje. Należy pamiętać, że:

- Maszyny wirtualne platformy Azure można replikować do dowolnego regionu platformy Azure w [tej samej lokalizacji geograficznej.](azure-to-azure-support-matrix.md#region-support) Jeśli docelowy region platformy Azure nie znajduje się w tej samej lokalizacji co źródło, należy włączyć usługę ExpressRoute Premium, jeśli używasz jednego obwodu usługi ExpressRoute. Dowiedz się więcej o [lokalizacjach usługi ExpressRoute](../expressroute/expressroute-locations.md) i [cenach usługi ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Nie można połączyć źródłowych i docelowych sieci wirtualnych jednocześnie z obwodem, jeśli w regionie docelowym jest używana ta sama przestrzeń adresowa IP. W tym scenariuszu:    
    -  Odłącz połączenie po stronie źródła, a następnie nawiązuj połączenie po stronie docelowej. Ta zmiana połączenia może być skryptowana jako część planu odzyskiwania lokacji. Należy pamiętać, że:
        - W przypadku awarii regionalnej, jeśli region podstawowy jest niedostępny, operacja rozłączania może zakończyć się niepowodzeniem. Może to mieć wpływ na tworzenie połączenia z regionem docelowym.
        - Jeśli połączenie utworzono w regionie docelowym, a region podstawowy zostanie odzyskany później, może wystąpić spadek pakietów, jeśli dwa jednoczesne połączenia będą próbowały połączyć się z tą samą przestrzenią adresową.
        - Aby temu zapobiec, należy natychmiast zakończyć połączenie podstawowe.
        - Po łączeniu po awarii maszyny Wirtualnej do regionu podstawowego można ponownie ustanowić połączenie podstawowe po odłączeniu połączenia pomocniczego.
-   Jeśli w docelowej sieci wirtualnej są używane różne przestrzenie adresowe, można jednocześnie połączyć się ze źródłowymi i docelowymi sieciami wirtualnymi z tego samego obwodu usługi ExpressRoute.


## <a name="failover-example"></a>Przykład trybu failover

W naszym przykładzie używamy następującej topologii:

- Dwa różne obwody usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej.
- Zachowaj prywatne adresy IP dla maszyn wirtualnych platformy Azure po przemijaniu awaryjnym.
- Docelowy region odzyskiwania to Azure SouthEast Asia.
- Wtórne połączenie obwodu usługi ExpressRoute jest ustanawiane przez przewagę partnera w Singapurze.

Aby uzyskać prostą topologię, która używa jednego obwodu usługi ExpressRoute, z tym samym adresem IP po przekroju awaryjnym, zapoznaj się z [tym artykułem](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Przykładowe kroki
Aby zautomatyzować odzyskiwanie w tym przykładzie, oto, co musisz zrobić:

1. Wykonaj kroki, aby skonfigurować replikację.
2. [Przeładuj w stan failover maszyn wirtualnych platformy Azure,](azure-to-azure-tutorial-failover-failback.md)z tych dodatkowych kroków podczas lub po pracy awaryjnej.

    a. Utwórz bramę usługi Azure ExpressRoute w sieci wirtualnej centrum regionu docelowego. Należy połączyć docelową w sieci wirtualnej koncentratora z obwodem usługi ExpressRoute.

    b. Utwórz połączenie z docelowej sieci wirtualnej koncentratora do docelowego obwodu usługi ExpressRoute.

    d. Konfigurowanie komunikacji równorzędnej sieci wirtualnej między koncentratorem regionu docelowego a sieciami wirtualnymi. Właściwości komunikacji równorzędnej w regionie docelowym będą takie same jak w regionie źródłowym.

    d. Skonfiguruj UDR w sieci wirtualnej centrum i dwie szprychowe sieci wirtualne.

    - Właściwości udr stronie docelowej są takie same jak te po stronie źródła podczas korzystania z tych samych adresów IP.
    - W przypadku różnych docelowych adresów IP, UDR powinny być odpowiednio modyfikowane.


Powyższe kroki mogą być skryptowane jako część [planu odzyskiwania](site-recovery-create-recovery-plans.md). W zależności od wymagań dotyczących łączności aplikacji i czasu odzyskiwania powyższe kroki można również wykonać przed rozpoczęciem pracy awaryjnej.

#### <a name="after-recovery"></a>Po odzyskaniu

Po odzyskaniu maszyn wirtualnych i zakończeniu łączności środowisko odzyskiwania jest następujące.

![Platforma Lokalna do platformy Azure z usługą ExpressRoute po przejściu w pracy awaryjnej](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [używaniu planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzacji pracy awaryjnej aplikacji.
