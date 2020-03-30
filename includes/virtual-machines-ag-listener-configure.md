---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468489"
---
Odbiornik grupy dostępności jest adresem IP i nazwą sieciową, na którą nasłuchuje grupa dostępności programu SQL Server. Aby utworzyć odbiornik grupy dostępności, wykonaj następujące czynności:

1. <a name="getnet"></a>Pobierz nazwę zasobu sieciowego klastra.

    a. Użyj protokołu RDP, aby połączyć się z maszyną wirtualną platformy Azure, która obsługuje replikę podstawową. 

    b. Otwórz Menedżera klastra trybu failover.

    d. Wybierz węzeł **Sieci** i zanotuj nazwę sieci klastra. Użyj tej nazwy `$ClusterNetworkName` w zmiennej w skrypcie programu PowerShell. Na poniższej ilustracji nazwa sieci klastra to **Cluster Network 1**:

   ![Nazwa sieci klastra](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Dodaj punkt dostępu klienta.  
    Punkt dostępu klienta to nazwa sieciowa używana przez aplikacje do łączenia się z bazami danych w grupie dostępności. Utwórz punkt dostępu klienta w Menedżerze klastrów trybu failover.

    a. Rozwiń nazwę klastra, a następnie kliknij pozycję **Role**.

    b. W okienku **Role** kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz polecenie Dodaj**punkt dostępu klienta** **zasobów** > .

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    d. W polu **Nazwa** utwórz nazwę dla tego nowego odbiornika. 
   Nazwa nowego odbiornika to nazwa sieciowa używana przez aplikacje do łączenia się z bazami danych w grupie dostępności programu SQL Server.

    d. Aby zakończyć tworzenie odbiornika, kliknij dwukrotnie przycisk **Dalej,** a następnie kliknij przycisk **Zakończ**. W tym momencie nie należy przewprowadzić odbiornika ani zasobu do trybu online.

1. Przejmij rolę klastra grupy dostępności w tryb offline. W **Menedżerze klastra trybu failover** w obszarze **Role**kliknij prawym przyciskiem myszy rolę i wybierz polecenie **Zatrzymaj rolę**.

1. <a name="congroup"></a>Skonfiguruj zasób IP dla grupy dostępności.

    a. Kliknij kartę **Zasoby,** a następnie rozwiń utworzony punkt dostępu klienta.  
    Punkt dostępu klienta jest w trybie offline.

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kliknij prawym przyciskiem myszy zasób IP, a następnie kliknij polecenie właściwości. Zanotuj nazwę adresu IP i `$IPResourceName` użyj go w zmiennej w skrypcie programu PowerShell.

    d. W obszarze **Adres IP**kliknij pozycję **Statyczny adres IP**. Ustaw adres IP jako ten sam adres, który był używany podczas ustawiania adresu modułu równoważenia obciążenia w witrynie Azure portal.

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Uzależnij zasób grupy dostępności programu SQL Server od punktu dostępu klienta.

    a. W Menedżerze klastrów trybu failover kliknij pozycję **Role**, a następnie kliknij grupę dostępności.

    b. Na karcie **Zasoby** w obszarze **Inne zasoby**kliknij prawym przyciskiem myszy grupę zasobów dostępności, a następnie kliknij polecenie **Właściwości**. 

    d. Na karcie zależności dodaj nazwę zasobu punktu dostępu klienta (odbiornika).

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kliknij przycisk **OK**.

1. <a name="listname"></a>Uzależnij zasób punktu dostępu klienta od adresu IP.

    a. W Menedżerze klastrów trybu failover kliknij pozycję **Role**, a następnie kliknij grupę dostępności. 

    b. Na karcie **Zasoby** kliknij prawym przyciskiem myszy zasób punktu dostępu klienta w obszarze **Nazwa serwera,** a następnie kliknij polecenie **Właściwości**. 

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    d. Kliknij kartę **Zależności.** Sprawdź, czy adres IP jest zależnością. Jeśli tak nie jest, ustaw zależność od adresu IP. Jeśli na liście znajduje się wiele zasobów, sprawdź, czy adresy IP mają zależności LUB, a nie i. Kliknij przycisk **OK**. 

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Można sprawdzić, czy zależności są poprawnie skonfigurowane. W Menedżerze klastrów trybu failover przejdź do funkcji Role, kliknij prawym przyciskiem myszy grupę dostępności, kliknij polecenie **Więcej akcji**, a następnie kliknij polecenie Pokaż **raport zależności**. Gdy zależności są poprawnie skonfigurowane, grupa dostępności jest zależna od nazwy sieci, a nazwa sieciowa jest zależna od adresu IP. 


1. <a name="setparam"></a>Ustawianie parametrów klastra w programie PowerShell.

   a. Skopiuj następujący skrypt programu PowerShell do jednego z wystąpień programu SQL Server. Zaktualizuj zmienne dla swojego środowiska.

   - `$ListenerILBIP`to adres IP utworzony na modułu równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności.
    
   - `$ListenerProbePort`to port skonfigurowany na modułu równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ustaw parametry klastra, uruchamiając skrypt programu PowerShell w jednym z węzłów klastra.  

   > [!NOTE]
   > Jeśli wystąpienia programu SQL Server znajdują się w oddzielnych regionach, należy dwukrotnie uruchomić skrypt programu PowerShell. Za pierwszym razem `$ListenerILBIP` użyj `$ListenerProbePort` i z pierwszego regionu. Za drugim razem `$ListenerILBIP` użyj `$ListenerProbePort` i z drugiego regionu. Nazwa sieci klastra i nazwa zasobu IP klastra są również różne dla każdego regionu.

1. Przewiń rolę klastra grupy dostępności w tryb online. W **Menedżerze klastrów trybu failover** w obszarze **Role**kliknij prawym przyciskiem myszy tę rolę i wybierz polecenie **Uruchom rolę**.

W razie potrzeby powtórz powyższe kroki, aby ustawić parametry klastra dla adresu IP klastra WSFC.

1. Pobierz nazwę adresu IP adresu IP klastra WSFC. W **Menedżerze klastrów trybu failover** w obszarze **Podstawowe zasoby klastra**znajdź nazwę **serwera**.

1. Kliknij prawym przyciskiem myszy pozycję **Adres IP**i wybierz polecenie **Właściwości**.

1. Skopiuj **nazwę** adresu IP. Może to `Cluster IP Address`być . 

1. <a name="setwsfcparam"></a>Ustawianie parametrów klastra w programie PowerShell.
  
   a. Skopiuj następujący skrypt programu PowerShell do jednego z wystąpień programu SQL Server. Zaktualizuj zmienne dla swojego środowiska.

   - `$ClusterCoreIP`to adres IP utworzony na modułu równoważenia obciążenia platformy Azure dla zasobu klastra podstawowego WSFC. Różni się od adresu IP dla odbiornika grupy dostępności.

   - `$ClusterProbePort`to port skonfigurowany na moduł równoważenia obciążenia platformy Azure dla sondy kondycji WSFC. Różni się od sondy dla odbiornika grupy dostępności.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ustaw parametry klastra, uruchamiając skrypt programu PowerShell w jednym z węzłów klastra.  

>[!WARNING]
>Port sondy kondycji odbiornika grupy dostępności musi się różnić od portu sondy kondycji adresów IP rdzenia klastra. W tych przykładach port odbiornika jest 59999 i portu sondy kondycji adresów IP rdzenia klastra jest 58888. Oba porty wymagają reguły zapory przychodzącej.
