---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 276ddf0a70fa450451cd3ddc78c7610c4ab1edc1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58494828"
---
Odbiornik grupy dostępności jest nazwa i adres IP sieci, grupy dostępności programu SQL Server nasłuchuje. Aby utworzyć odbiornik grupy dostępności, wykonaj następujące czynności:

1. <a name="getnet"></a>Pobierz nazwę zasobu sieci klastra.

    a. Użyj protokołu RDP, aby nawiązać połączenie z maszyny wirtualnej platformy Azure, który obsługuje replikę podstawową. 

    b. Otwórz Menedżera klastra trybu Failover.

    c. Wybierz **sieci** węzła i zanotuj nazwy sieciowej klastra. Należy użyć tej nazwy w `$ClusterNetworkName` zmiennych w skrypcie programu PowerShell. Na poniższej ilustracji Nazwa sieciowa klastra jest **klastra sieci 1**:

   ![Nazwa sieciowa klastra](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Dodaj punkt dostępu klienta.  
    Punkt dostępu klienta jest nazwą sieci, które aplikacje korzystają z bazami danych w grupie dostępności. Utwórz punkt dostępu klienta w Menedżerze klastra trybu Failover.

    a. Rozwiń nazwę klastra, a następnie kliknij przycisk **role**.

    b. W **role** okienku kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz **Dodaj zasób** > **punktu dostępu klienta**.

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. W **nazwa** Utwórz nazwę dla tego nowego odbiornika. 
   Nazwa dla nowego odbiornika jest nazwa sieci, które aplikacje korzystają z bazami danych w grupie dostępności programu SQL Server.

    d. Aby zakończyć tworzenie odbiornika, kliknij przycisk **dalej** dwa razy, a następnie kliknij przycisk **Zakończ**. Nie należy przełączać odbiornika lub zasób w tryb online w tym momencie.

1. Przełącz rolę klastra grupy dostępności do trybu offline. W **Menedżera klastra trybu Failover** w obszarze **role**, kliknij prawym przyciskiem myszy rolę i wybierz **Zatrzymaj rolę**.

1. <a name="congroup"></a>Skonfiguruj zasób adresu IP dla grupy dostępności.

    a. Kliknij przycisk **zasobów** kartę, a następnie rozwiń utworzony punkt dostępu klienta.  
    Punkt dostępu klienta jest w trybie offline.

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kliknij prawym przyciskiem myszy zasób adresu IP, a następnie kliknij polecenie Właściwości. Zanotuj nazwę adresu IP i używać go w `$IPResourceName` zmiennych w skrypcie programu PowerShell.

    c. W obszarze **adresu IP**, kliknij przycisk **statyczny adres IP**. Ustaw adres IP jako ten sam adres, która została użyta podczas ustawisz adres usługi równoważenia obciążenia w witrynie Azure portal.

   ![Zasób adresu IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Zasób grupy dostępności programu SQL Server należy zależne od punktu dostępu klienta.

    a. W Menedżerze klastra trybu Failover kliknij **role**, a następnie kliknij pozycję grupy dostępności.

    b. Na **zasobów** , w obszarze **inne zasoby**, kliknij prawym przyciskiem myszy grupę dostępności zasobów, a następnie kliknij przycisk **właściwości**. 

    c. Na karcie zależności należy dodać nazwę zasobu (odbiornika) punktu dostępu klienta.

   ![Zasób adresu IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kliknij przycisk **OK**.

1. <a name="listname"></a>Marka zależne od adresu IP zasobu punktu dostępu klienta.

    a. W Menedżerze klastra trybu Failover kliknij **role**, a następnie kliknij pozycję grupy dostępności. 

    b. Na **zasobów** kartę, kliknij prawym przyciskiem myszy zasób punktu dostępu klienta w obszarze **nazwy serwera**, a następnie kliknij przycisk **właściwości**. 

   ![Zasób adresu IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kliknij przycisk **zależności** kartę. Sprawdź, czy adres IP jest zależność. Jeśli nie jest, należy ustawić zależność dla adresu IP. W przypadku wielu zasobów, na liście, sprawdź, czy adresy IP są lub nie oraz zależności. Kliknij przycisk **OK**. 

   ![Zasób adresu IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Aby zweryfikować, czy zależności są poprawnie skonfigurowane. W Menedżerze klastra trybu Failover, przejdź do ról, kliknij prawym przyciskiem myszy grupę dostępności, kliknij przycisk **więcej akcji**, a następnie kliknij przycisk **Pokaż raport zależności**. Gdy zależności są poprawnie skonfigurowane, grupa dostępności jest zależna od nazwy sieci, a nazwa sieciowa jest zależny od adresu IP. 


1. <a name="setparam"></a>Ustaw parametry klastra w programie PowerShell.

   a. Skopiuj poniższy skrypt programu PowerShell do jednego wystąpienia programu SQL Server. Zaktualizuj zmienne w danym środowisku.

   - `$ListenerILBIP` to adres IP, który został utworzony w usłudze równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności.
    
   - `$ListenerProbePort` jest to port skonfigurowany w module równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ustaw parametry klastra, uruchamiając skrypt programu PowerShell na jednym z węzłów klastra.  

   > [!NOTE]
   > Wystąpień programu SQL Server znajdują się w oddzielnych regionach, należy uruchomić skrypt programu PowerShell, dwa razy. Po raz pierwszy, użyj `$ListenerILBIP` i `$ListenerProbePort` z pierwszym regionie. Następnie należy użyć `$ListenerILBIP` i `$ListenerProbePort` z drugiego regionu. Nazwa sieciowa klastra i nazwę zasobu adresu IP klastra są również inne dla każdego regionu.

1. Przenieś rolę klastra grupy dostępności online. W **Menedżera klastra trybu Failover** w obszarze **role**, kliknij prawym przyciskiem myszy rolę i wybierz **Uruchom rolę**.

W razie potrzeby powtórz powyższe kroki, aby ustawić parametry klastra dla adresu IP klastra usługi WSFC.

1. Pobierz nazwę adres IP adres IP klastra usługi WSFC. W **Menedżera klastra trybu Failover** w obszarze **zasoby podstawowe klastra**, zlokalizuj **nazwy serwera**.

1. Kliknij prawym przyciskiem myszy **adresu IP**i wybierz **właściwości**.

1. Kopiuj **nazwa** adresu IP. Może być `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Ustaw parametry klastra w programie PowerShell.
  
   a. Skopiuj poniższy skrypt programu PowerShell do jednego wystąpienia programu SQL Server. Zaktualizuj zmienne w danym środowisku.

   - `$ClusterCoreIP` to adres IP, który został utworzony w usłudze równoważenia obciążenia platformy Azure dla zasobu klastra usługi WSFC core. Różni się od adresu IP dla odbiornika grupy dostępności.

   - `$ClusterProbePort` jest to port skonfigurowany w module równoważenia obciążenia platformy Azure dla sondy kondycji usługi WSFC. Różni się od sondy dla odbiornika grupy dostępności.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ustaw parametry klastra, uruchamiając skrypt programu PowerShell na jednym z węzłów klastra.  

>[!WARNING]
>Port sondy kondycji odbiornika grupy dostępności musi być inna niż port sondy kondycji adres IP klastra core. W tych przykładach na port odbiornika jest 59999 i adres IP klastra core jest 58888. Oba porty wymagają reguła zapory dla ruchu przychodzącego.