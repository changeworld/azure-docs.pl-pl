---
title: Odzyskiwanie po awarii — usługa Azure Virtual Machines — grup dostępności serwera SQL | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania grupy dostępności programu SQL Server na maszynach wirtualnych Azure z repliką w innym regionie.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: f9e31ac7685d597c741033bc165c6a51280e3d72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571730"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Konfigurowanie zawsze włączonej grupy dostępności na maszynach wirtualnych platformy Azure w różnych regionach

W tym artykule opisano sposób konfigurowania programu SQL Server Always On repliki grupy dostępności na maszynach wirtualnych platformy Azure w zdalnej lokalizacji platformy Azure. Użyj tej konfiguracji w ramach obsługi odzyskiwania po awarii.

Ten artykuł ma zastosowanie do maszyn wirtualnych platformy Azure w trybie usługi Resource Manager.

Na poniższej ilustracji przedstawiono typowe wdrożenie grupy dostępności na maszynach wirtualnych platformy Azure:

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

W tym wdrożeniu wszystkie maszyny wirtualne znajdują się w jednym regionie platformy Azure. Repliki grupy dostępności może mieć zatwierdzania synchronicznego automatyczny tryb failover na SQL-1 i 2 dla programu SQL. Aby skompilować tej architektury, zobacz [szablonu grupy dostępności lub samouczku](virtual-machines-windows-portal-sql-availability-group-overview.md).

Ta architektura jest narażony na czas przestoju, gdy region platformy Azure stanie się niedostępna. Aby wyeliminować tę lukę w zabezpieczeniach, należy dodać repliki w innym regionie platformy Azure. Na poniższym diagramie przedstawiono, jak będzie wyglądać nowej architektury:

   ![Grupa dostępności odzyskiwania po awarii](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Na powyższym diagramie przedstawiono nową maszynę wirtualną o nazwie SQL-3. SQL 3 jest w innym regionie platformy Azure. SQL 3 zostanie dodany do klastra trybu Failover systemu Windows Server. SQL 3 może hostować replika grupy dostępności. Na koniec Zwróć uwagę, że region platformy Azure dla programu SQL 3 zawiera nowe równoważenia obciążenia platformy Azure.

>[!NOTE]
> Zestawu dostępności platformy Azure jest wymagana, gdy więcej niż jedna maszyna wirtualna znajduje się w tym samym regionie. Jeśli jedna maszyna wirtualna jest tylko w regionie, zestaw dostępności nie jest wymagana. Tylko można umieścić maszyny wirtualnej w zestawie w czasie tworzenia dostępności. Jeśli maszyna wirtualna już znajduje się w zestawie dostępności, możesz dodać później dodatkowe repliki maszyny wirtualnej.

W tej architekturze replik w regionie zdalnego zwykle skonfigurowano tryb dostępności zatwierdzania asynchronicznego i ręczny tryb pracy awaryjnej.

W przypadku repliki grupy dostępności na maszynach wirtualnych platformy Azure w różnych regionach platformy Azure, wymaga każdego regionu:

* Brama sieci wirtualnej
* Połączenia bramy sieci wirtualnej

Na poniższym diagramie przedstawiono, jak komunikować się sieci między centrami danych.

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Ta architektura spowoduje naliczenie opłaty za wychodzący danych dla danych replikowanych między regionami platformy Azure. Zobacz [cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Tworzenie zdalnej repliki

Aby utworzyć replikę w centrum danych zdalnego, wykonaj następujące czynności:

1. [Tworzenie sieci wirtualnej w nowym regionie](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna za pomocą witryny Azure portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >W niektórych przypadkach może być konieczne do utworzenia połączenia sieć wirtualna-sieć wirtualna za pomocą programu PowerShell. Na przykład jeśli używasz różnych kont platformy Azure w portalu nie można skonfigurować połączenie. W tym przypadku znajduje się pozycja [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna za pomocą witryny Azure portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Tworzenie kontrolera domeny w nowym regionie](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ten kontroler domeny umożliwia uwierzytelnianie, jeśli kontroler domeny w lokacji głównej nie jest dostępna.

1. [Utwórz maszynę wirtualną programu SQL Server w nowym regionie](virtual-machines-windows-portal-sql-server-provision.md).

1. [Tworzenie modułu równoważenia obciążenia platformy Azure w sieci w nowym regionie](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Ten moduł równoważenia obciążenia musi:

   - Znajdować się w tej samej sieci i podsieci, co nowej maszyny wirtualnej.
   - Ma statyczny adres IP dla odbiornika grupy dostępności.
   - Obejmują puli zaplecza, składający się z tylko maszyny wirtualne w tym samym regionie co moduł równoważenia obciążenia.
   - Użyj określonego adresu IP sondy portu TCP.
   - Mają reguły specyficzne dla programu SQL Server, w tym samym regionie równoważenia obciążenia.  
   - Jeśli maszyny wirtualne w puli zaplecza nie są częścią pojedynczym zestawie dostępności lub zestawie skalowania maszyn wirtualnych, należy być standardowego modułu równoważenia obciążenia. Aby uzyskać dodatkowe informacje, przejrzyj [Omówienie usługi Azure Load Balancer w warstwie standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Dodaj funkcję Klaster trybu Failover na nowy serwer SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Dołącz nowy program SQL Server do domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Ustaw nowe konto usługi programu SQL Server do używania konta domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Dodaj nowy program SQL Server do klastra trybu Failover systemu Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Utwórz zasób adresu IP w klastrze.

   W Menedżerze klastra trybu Failover można utworzyć zasobu adresu IP. Kliknij prawym przyciskiem myszy rolę grupy dostępności, kliknij przycisk **Dodaj zasób**, **więcej zasobów**i kliknij przycisk **adresu IP**.

   ![Utwórz adres IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Skonfiguruj ten adres IP w następujący sposób:

   - Użyj sieci z centrum danych zdalnego.
   - Przypisanie adresu IP z nowego modułu równoważenia obciążenia platformy Azure. 

1. Na nowym serwerze SQL w programie SQL Server Configuration Manager [Włącz zawsze włączone grupy dostępności](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Otwórz porty zapory na nowy serwer SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Numery portów, które należy otworzyć zależą od środowiska. Otwieranie portów dla punktu końcowego dublowania i Azure obciążenia sonda kondycji modułu równoważenia.

1. [Dodaj replikę grupy dostępności na nowym serwerze SQL](https://msdn.microsoft.com/library/hh213239.aspx).

   Dla repliki odległym regionie platformy Azure ustaw ją na replikację asynchroniczną za pomocą ręcznej pracy awaryjnej.  

1. Dodaj zasób adresu IP jako zależność dla klastra (Nazwa sieciowa) punktu dostępu klienta odbiornika.

   Poniższy zrzut ekranu przedstawia prawidłowo skonfigurowane zasobu adresu IP klastra:

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Grupa zasobów klastra zawiera oba adresy IP. Oba adresy IP są zależności dla odbiornika punktu dostępu klienta. Użyj **lub** operatora w konfiguracji klastra zależności.

1. [Ustawianie parametrów klastra w programie PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Uruchom skrypt programu PowerShell przy użyciu nazwy sieciowej klastra, adres IP i port sondy, który został skonfigurowany w module równoważenia obciążenia w nowym regionie.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Ustaw połączenie do wielu podsieci

Repliki w centrum danych zdalnych jest częścią grupy dostępności, ale jest w innej podsieci. Jeśli replika stanie się repliką podstawową, może wystąpić limity czasu połączenia w aplikacji. To zachowanie jest takie same jak w środowisku lokalnym grupy dostępności w ramach wdrożenia wielu podsieci. Umożliwia nawiązywanie połączeń z klienta aplikacji, zaktualizuj połączenie klienta lub skonfigurowanie rozpoznawania nazw, buforowanie od zasobu nazwy sieciowej klastra.

Najlepiej, zaktualizuj ciągi połączeń klienta, aby ustawić `MultiSubnetFailover=Yes`. Zobacz [połączenie z usługą MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Jeśli nie można zmodyfikować parametry połączenia, można skonfigurować buforowanie rozpoznawania nazw. Zobacz [błędem przekroczenia limitu czasu i nie można nawiązać połączenia odbiornika grupy dostępności AlwaysOn programu SQL Server 2012, w środowisku obejmującym wiele podsieci](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Do zdalnego regionu pracy awaryjnej

Aby przetestować łączność odbiornika do zdalnego regionu, możesz przełączać awaryjnie repliki do zdalnego regionu. Replika jest asynchroniczne, pracy awaryjnej jest narażony na ryzyko utraty danych. Do trybu failover bez utraty danych, Zmień tryb dostępności na synchroniczne i ustaw tryb pracy awaryjnej na tryb automatyczny. Wykonaj następujące czynności:

1. W **Eksplorator obiektów**, połącz się z wystąpieniem programu SQL Server, który obsługuje replikę podstawową.
1. W obszarze **zawsze włączonych grup dostępności**, **grup dostępności**, kliknij prawym przyciskiem myszy grupy dostępności i kliknij przycisk **właściwości**.
1. Na **ogólne** w obszarze **replik dostępności**, ustaw repliki pomocniczej w lokacji odzyskiwania po awarii, aby użyć **zatwierdzania synchronicznego** tryb dostępności i  **Automatyczne** tryb pracy awaryjnej.
1. W przypadku repliki pomocniczej w tej samej lokacji co swoje repliki podstawowej w celu zapewnienia wysokiej dostępności, wartość ta replika **zatwierdzania asynchronicznego** i **ręczne**.
1. Kliknij przycisk OK.
1. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy grupę dostępności, a kliknij **wyświetlić pulpit nawigacyjny**.
1. Na pulpicie nawigacyjnym Sprawdź, czy synchronizacji repliki w lokacji odzyskiwania po awarii.
1. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy grupę dostępności, a kliknij **trybu Failover...** . SQL Server Management Studio zostanie otwarty Kreator do trybu failover programu SQL Server.  
1. Kliknij przycisk **dalej**i wybierz wystąpienie programu SQL Server w lokacji odzyskiwania po awarii. Kliknij przycisk **dalej** ponownie.
1. Połącz się z wystąpieniem programu SQL Server w lokacji odzyskiwania po awarii, a następnie kliknij przycisk **dalej**.
1. Na **Podsumowanie** strony, sprawdź ustawienia i kliknij przycisk **Zakończ**.

Po zakończeniu testowania łączności przenieść podstawową replikę z powrotem do głównego centrum danych i ustaw tryb dostępności do ustawień normalnego działania. W poniższej tabeli przedstawiono normalne ustawienia robocze dotyczące architektury opisanej w tym dokumencie:

| Lokalizacja | Wystąpienie serwera | Rola | Tryb dostępności | Tryb pracy awaryjnej
| ----- | ----- | ----- | ----- | -----
| Podstawowe centrum danych | SQL-1 | Podstawowy | Synchroniczne | Automatyczne
| Podstawowe centrum danych | SQL-2 | Pomocniczy | Synchroniczne | Automatyczne
| Centrum danych w dodatkowej lub zdalnym | SQL-3 | Pomocniczy | Asynchroniczne | Ręcznie


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Więcej informacji na temat planowanych i wymuszonego ręcznego przełączania trybu failover

Więcej informacji znajduje się w następujących tematach:

- [Wykonaj planowane ręczna praca awaryjna grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Wykonać wymuszone ręczna praca awaryjna grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Dodatkowe linki

* [Zawsze włączone grupy dostępności](https://msdn.microsoft.com/library/hh510230.aspx)
* [Usługa Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Moduły równoważenia obciążenia platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Zestawy dostępności platformy Azure](../manage-availability.md)
