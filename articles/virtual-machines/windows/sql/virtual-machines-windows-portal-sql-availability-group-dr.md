---
title: Konfigurowanie grupy dostępności w różnych regionach
description: W tym artykule wyjaśniono, jak skonfigurować grupę dostępności programu SQL Server na maszynach wirtualnych platformy Azure z repliką w innym regionie.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060110"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Konfigurowanie grupy dostępności na maszynach wirtualnych programu Azure SQL Server w różnych regionach

W tym artykule wyjaśniono, jak skonfigurować replikę grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure w zdalnej lokalizacji platformy Azure. Ta konfiguracja służy do obsługi odzyskiwania po awarii.

Ten artykuł dotyczy maszyn wirtualnych platformy Azure w trybie Menedżera zasobów.

Na poniższej ilustracji przedstawiono typowe wdrożenie grupy dostępności na maszynach wirtualnych platformy Azure:

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

W tym wdrożeniu wszystkie maszyny wirtualne znajdują się w jednym regionie platformy Azure. Repliki grupy dostępności mogą mieć synchroniczne zatwierdzanie z automatyczną przełęczą w trybie failover w programach SQL-1 i SQL-2. Aby utworzyć tę architekturę, zobacz [Szablon grupy dostępności lub samouczek](virtual-machines-windows-portal-sql-availability-group-overview.md).

Ta architektura jest narażona na przestoje, jeśli region platformy Azure staje się niedostępny. Aby rozwiązać ten problem, dodaj replikę w innym regionie platformy Azure. Na poniższym diagramie pokazano, jak nowa architektura będzie wyglądać:

   ![Dr grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Na poprzednim diagramie przedstawiono nową maszynę wirtualną o nazwie SQL-3. SQL-3 znajduje się w innym regionie platformy Azure. Sql-3 jest dodawany do klastra trybu failover systemu Windows Server. SQL-3 może obsługiwać replikę grupy dostępności. Na koniec należy zauważyć, że region platformy Azure dla sql-3 ma nowy moduł równoważenia obciążenia platformy Azure.

>[!NOTE]
> Zestaw dostępności platformy Azure jest wymagany, gdy więcej niż jedna maszyna wirtualna znajduje się w tym samym regionie. Jeśli tylko jedna maszyna wirtualna znajduje się w regionie, zestaw dostępności nie jest wymagany. Maszynę wirtualną można umieścić tylko w zestawie dostępności w czasie tworzenia. Jeśli maszyna wirtualna jest już w zestawie dostępności, można dodać maszynę wirtualną dla dodatkowej repliki później.

W tej architekturze replika w regionie zdalnym jest zwykle konfigurowana w trybie dostępności asynchronicznie zatwierdzania i trybie ręcznego pracy awaryjnej.

Gdy repliki grup dostępności znajdują się na maszynach wirtualnych platformy Azure w różnych regionach platformy Azure, każdy region wymaga:

* Brama sieci wirtualnej
* Połączenie bramy sieci wirtualnej

Na poniższym diagramie przedstawiono sposób komunikacji sieci między centrami danych.

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Ta architektura wiąże się z opłatami za dane wychodzące dla danych replikowanych między regionami platformy Azure. Zobacz [Ceny przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Tworzenie repliki zdalnej

Aby utworzyć replikę w zdalnym centrum danych, wykonaj następujące czynności:

1. [Utwórz sieć wirtualną w nowym regionie](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną przy użyciu portalu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >W niektórych przypadkach może być trzeba użyć programu PowerShell do utworzenia połączenia sieci wirtualnej do sieci wirtualnej. Na przykład jeśli używasz różnych kont platformy Azure, nie można skonfigurować połączenia w portalu. W tym przypadku zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną przy użyciu portalu Azure.](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

1. [Utwórz kontroler domeny w nowym regionie](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ten kontroler domeny zapewnia uwierzytelnianie, jeśli kontroler domeny w lokacji głównej nie jest dostępny.

1. [Utwórz maszynę wirtualną programu SQL Server w nowym regionie](virtual-machines-windows-portal-sql-server-provision.md).

1. [Utwórz moduł równoważenia obciążenia platformy Azure w sieci w nowym regionie](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Ten moduł równoważenia obciążenia musi:

   - Bądź w tej samej sieci i podsieci co nowa maszyna wirtualna.
   - Mają statyczny adres IP dla odbiornika grupy dostępności.
   - Dołącz pulę wewnętrznej bazy danych składającą się tylko z maszyn wirtualnych w tym samym regionie co moduł równoważenia obciążenia.
   - Użyj sondy portu TCP specyficznej dla adresu IP.
   - Mają regułę równoważenia obciążenia specyficzne dla programu SQL Server w tym samym regionie.  
   - Równo być standardowy moduł równoważenia obciążenia, jeśli maszyny wirtualne w puli wewnętrznej bazy danych nie są częścią zestawu pojedynczej dostępności lub grupy skalowania maszyny wirtualnej. Aby uzyskać dodatkowe informacje, zapoznaj się z [omówieniem standardu równoważenia obciążenia azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Dodaj funkcję klastrowania trybu failover do nowego programu SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Dołącz nowy program SQL Server do domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Ustaw nowe konto usługi PROGRAMU SQL Server tak, aby używało konta domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Dodaj nowy program SQL Server do klastra trybu failover systemu Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Dodawanie zasobu adresu IP do klastra.

   Zasób adresów IP można utworzyć w Menedżerze klastrów trybu failover. Wybierz nazwę klastra, a następnie kliknij prawym przyciskiem myszy nazwę klastra w obszarze **Podstawowe zasoby klastra** i wybierz polecenie **Właściwości:** 

   ![Właściwości klastra](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   W oknie dialogowym **Właściwości** wybierz pozycję **Dodaj** w obszarze **Adres IP**, a następnie dodaj adres IP nazwy klastra z regionu sieci zdalnej. Wybierz przycisk OK w oknie dialogowym **Adres IP,** a następnie ponownie wybierz przycisk OK w oknie dialogowym **Właściwości klastra,** aby zapisać nowy adres IP.Select OK on the IP dialog, and then select OK again on the Cluster Properties dialog to save the new IP address.Select **OK** on the IP dialog, and then select **OK** again on the cluster properties dialog. 

   ![Dodawanie adresu IP klastra](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Dodaj adres IP jako zależność dla podstawowej nazwy klastra.

   Ponownie otwórz właściwości klastra i wybierz kartę **Zależności.** 

   ![Właściwości klastra](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. Dodaj zasób adresu IP do roli grupy dostępności w klastrze. 

   Kliknij prawym przyciskiem myszy rolę grupy dostępności w Menedżerze klastrów trybu failover, wybierz pozycję **Dodaj zasób,** **Więcej zasobów**i wybierz polecenie Adres **IP**.

   ![Tworzenie adresu IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Skonfiguruj ten adres IP w następujący sposób:

   - Użyj sieci ze zdalnego centrum danych.
   - Przypisz adres IP z nowego modułu równoważenia obciążenia platformy Azure. 

1. Dodaj zasób adresu IP jako zależność klastra punktu dostępu klienta odbiornika (nazwa sieciowa).

   Na poniższym zrzucie ekranu przedstawiono prawidłowo skonfigurowany zasób klastra adresów IP:

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Grupa zasobów klastra zawiera oba adresy IP. Oba adresy IP są zależnościami dla punktu dostępu klienta odbiornika. Użyj operatora **OR** w konfiguracji zależności klastra.

1. [Ustawianie parametrów klastra w programie PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Uruchom skrypt programu PowerShell z nazwą sieci klastra, adresem IP i portem sondy skonfigurowanym na modułze równoważenia obciążenia w nowym regionie.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. W nowym programie SQL Server w programie SQL Server Configuration Manager [włącz grupy zawsze włączone do dostępności](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Otwórz porty zapory w nowym programie SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Numery portów, które należy otworzyć, zależą od środowiska. Otwórz porty dla dublowania punktu końcowego i sondy kondycji modułu równoważenia obciążenia platformy Azure.


1. [Dodaj replikę do grupy dostępności w nowym programie SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Dla repliki w zdalnym regionie platformy Azure, ustaw ją dla replikacji asynchroniiowej z ręcznej pracy awaryjnej.  

## <a name="set-connection-for-multiple-subnets"></a>Ustawianie połączenia dla wielu podsieci

Replika w zdalnym centrum danych jest częścią grupy dostępności, ale znajduje się w innej podsieci. Jeśli ta replika staje się repliką podstawową, mogą wystąpić limity czasu połączenia aplikacji. To zachowanie jest takie samo jak lokalna grupa dostępności we wdrożeniu wielu podsieci. Aby zezwolić na połączenia z aplikacji klienckich, zaktualizuj połączenie klienta lub skonfiguruj buforowanie rozpoznawania nazw w zasobie nazw sieci klastra.

Najlepiej zaktualizować parametry połączenia klienta, `MultiSubnetFailover=Yes`aby ustawić . Zobacz [Łączenie z MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Jeśli nie można zmodyfikować parametry połączenia, można skonfigurować buforowanie rozpoznawania nazw. Zobacz [Błąd przesiewania czasu i nie można połączyć się z odbiornikiem grupy dostępności programu SQL Server 2012 AlwaysOn w środowisku wielosieciowym](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Praca awaryjna w regionie zdalnym

Aby przetestować łączność odbiornika z regionem zdalnym, można przełączyć replikę w stan fail, do regionu zdalnego. Gdy replika jest asynchroniza, pracy awaryjnej jest narażony na potencjalne utraty danych. Aby zakończyć się awaryjnie bez utraty danych, zmień tryb dostępności na synchroniczne i ustaw tryb pracy awaryjnej na automatyczny. Wykonaj następujące czynności:

1. W **Eksploratorze obiektów**połącz się z wystąpieniem programu SQL Server, w którym znajduje się replika podstawowa.
1. W obszarze **Grupy dostępności AlwaysOn** **kliknij**prawym przyciskiem myszy grupę dostępności i kliknij polecenie **Właściwości**.
1. Na stronie **Ogólne** w obszarze **Repliki dostępności**ustaw replikę pomocniczą w lokacji odzyskiwania po awarii, aby używała trybu dostępności **zatwierdzania synchroniczowego** i trybu automatycznego trybu pracy **awaryjnej.**
1. Jeśli replika pomocnicza znajduje się w tej samej lokacji co replika podstawowa, aby uzyskać wysoką dostępność, ustaw tę replikę na **Asynchroniczne zatwierdzanie** i **ręczne**.
1. Kliknij przycisk OK.
1. W **Eksploratorze obiektów**kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij polecenie **Pokaż pulpit nawigacyjny**.
1. Na pulpicie nawigacyjnym sprawdź, czy replika w lokacji odzyskiwania po awarii jest zsynchronizowana.
1. W **Eksploratorze obiektów**kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij polecenie **Praca awaryjna...**. Program SQL Server Management Studios otwiera kreatora w celu awaryjnego pracy programu SQL Server.  
1. Kliknij **przycisk Dalej**i wybierz wystąpienie programu SQL Server w witrynie odzyskiwania po awarii. Kliknij ponownie przycisk **Next** (Dalej).
1. Połącz się z wystąpieniem programu SQL Server w witrynie odzyskiwania po awarii i kliknij przycisk **Dalej**.
1. Na stronie **Podsumowanie** sprawdź ustawienia i kliknij przycisk **Zakończ**.

Po przetestowaniu łączności przenieś replikę podstawową z powrotem do podstawowego centrum danych i ustaw tryb dostępności z powrotem do normalnych ustawień pracy. W poniższej tabeli przedstawiono normalne ustawienia operacyjne architektury opisanej w tym dokumencie:

| Lokalizacja | Wystąpienie serwera | Rola | Tryb dostępności | Tryb pracy awaryjnej
| ----- | ----- | ----- | ----- | -----
| Podstawowe centrum danych | SQL-1 | Podstawowy | Synchroniczny | Automatyczny
| Podstawowe centrum danych | SQL-2 (w języku SQL-2) | Pomocniczy | Synchroniczny | Automatyczny
| Dodatkowe lub zdalne centrum danych | SQL-3 | Pomocniczy | Asynchroniczne | Ręcznie


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Więcej informacji o planowanym i wymuszonym ręcznym trybie failover

Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Wykonywanie planowanej ręcznej pracy awaryjnej grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Wykonywanie wymuszonej ręcznej pracy awaryjnej grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Dodatkowe linki

* [Zawsze włączone grupy dostępności](https://msdn.microsoft.com/library/hh510230.aspx)
* [Maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Moduły równoważenia obciążenia platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Zestawy dostępności platformy Azure](../manage-availability.md)
