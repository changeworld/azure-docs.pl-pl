---
title: Zachowaj adresy IP po przeczesywaniu usługi Azure VM w usłudze Azure site recovery
description: W tym artykule opisano sposób zachowywania adresów IP w przypadku awarii maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii do regionu pomocniczego za pomocą usługi Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257565"
---
# <a name="retain-ip-addresses-during-failover"></a>Zachowywanie adresów IP podczas pracy awaryjnej

[Usługa Azure Site Recovery](site-recovery-overview.md) umożliwia odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przez replikowanie maszyn wirtualnych do innego regionu platformy Azure, w przypadku awarii i niepowodzeniem z powrotem do regionu podstawowego, gdy rzeczy są z powrotem do normy.

Podczas pracy awaryjnej można zachować adresowanie IP w regionie docelowym identyczne z regionem źródłowym:

- Domyślnie po włączeniu odzyskiwania po awarii dla maszyn wirtualnych platformy Azure usługa Site Recovery tworzy zasoby docelowe na podstawie ustawień zasobów źródłowych. W przypadku maszyn wirtualnych platformy Azure skonfigurowanych ze statycznymi adresami IP usługa Site Recovery próbuje aprowizować ten sam adres IP dla docelowej maszyny Wirtualnej, jeśli nie jest używana. Aby uzyskać pełne wyjaśnienie, w jaki sposób usługi Site Recovery obsługują adresowanie, [zapoznaj się z tym artykułem](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- W przypadku prostych aplikacji wystarczy domyślna konfiguracja. W przypadku bardziej złożonych aplikacji może być konieczne aprowizowanie dodatkowych zasobów, aby upewnić się, że łączność działa zgodnie z oczekiwaniami po przepracie awaryjnym.


W tym artykule przedstawiono kilka przykładów zachowania adresów IP w bardziej złożonych przykładowych scenariuszach. Przykłady obejmują:

- Przewijanie awaryjne dla firmy z wszystkimi zasobami uruchomionymi na platformie Azure
- Tryb failover dla firmy z wdrożeniem hybrydowym i zasobów działających zarówno lokalnie, jak i na platformie Azure

## <a name="resources-in-azure-full-failover"></a>Zasoby na platformie Azure: pełna praca awaryjna

Firma A ma wszystkie swoje aplikacje uruchomione na platformie Azure.

### <a name="before-failover"></a>Przed przejściem awaryjnym

Oto architektura przed przejściem w stan failover.

- Firma A ma identyczne sieci i podsieci w regionach platformy Azure źródłowych i docelowych.
- Aby skrócić cel czasu odzyskiwania (RTO), firma używa węzłów repliki dla programu SQL Server Always On, kontrolerów domeny itp. Te węzły repliki znajdują się w innej sieci wirtualnej w regionie docelowym, dzięki czemu można ustanowić łączność lokacji sieci VPN między regionami źródłowymi i docelowymi. Nie jest to możliwe, jeśli ta sama przestrzeń adresu IP jest używana w źródle i miejscu docelowym.  
- Przed przejściem awaryjnym architektura sieci jest następująca:
    - Regionem podstawowym jest Platforma Azure Wschodnia
        - Azja Wschodnia ma sieć wirtualną **(source vnet)** z przestrzenią adresową 10.1.0.0/16.
        - W Azji Wschodniej obciążenia podzielone są na trzy podsieci w sieci wirtualnej:
            - **Podsieć 1**: 10.1.1.0/24
            - **Podsieć 2**: 10.1.2.0/24
            - **Podsieć 3**: 10.1.3.0/24
    - Region pomocniczy (docelowy) to Azure Southeast Asia
        - Azja Południowo-Wschodnia ma odzyskiwanie sieci wirtualnej **(Recovery VNet)** identyczne **ze źródłową siecią wirtualną**.
        - Azja Południowo-Wschodnia ma dodatkową witrynę wirtualną **(Azure VNet)** z przestrzenią adresową 10.2.0.0/16.
        - **Usługa Azure VNet** zawiera podsieć **(podsieć 4)** z przestrzenią adresową 10.2.4.0/24.
        - Węzły repliki dla programu SQL Server Always **Subnet 4**On, kontroler domeny itp.
    - **Źródłowej sieci wirtualnej** i **sieci wirtualnej platformy Azure** są połączone z połączeniem lokacja-lokacja sieci VPN.
    - **Sieć wirtualna odzyskiwania** nie jest połączona z żadną inną siecią wirtualną.
    - **Firma A** przypisuje/weryfikuje docelowe adresy IP dla elementów replikowanych. Docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej.

![Zasoby na platformie Azure przed pełnym trybem failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po pracy awaryjnej

Jeśli wystąpi awaria regionalna źródła, firma A może awaryjnie wszystkie swoje zasoby do regionu docelowego.

- W razie upływu docelowego adresu IP, które zostały już wprowadzone przed przejściem awaryjnym, firma A może aranżować przełączenie w błąd i automatycznie nawiązywane połączenia po przejściu w błąd między **siecią wirtualną odzyskiwania** a **siecią wirtualną platformy Azure.** Jest to zilustrowane na poniższym diagramie..
- W zależności od wymagań aplikacji połączenia między dwiema sieciami wirtualnymi **(sieci** wirtualnej odzyskiwania i **sieci wirtualnej platformy Azure**) w regionie docelowym można ustanowić przed, w trakcie (jako krok pośredni) lub po przełączeniu awaryjnego.
  - Firma może używać [planów odzyskiwania,](site-recovery-create-recovery-plans.md) aby określić, kiedy połączenia zostaną nawiązane.
  - Mogą łączyć się między sieciami wirtualnymi przy użyciu komunikacji równorzędnej sieci wirtualnej lub sieci VPN lokacji lokacji.
      - W przypadku wirtualnych sieci równorzędnych nie jest używana brama sieci VPN i występują inne ograniczenia.
      - Ceny komunikacji równorzędnej sieci [wirtualnej](https://azure.microsoft.com/pricing/details/virtual-network) są obliczane inaczej niż ceny bramy sieci VPN sieci [wirtualnej.](https://azure.microsoft.com/pricing/details/vpn-gateway) W przypadku pracy awaryjnej zazwyczaj zaleca się użycie tej samej metody łączności jako sieci źródłowych, w tym typu połączenia, aby zminimalizować nieprzewidywalne zdarzenia sieciowe.

    ![Pełne przebłajowanie zasobów na platformie Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Zasoby na platformie Azure: izolowana aplikacja trybu failover

Może być konieczne przełączenie w stan fail over na poziomie aplikacji. Na przykład w celu przesiedć w stan fail on określonej aplikacji lub warstwy aplikacji znajduje się w dedykowanej podsieci.

- W tym scenariuszu, chociaż można zachować adresowania IP, nie jest ogólnie wskazane, ponieważ zwiększa ryzyko niespójności łączności. Utracisz również łączność podsieci z innymi podsieciami w tej samej sieci wirtualnej platformy Azure.
- Lepszym sposobem wykonania pracy awaryjnej aplikacji na poziomie podsieci jest użycie różnych docelowych adresów IP do pracy awaryjnej (jeśli potrzebujesz łączności z innymi podsieciami w źródłowej sieci wirtualnej) lub wyizolowanie każdej aplikacji w dedykowanej sieci wirtualnej w regionie źródłowym. Za pomocą tego ostatniego podejścia można ustanowić łączność między sieciami w regionie źródłowym i emulować to samo zachowanie podczas pracy awaryjnej w regionie docelowym.  

W tym przykładzie firma A umieszcza aplikacje w regionie źródłowym w dedykowanych sieciach wirtualnych i ustanawia łączność między tymi sieciami wirtualnymi. Dzięki temu projektowi mogą wykonywać izolowane tryb failover aplikacji i zachowywać źródłowe prywatne adresy IP w sieci docelowej.

### <a name="before-failover"></a>Przed przejściem awaryjnym

Przed przejściem awaryjnym architektura jest następująca:

- Maszyny wirtualne aplikacji są hostowane w podstawowym regionie Azure East Asia:
    - **Aplikacja1** Maszyny wirtualne znajdują się w **sieci wirtualnej źródło sieci wirtualnej 1:** 10.1.0.0/16.
    - **Aplikacja 2** Maszyny wirtualne znajdują się w **sieci wirtualnej źródło sieci wirtualnej 2:** 10.2.0.0/16.
    - **Źródło sieci wirtualnej 1** ma dwie podsieci.
    - **Źródło sieci wirtualnej 2** ma dwie podsieci.
- Region pomocniczy (docelowy) to Azure Southeast Asia — Azja Południowo-Wschodnia ma sieci wirtualne odzyskiwania **(sieć wirtualna odzyskiwania 1** i **sieć wirtualną odzyskiwania 2),** które są identyczne z **źródłową siecią wirtualną 1** i **źródłową siecią wirtualną 2**.
        - **Sieć wirtualna odzyskiwania 1** i **sieć wirtualna odzyskiwania 2** mają po dwie podsieci, które pasują do podsieci w **źródłowej sieci wirtualnej 1** i źródłowej sieci **wirtualnej 2** — azja południowo-wschodnia ma dodatkową sieć wirtualną **(Sieć wirtualną platformy Azure)** z przestrzenią adresową 10.3.0.0/16.
        - **Usługa Azure VNet** zawiera podsieć **(podsieć 4)** z przestrzenią adresową 10.3.4.0/24.
        - Węzły repliki dla programu SQL Server Always **Subnet 4**On, kontroler domeny itp.
- Istnieje wiele połączeń sieci VPN lokacja-lokacja: 
    - **Źródłowej sieci wirtualnej 1** i **sieci wirtualnej platformy Azure**
    - **Źródłowej sieci wirtualnej 2** i **sieci wirtualnej platformy Azure**
    - **Źródło sieci wirtualnej 1** i **źródło sieci wirtualnej 2** są połączone z lokacją sieci VPN
- **Odzyskiwanie sieci wirtualnej 1** i **odzyskiwania sieci wirtualnej 2** nie są połączone z innymi sieciami wirtualnymi.
- **Firma A** konfiguruje bramy sieci VPN w **sieci wirtualnej odzyskiwania 1** i **sieci wirtualnej odzyskiwania 2**, aby zmniejszyć rto.  
- **Odzyskiwanie wirtualne1** i **odzyskiwanie wirtualne2** nie są połączone z inną siecią wirtualną.
- Aby skrócić cel czasu odzyskiwania (RTO), bramy sieci VPN są konfigurowane na **sieci wirtualnej odzyskiwania 1** i **sieci wirtualnej odzyskiwania2** przed przeczesyniem pracy awaryjnej.

    ![Zasoby na platformie Azure przed przełączeniem aplikacji w niepowodzeniem](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Po pracy awaryjnej

W przypadku awarii lub problemu, który wpływa na pojedynczą aplikację (w **Source VNet 2 w naszym przykładzie), firma A może odzyskać aplikację, którego dotyczy problem:


- Rozłącz połączenia sieci VPN między **źródłową siecią wirtualną1** a **źródłową siecią wirtualną2**oraz między **źródłową siecią wirtualną2** a **siecią wirtualną platformy Azure** .
- Ustanawianie połączeń sieci VPN między **źródłową siecią wirtualną1** i **siecią wirtualną odzyskiwania2**oraz między **siecią Wirtualną odzyskiwania 2** a **siecią wirtualną platformy Azure**.
- Maszyny wirtualne w wersji fail0 w **źródłowej sieci wirtualnej2** do **odzyskiwania wirtualnej2**.

![Zasoby w pracy awaryjnej aplikacji platformy Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- W tym przykładzie można rozwinąć, aby uwzględnić więcej aplikacji i połączeń sieciowych. Zalecenie jest, aby wykonać podobny model połączenia, w miarę możliwości, gdy w przypadku awarii ze źródła do obiektu docelowego.
- Bramy sieci VPN używają publicznych adresów IP i przeskoków bramy do nawiązywania połączeń. Jeśli nie chcesz używać publicznych adresów IP lub chcesz uniknąć dodatkowych przeskoków, możesz użyć [komunikacji równorzędnej sieci wirtualnej platformy Azure,](../virtual-network/virtual-network-peering-overview.md) aby równorzędnie sieci wirtualnych w [obsługiwanych regionach platformy Azure.](../virtual-network/virtual-network-manage-peering.md#cross-region)

## <a name="hybrid-resources-full-failover"></a>Zasoby hybrydowe: pełna praca awaryjna

W tym scenariuszu **firma B** uruchamia firmę hybrydową, z częścią infrastruktury aplikacji uruchomionej na platformie Azure, a pozostała część działa lokalnie. 

### <a name="before-failover"></a>Przed przejściem awaryjnym

Oto, jak wygląda architektura sieci przed przejściem awaryjnym.

- Maszyny wirtualne aplikacji są hostowane w Azji Wschodniej platformy Azure.
- Azja Wschodnia ma sieć wirtualną **(source vnet)** z przestrzenią adresową 10.1.0.0/16.
  - Azja Wschodnia ma obciążenia podzielone na trzy podsieci w **źródłowej sieci wirtualnej:**
    - **Podsieć 1**: 10.1.1.0/24
    - **Podsieć 2**: 10.1.2.0/24
    - **Podsieć 3:** 10.1.3.0/24, wykorzystująca sieć wirtualną platformy Azure z przestrzenią adresową 10.1.0.0/16. Ta sieć wirtualna nosi nazwę **source vnet**
      - Pomocniczym (docelowym) regionem jest Azure Southeast Asia:
  - Azja Południowo-Wschodnia ma odzyskiwanie sieci wirtualnej **(Recovery VNet)** identyczne **ze źródłową siecią wirtualną**.
- Maszyny wirtualne w Azji Wschodniej są połączone z lokalnym centrum danych za pomocą usługi Azure ExpressRoute lub sieci VPN typu lokacja-lokacja.
- Aby zmniejszyć RTO, bramy kompecyjnie firmy B aprecji w sieci wirtualnej odzyskiwania w Azji Południowo-Wschodniej platformy Azure przed przeczesyniem pracy awaryjnej.
- Firma B przypisuje/weryfikuje docelowe adresy IP dla replikowanych maszyn wirtualnych. Docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej.


![Łączność lokalna z platformą Azure przed przełączeniem w ten sposób](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po pracy awaryjnej


Jeśli wystąpi awaria regionalna źródła, firma B może awaryjnie wszystkie swoje zasoby do regionu docelowego.

- W razie upływu docelowego adresu IP, które zostały już wprowadzone przed przejściem awaryjnym, firma B może aranżować przełączenie w błąd i automatycznie nawiązywane połączenia po przejściu w błąd między **siecią wirtualną odzyskiwania** a **siecią wirtualną platformy Azure.**
- W zależności od wymagań aplikacji połączenia między dwiema sieciami wirtualnymi **(sieci** wirtualnej odzyskiwania i **sieci wirtualnej platformy Azure**) w regionie docelowym można ustanowić przed, w trakcie (jako krok pośredni) lub po przełączeniu awaryjnego. Firma może używać [planów odzyskiwania,](site-recovery-create-recovery-plans.md) aby określić, kiedy połączenia zostaną nawiązane.
- Oryginalne połączenie między usługą Azure East Asia a lokalnym centrum danych powinno zostać rozłączone przed nawiązaniem połączenia między platformą Azure Southeast Asia i lokalnym centrum danych.
- Routing lokalny jest ponownie skonfigurowany w celu wskazania regionu docelowego i bram po przemiń awaryjnych.

![Łączność lokalna z platformą Azure po przemijenie w pracy awaryjnej](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Zasoby hybrydowe: izolowana aplikacja trybu failover

Firma B nie może awaryjnie nad izolowanymi aplikacjami na poziomie podsieci. Dzieje się tak, ponieważ przestrzeń adresowa w sieciach wirtualnych źródłowych i odzyskiwania jest taka sama, a oryginalne źródło do połączenia lokalnego jest aktywne.

 - W przypadku odporności aplikacji firma B będzie musiała umieścić każdą aplikację w własnej dedykowanej sieci wirtualnej platformy Azure.
 - Z każdej aplikacji w oddzielnej sieci wirtualnej, firma B może awaryjnie nad izolowanych aplikacji i połączenia źródłowe trasy do regionu docelowego.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md).
