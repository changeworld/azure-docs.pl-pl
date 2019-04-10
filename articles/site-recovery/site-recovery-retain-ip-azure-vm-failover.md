---
title: Zachowaj adresów IP podczas pracy w trybie failover maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Opisuje sposób zachować adresy IP, gdy przejść w tryb failover maszyn wirtualnych platformy Azure do odzyskiwania po awarii do regionu pomocniczego przy użyciu usługi Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357888"
---
# <a name="retain-ip-addresses-during-failover"></a>Podczas pracy awaryjnej należy zachować adresy IP

[Usługa Azure Site Recovery](site-recovery-overview.md) umożliwia odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przez replikowanie maszyn wirtualnych do innego regionu platformy Azure, przechodzenie w tryb failover, jeśli wystąpi awaria i przechodzenie do regionu podstawowego, w przypadku elementów na normalne.

Podczas pracy w trybie failover może chcesz zachować przydzielanie adresów IP w regionie docelowym identyczne do regionu źródłowego:

- Domyślnie po włączeniu funkcji odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, Usługa Site Recovery tworzy zasobów docelowych na podstawie ustawień zasobu źródła. Skonfigurowano statyczne adresy IP maszyn wirtualnych platformy Azure Usługa Site Recovery próbuje udostępniania tego samego adresu IP dla docelowej maszyny Wirtualnej, jeśli nie jest używany. Aby uzyskać pełne wyjaśnienie, jak Usługa Site Recovery obsługuje adresowania [zapoznaj się z tym artykułem](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- W przypadku prostych aplikacji domyślnej konfiguracji jest wystarczająca. W przypadku bardziej złożonych aplikacji może być konieczne aprowizowanie dodatkowych zasobów, aby upewnić się, że połączenie działa w oczekiwany sposób po włączeniu trybu failover.


W tym artykule przedstawiono kilka przykładów płaci adresów IP w bardziej złożonych scenariuszy przykładu. Przykłady:

- Tryb failover dla firmy, ze wszystkimi zasobami działających na platformie Azure
- Tryb failover dla firmy za pomocą wdrożenie hybrydowe i zasoby działające lokalnie i na platformie Azure

## <a name="resources-in-azure-full-failover"></a>Zasoby na platformie Azure: pełny trybu failover

Firma A ma wszystkie swoje aplikacje działające na platformie Azure.

### <a name="before-failover"></a>Przed włączeniem trybu failover

Poniżej przedstawiono architekturę przed włączeniem trybu failover.

- Firmy, A ma identyczne sieci i podsieci w źródłowych i docelowych regionów platformy Azure.
- Aby zmniejszyć cel czasu odzyskiwania (RTO), firma korzysta z repliki węzłów dla programu SQL Server Always On, kontrolery domeny, itp. Te węzły repliki są w innej sieci wirtualnej w regionie docelowym co one może nawiązać połączenie z siecią VPN lokacja lokacja między regionami w źródłowym i docelowym. Nie jest to możliwe, jeśli tą samą przestrzenią adresów IP jest używany w elemencie źródłowym i docelowym.  
- Przed włączeniem trybu failover architekturę sieci jest następująca:
    - Region podstawowy jest Azja Wschodnia platformy Azure
        - Azja Wschodnia ma sieci wirtualnej (**źródłowa sieć wirtualna**) przy użyciu 10.1.0.0/16 przestrzeni adresowej.
        - Azja Wschodnia ma obciążeń podzielić na trzy podsieci w sieci wirtualnej:
            - **Podsieć 1**: 10.1.1.0/24
            - **Podsieć 2**: 10.1.2.0/24,
            - **Podsieci 3**: 10.1.3.0/24
    - Region pomocniczy (docelowy) to Azure Południowo-Wschodnia Azja
        - Azja południowo-wschodnia ma odzyskiwania sieci wirtualnej (**sieć odzyskiwania**) taka sama jak **źródłowa sieć wirtualna**.
        - Azja południowo-wschodnia ma dodatkowe sieci wirtualnej (**sieci wirtualnej platformy Azure**) przy użyciu 10.2.0.0/16 przestrzeni adresowej.
        - **Sieci wirtualnej platformy Azure** zawiera podsieć (**4 podsieci**) przy użyciu 10.2.4.0/24 przestrzeni adresowej.
        - Węzły repliki dla programu SQL Server Always On, kontroler domeny itd. znajdują się w **4 podsieci**.
    - **Źródła sieci wirtualnej** i **sieci wirtualnej platformy Azure** są połączone za pomocą połączenia sieci VPN lokacja lokacja.
    - **Sieć wirtualna odzyskiwania** nie jest połączony z innej sieci wirtualnej.
    - **Firmy A** przypisuje/sprawdza docelowych adresów IP dla zreplikowanych elementów. Docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej.

![Zasoby na platformie Azure, przed włączeniem trybu failover pełne](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po włączeniu trybu failover

Jeśli wystąpi awaria regionalna źródła, firmy A można przełączyć w tryb failover wszystkie jej zasoby w regionie docelowym.

- Za pomocą docelowych adresów IP już na miejscu przed trybu failover firmy A można organizować tryb failover i automatycznego nawiązywania połączeń po włączeniu trybu failover między **sieć odzyskiwania** i **sieci wirtualnej platformy Azure**. Jest to zilustrowane na poniższym diagramie...
- W zależności od wymagań dotyczących aplikacji, połączeń między dwiema sieciami wirtualnymi (**sieć odzyskiwania** i **sieci wirtualnej platformy Azure**) w elemencie docelowym może być region ustalonych wcześniej, podczas (jako pośredniego kroku) lub po pracy w trybie failover.
  - Firma może używać [planów odzyskiwania](site-recovery-create-recovery-plans.md) do określenia, kiedy będzie można nawiązać połączenia.
  - Umożliwić im połączenie między sieciami wirtualnymi za pomocą komunikacji równorzędnej sieci wirtualnej lub sieci VPN typu lokacja lokacja.
      - W przypadku wirtualnych sieci równorzędnych nie jest używana brama sieci VPN i występują inne ograniczenia.
      - Komunikacja równorzędna sieci wirtualnych [ceny](https://azure.microsoft.com/pricing/details/virtual-network) jest obliczana inaczej niż Brama sieci VPN typu sieć wirtualna-sieć wirtualna [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway). Dla pracy w trybie Failover Radzimy zminimalizować zdarzenia nieprzewidywalne sieci przy użyciu tej samej metody łączności jako źródła sieci, w tym typ połączenia.

    ![Zasoby w pełnej platformy Azure w tryb failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Zasoby na platformie Azure: izolowany aplikacji w tryb failover

Może być konieczne do trybu failover na poziomie aplikacji. Na przykład w trybie Failover specyficzne dla aplikacji lub warstwy aplikacji znajduje się w dedykowanej podsieci.

- W tym scenariuszu mimo że można zachować adresowanie IP, nie jest na ogół ponieważ zwiększa ryzyko wystąpienia niespójności łączności. Utracisz też łączność podsieci do innych podsieci w tej samej sieci wirtualnej platformy Azure.
- Lepszy sposób przejść w tryb failover aplikacji na poziomie podsieci jest używać różnych docelowych adresów IP dla trybu failover (Jeśli potrzebujesz łączności z innych podsieci w źródłowej sieci wirtualnej) lub aby odizolować każdą aplikację we własnej, dedykowanej sieci wirtualnej w regionie źródłowym. Drugie podejście mogą poprawnie ustanowić połączenia między sieciami w regionie źródłowym, a ponadto emulować takie samo zachowanie przechodzenia w tryb failover do regionu docelowego.  

W tym przykładzie A miejsc aplikacji w regionie źródłowym w sieciach wirtualnych w wersji dedykowanej i ustanawia połączenie między tymi sieciami wirtualnymi. W tym projekcie są izolowane aplikacji w tryb failover i Zachowaj źródło prywatnych adresów IP w sieci docelowej.

### <a name="before-failover"></a>Przed włączeniem trybu failover

Przed włączeniem trybu failover architektura jest w następujący sposób:

- Maszyn wirtualnych aplikacji znajdują się w regionie podstawowym Azure Azja Wschodnia:
    - **App1** maszyny wirtualne znajdują się w sieci wirtualnej **źródła sieci wirtualnej 1**: 10.1.0.0/16.
    - **App2** maszyny wirtualne znajdują się w sieci wirtualnej **źródła sieci wirtualnej 2**: 10.2.0.0/16.
    - **Źródło 1 sieć wirtualna** ma dwie podsieci.
    - **Źródła sieci wirtualnej 2** ma dwie podsieci.
- Region pomocniczy (docelowy) to Azure Południowo-Wschodnia Azja — Azja południowo-wschodnia ma odzyskiwania sieciami wirtualnymi (**1 sieć wirtualna odzyskiwania** i **2 sieć odzyskiwania**), które są identyczne z **źródła sieci wirtualnej 1** i **Źródła siecią vnet2**.
        - **Sieć wirtualna odzyskiwania 1** i **2 sieć odzyskiwania** mają dwie podsieci, które odpowiadają podsieci w **źródła sieci wirtualnej 1** i **źródła sieci wirtualnej 2** -ma Azja południowo-wschodnia dodatkowe sieci wirtualnej (**sieci wirtualnej platformy Azure**) przy użyciu 10.3.0.0/16 przestrzeni adresowej.
        - **Sieci wirtualnej platformy Azure** zawiera podsieć (**4 podsieci**) przy użyciu 10.3.4.0/24 przestrzeni adresowej.
        -Węzły repliki dla programu SQL Server Always On, kontroler domeny itd. znajdują się w **4 podsieci**.
- Istnieje wiele połączeń sieci VPN typu lokacja lokacja: 
    - **Źródła sieci wirtualnej 1** i **sieci wirtualnej platformy Azure**
    - **Źródło siecią vnet2** i **sieci wirtualnej platformy Azure**
    - **Źródło 1 sieć wirtualna** i **źródła sieci wirtualnej 2** są połączone za pomocą sieci VPN lokacja lokacja
- **Sieć wirtualna odzyskiwania 1** i **2 sieć odzyskiwania** nie są połączone z innymi sieciami wirtualnymi.
- **Firmy A** umożliwia skonfigurowanie bramy sieci VPN na **1 sieć wirtualna odzyskiwania** i **2 sieć odzyskiwania**, aby zmniejszyć cel czasu odzyskiwania.  
- **Odzyskiwanie VNet1** i **VNet2 odzyskiwania** nie są połączone z usługą virtual network.
- Aby zmniejszyć cel czasu odzyskiwania (RTO), bramy sieci VPN są skonfigurowane na **VNet1 odzyskiwania** i **VNet2 odzyskiwania** przed trybu failover.

    ![Zasoby na platformie Azure, przed włączeniem trybu failover w aplikacji](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Po włączeniu trybu failover

W razie awarii lub problem, który wpływa na jednej aplikacji (w ** źródła sieci wirtualnej 2 w naszym przykładzie), firmy A można odzyskać dotyczy aplikacji w następujący sposób:


- Rozłączanie połączenia sieci VPN między **VNet1 źródła** i **VNet2 źródła**oraz między **VNet2 źródła** i **sieci wirtualnej platformy Azure** .
- Nawiązywać połączenia sieci VPN między **VNet1 źródła** i **VNet2 odzyskiwania**oraz między **VNet2 odzyskiwania** i **sieci wirtualnej platformy Azure**.
- Maszyny wirtualne w tryb failover **źródła VNet2** do **VNet2 odzyskiwania**.

![Zasoby w aplikacji platformy Azure w tryb failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- W tym przykładzie można rozszerzyć, aby uwzględnić większej liczby aplikacji oraz połączeń sieciowych. Zaleca się postępuj zgodnie z modelem podobne jak połączenie, o ile to możliwe, podczas przechodzenia w tryb failover ze źródła do docelowego.
- Bramy sieci VPN używają publiczne adresy IP i przeskoków bramy do nawiązywania połączeń. Jeśli nie chcesz używać publicznych adresów IP, lub aby uniknąć dodatkowych przeskoków, możesz użyć [komunikacji równorzędnej sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md) nawiązać komunikację równorzędną sieci wirtualnych między [obsługiwane regiony platformy Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Zasoby hybrydowego: pełne trybu failover

W tym scenariuszu **firmy B** uruchamiany firm hybrydowej, przy użyciu część infrastruktury aplikacji, uruchomione na platformie Azure i resztę, uruchamiane lokalnie. 

### <a name="before-failover"></a>Przed włączeniem trybu failover

Oto jak wygląda architektura sieci przed włączeniem trybu failover.

- Maszyn wirtualnych aplikacji znajdują się w usłudze Azure Azja Wschodnia.
- Azja Wschodnia ma sieci wirtualnej (**źródłowa sieć wirtualna**) przy użyciu 10.1.0.0/16 przestrzeni adresowej.
  - Azja Wschodnia ma obciążeń podzielić na trzy podsieci w **źródłowa sieć wirtualna**:
    - **Podsieć 1**: 10.1.1.0/24
    - **Podsieć 2**: 10.1.2.0/24,
    - **Podsieci 3**: 10.1.3.0/24utilizing siecią wirtualną platformy Azure przy użyciu 10.1.0.0/16 przestrzeni adresowej. Ta sieć wirtualna ma nazwę **źródłowa sieć wirtualna**
      - Region pomocniczy (docelowy) to Azure, Azja południowo-wschodnia:
  - Azja południowo-wschodnia ma odzyskiwania sieci wirtualnej (**sieć odzyskiwania**) taka sama jak **źródłowa sieć wirtualna**.
- Maszyny wirtualne w Azja Wschodnia są połączone z lokalnym centrum danych za pomocą usługi Azure ExpressRoute lub sieci lokacji do lokacji VPN.
- Aby zmniejszyć czas RTO, firmy B aprowizuje bramy w sieci wirtualnej odzyskiwania w usłudze Azure Azja południowo-wschodnia przed trybu failover.
- Firma B przypisuje/sprawdza docelowych adresów IP dla replikowanych maszyn wirtualnych. Docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny Wirtualnej.


![Łączność w lokalnym — z platformą Azure, przed włączeniem trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po włączeniu trybu failover


Jeśli wystąpi awaria regionalna źródła, firmy B można przełączyć w tryb failover wszystkie jej zasoby w regionie docelowym.

- Za pomocą docelowych adresów IP już na miejscu przed trybu failover firmy B można organizować tryb failover i automatycznego nawiązywania połączeń po włączeniu trybu failover między **sieć odzyskiwania** i **sieci wirtualnej platformy Azure**.
- W zależności od wymagań dotyczących aplikacji, połączeń między dwiema sieciami wirtualnymi (**sieć odzyskiwania** i **sieci wirtualnej platformy Azure**) w elemencie docelowym może być region ustalonych wcześniej, podczas (jako pośredniego kroku) lub po pracy w trybie failover. Firma może używać [planów odzyskiwania](site-recovery-create-recovery-plans.md) do określenia, kiedy będzie można nawiązać połączenia.
- Oryginalne połączenie między Azja Wschodnia platformy Azure i lokalnym centrum danych musi być odłączony przed nawiązaniem połączenia między Azja Południowa platformy Azure i lokalnym centrum danych.
- Lokalne routingu jest konfigurowana ponownie, aby wskazać region docelowy i bram publikowanie trybu failover.

![Łączność w lokalnym — z platformą Azure po włączeniu trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Zasoby hybrydowego: izolowany aplikacji w tryb failover

Firma B nie można przełączyć w tryb failover aplikacje izolowane na poziomie podsieci. Jest to spowodowane przestrzeni adresowej w elemencie źródłowym i odzyskiwania sieciami wirtualnymi jest taka sama, a oryginalne źródło, aby połączenie lokalne jest aktywny.

 - Aby zapewnić odporność aplikacji firmy B należy umieścić każdą aplikację we własnej dedykowanej sieci wirtualnej platformy Azure.
 - Z poszczególnymi aplikacjami w oddzielnych sieci wirtualnej firmy B można w trybie Failover aplikacje izolowane i kierować połączeń ze źródłami do regionu docelowego.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md).
