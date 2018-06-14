---
title: Grupy dostępności serwera SQL, odzyskiwania po awarii — maszyn wirtualnych platformy Azure — | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania grupy dostępności programu SQL Server na maszynach wirtualnych Azure z repliką w innym regionie.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: 84fa2e051c46e178e3e72709886babc8c3db7b9d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852833"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Konfigurowanie zawsze włączonej grupy dostępności na maszynach wirtualnych Azure w różnych regionach

W tym artykule opisano sposób konfigurowania programu SQL Server AlwaysOn repliki grupy dostępności na maszynach wirtualnych Azure w lokalizacji zdalnej platformy Azure. Użyj tej konfiguracji do obsługi odzyskiwania po awarii.

Ten artykuł dotyczy na maszynach wirtualnych platformy Azure w trybie Menedżera zasobów.

Na poniższej ilustracji przedstawiono typowe wdrożenie grupy dostępności na maszynach wirtualnych Azure:

   ![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

W tym wdrożeniu wszystkie maszyny wirtualne znajdują się w jednym regionie Azure. Repliki grupy dostępności może mieć zatwierdzanie synchroniczne z automatycznej pracy awaryjnej na SQL-1 i 2 dla programu SQL. Aby utworzyć tej architektury, zobacz [szablonu grupy dostępności lub samouczek](virtual-machines-windows-portal-sql-availability-group-overview.md).

Taka architektura jest narażony na Przestój, gdy region platformy Azure stanie się niedostępna. Aby rozwiązać tę lukę w zabezpieczeniach, należy dodać repliki w innym regionie Azure. Na poniższym diagramie przedstawiono, jak będzie wyglądać architektura:

   ![DR grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Na powyższym diagramie przedstawiono nowej maszyny wirtualnej o nazwie SQL 3. SQL 3 jest w innym regionie Azure. SQL 3 jest dodawany do klastra trybu Failover systemu Windows Server. SQL 3 może obsługiwać repliki grupy dostępności. Warto zauważyć, że region platformy Azure dla programu SQL 3 ma nowego modułu równoważenia obciążenia Azure.

>[!NOTE]
> Zestaw dostępności Azure jest wymagany, gdy więcej niż jednej maszyny wirtualnej jest w tym samym regionie. Jeśli tylko jednej maszyny wirtualnej znajduje się w regionie, zestaw dostępności nie jest wymagane. Maszyny wirtualne można umieścić tylko w zestawie w czasie tworzenia dostępności. Jeśli maszyna wirtualna już znajduje się w zestawie dostępności, możesz dodać maszyny wirtualnej do dodatkowej repliki później.

W ramach tej architektury replik w zdalnym regionie zwykle skonfigurowano tryb dostępności zatwierdzania asynchronicznego i ręczny tryb pracy awaryjnej.

W przypadku replik grupy dostępności na maszynach wirtualnych Azure w różnych regionach platformy Azure, wymaga każdego regionu:

* Brama sieci wirtualnej
* Połączenie bramy sieci wirtualnej

Na poniższym diagramie przedstawiono sposób sieci komunikacji między centrami danych.

   ![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Taka architektura wiąże się z opłatami za przesyłanie danych wychodzących dla danych replikowanych między regiony platformy Azure. Zobacz [przepustowości ceny](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Utwórz zdalnej repliki

Aby utworzyć replikę w centrum danych zdalnych, wykonaj następujące czynności:

1. [Tworzenie sieci wirtualnej w nowym regionie](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurowanie połączenia do wirtualnymi przy użyciu portalu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >W niektórych przypadkach może być konieczne do utworzenia połączenia do wirtualnymi za pomocą programu PowerShell. Na przykład użycie innego konta platformy Azure w portalu nie można skonfigurować połączenie. W takim przypadku wyświetlony [skonfigurować połączenia do wirtualnymi przy użyciu portalu Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Tworzenie kontrolera domeny w nowym regionie](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ten kontroler domeny zapewnia uwierzytelnianie, jeśli kontroler domeny w lokacji głównej nie jest dostępny.

1. [Utwórz maszynę wirtualną programu SQL Server w nowym regionie](virtual-machines-windows-portal-sql-server-provision.md).

1. [Utwórz moduł równoważenia obciążenia Azure w sieci na nowy region](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Ten moduł równoważenia obciążenia musi:

   - Można w tej samej sieci i podsieci jako nowej maszyny wirtualnej.
   - Ma statyczny adres IP dla odbiornika grupy dostępności.
   - Obejmują puli zaplecza, składające się z tylko maszyny wirtualne w tym samym regionie co moduł równoważenia obciążenia.
   - Za pomocą sondowania port TCP specyficzne dla adresu IP.
   - Ma specyficzne dla serwera SQL, w tym samym regionie reguły równoważenia obciążenia.  

1. [Dodaj funkcję Klaster pracy awaryjnej na nowy serwer SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Dołącz nowy serwer SQL do domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Ustaw nowe konto usługi programu SQL Server do używania konta domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Dodaj nowy serwer SQL do klastra trybu Failover systemu Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Utwórz zasób adresu IP w klastrze.

   W Menedżerze klastra trybu Failover można utworzyć zasobu adresu IP. Kliknij prawym przyciskiem myszy rolę grupy dostępności, kliknij przycisk **dodawania zasobów**, **więcej zasobów**i kliknij przycisk **adres IP**.

   ![Utwórz adres IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Ten adres IP należy skonfigurować w następujący sposób:

   - Używać sieci z centrum danych zdalnych.
   - Przypisz adres IP z nowej usługi równoważenia obciążenia Azure. 

1. Na nowym serwerze SQL w programie SQL Server Configuration Manager [Włącz zawsze włączone grupy dostępności](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Otworzyć porty zapory na nowym serwerze SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Numery portów, które należy otworzyć zależą od środowiska. Otwieranie portów dla punktu końcowego dublowania i Azure załadować sondy kondycji modułu równoważenia.

1. [Dodaj replikę grupy dostępności na nowym serwerze SQL](http://msdn.microsoft.com/library/hh213239.aspx).

   Dla replik w zdalnym regionie Azure należy ustawić dla asynchronicznego replikacji z ręcznego przełączania trybu failover.  

1. Dodaj zasób adresu IP jako zależność klastra (Nazwa sieciowa) punktu dostępu klienta odbiornika.

   Poniższy zrzut ekranu przedstawia poprawnego skonfigurowania zasobu klastra adresu IP:

   ![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Grupa zasobów klastra zawiera oba adresy IP. Oba adresy IP są zależności dla odbiornika punktu dostępu klienta. Użyj **lub** operatora w konfiguracji klastra zależności.

1. [Ustaw parametry klastra w programie PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Uruchom skrypt programu PowerShell z nazwy sieciowej klastra, adres IP i port sondy, skonfigurowanego w regionie nowego modułu równoważenia obciążenia.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Ustaw połączenie dla wielu podsieci

Repliki w centrum danych zdalnych jest częścią grupy dostępności, ale jest w innej podsieci. Jeśli replika stanie się repliką podstawową, limity czasu połączenia aplikacji mogą wystąpić. To zachowanie jest takie same lokalne grupy dostępności w ramach wdrożenia wielu podsieci. Umożliwia nawiązywanie połączeń z klienta aplikacji, zaktualizuj połączenie klienta lub skonfigurowania rozpoznawania nazw buforowanie na zasobu nazwy sieciowej klastra.

Najlepiej, zaktualizuj parametry połączenia klienta, aby ustawić `MultiSubnetFailover=Yes`. Zobacz [nawiązywania połączenia z MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

Jeśli nie można zmodyfikować parametry połączenia, można skonfigurować buforowanie rozpoznawania nazw. Zobacz [przekroczeń limitu czasu połączenia w grupie dostępności wielu podsieci](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Przełączyć zdalnego regionu

Aby przetestować połączenie odbiornika zdalnego regionu, można przełączyć repliki w celu zdalnego regionu. Replika jest asynchroniczne, pracy awaryjnej jest narażone na ryzyko utraty danych. Do trybu failover bez utraty danych, Zmień tryb dostępności na synchroniczne i ustaw tryb pracy awaryjnej na automatyczny. Wykonaj następujące czynności:

1. W **Eksplorator obiektów**, połącz się z wystąpieniem programu SQL Server, który obsługuje replikę podstawową.
1. W obszarze **zawsze włączonych grup dostępności**, **grup dostępności**, kliknij prawym przyciskiem myszy tej grupy dostępności i kliknij przycisk **właściwości**.
1. Na **ogólne** w obszarze **replik dostępności**, ustaw repliki pomocniczej w lokacji odzyskiwania po awarii, aby użyć **zatwierdzania synchronicznego** tryb dostępności i  **Automatyczne** tryb pracy awaryjnej.
1. Jeśli masz repliki pomocniczej w tej samej lokacji co repliki podstawowej wysokiej dostępności, wartość ta replika **zatwierdzania asynchronicznego** i **ręcznego**.
1. Kliknij przycisk OK.
1. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy grupę dostępności i kliknij przycisk **Pokaż pulpit nawigacyjny**.
1. Na pulpicie nawigacyjnym Sprawdź, czy synchronizacji repliki w lokacji odzyskiwania po awarii.
1. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy grupę dostępności i kliknij przycisk **pracy awaryjnej...** . SQL Server Management Studio zostanie otwarty Kreator do pracy awaryjnej programu SQL Server.  
1. Kliknij przycisk **dalej**i wybierz wystąpienie programu SQL Server w lokacji odzyskiwania po awarii. Kliknij przycisk **dalej** ponownie.
1. Połącz z wystąpieniem programu SQL Server w lokacji odzyskiwania po awarii, a następnie kliknij przycisk **dalej**.
1. Na **Podsumowanie** , sprawdź ustawienia i kliknij przycisk **Zakończ**.

Po zakończeniu testowania łączności, przenieść podstawową replikę z powrotem do centrum danych głównej i ustaw tryb dostępności do ustawień normalnego działania. W poniższej tabeli przedstawiono normalne ustawienia robocze dotyczące architektury opisanej w tym dokumencie:

| Lokalizacja | Wystąpienie serwera | Rola | Tryb dostępności | Tryb pracy awaryjnej
| ----- | ----- | ----- | ----- | -----
| Centrum danych podstawowych | SQL-1 | Podstawowy | Synchroniczne | Automatyczny
| Centrum danych podstawowych | SQL-2 | Pomocniczy | Synchroniczne | Automatyczny
| Centrum danych w dodatkowej lub zdalnego | SQL-3 | Pomocniczy | Asynchroniczne | Ręczne


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Więcej informacji na temat planowanymi, jak i wymuszonego ręcznego przełączania trybu failover

Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Wykonaj planowane ręcznego przełączania trybu Failover grupy dostępności (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Wykonaj wymuszonego ręcznego przełączania trybu Failover grupy dostępności (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Dodatkowe linki

* [Zawsze włączone grupy dostępności](http://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Moduły równoważenia obciążenia Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Zestawy dostępności Azure](../manage-availability.md)
