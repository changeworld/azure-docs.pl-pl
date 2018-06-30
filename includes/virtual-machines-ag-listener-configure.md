Odbiornik grupy dostępności jest adresu IP i sieci nazwy który grupy dostępności programu SQL Server nasłuchuje. Aby utworzyć odbiornika grupy dostępności, wykonaj następujące czynności:

1. <a name="getnet"></a>Pobierz nazwę klastra zasobu sieciowego.

    a. Podłącz do maszyny wirtualnej platformy Azure, który obsługuje replikę podstawową za pomocą protokołu RDP. 

    b. Otwórz Menedżera klastra pracy awaryjnej.

    c. Wybierz **sieci** węzeł i Uwaga Nazwa sieciowa klastra. Użyj tej nazwy w `$ClusterNetworkName` zmiennych w skrypcie programu PowerShell. Na poniższej ilustracji jest nazwa sieciowa klastra **1 sieci klastra**:

   ![Nazwa sieciowa klastra](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Dodaj punkt dostępu klienta.  
    Punkt dostępu klienta jest nazwą sieci aplikacje używają do nawiązania połączenia bazy danych w grupie dostępności. Utwórz punkt dostępu klienta w Menedżerze klastra trybu Failover.

    a. Rozwiń nazwę klastra, a następnie kliknij przycisk **ról**.

    b. W **ról** okienku kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz **dodawania zasobów** > **punktu dostępu klienta**.

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. W **nazwa** Utwórz nazwę dla tego nowego odbiornika. 
   Nazwa nowej odbiornika jest nazwę sieciową, której aplikacje używają do nawiązania połączenia bazy danych w grupie dostępności programu SQL Server.
   
    d. Aby zakończyć tworzenie odbiornika, kliknij przycisk **dalej** dwa razy, a następnie kliknij przycisk **Zakończ**. Nie powodują odbiornika lub zasób w tryb online w tym momencie.

3. <a name="congroup"></a>Konfigurowanie zasobów adresu IP dla grupy dostępności.

    a. Kliknij przycisk **zasobów** karcie, a następnie rozwiń utworzony punkt dostępu klienta.  
    Punkt dostępu klienta działa w trybie offline.

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kliknij prawym przyciskiem myszy zasób IP, a następnie kliknij polecenie Właściwości. Zanotuj nazwę adres IP i używać go w `$IPResourceName` zmiennych w skrypcie programu PowerShell.

    c. W obszarze **adres IP**, kliknij przycisk **statyczny adres IP**. Ustaw adres IP jako ten sam adres używany podczas ustawiania adres usługi równoważenia obciążenia w portalu Azure.

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Należy zasób grupy dostępności programu SQL Server jest zależny od punktu dostępu klienta.

    a. W Menedżerze klastra trybu Failover kliknij **ról**, a następnie kliknij przycisk grupy dostępności.

    b. Na **zasobów** , w obszarze **inne zasoby**, kliknij prawym przyciskiem myszy grupę dostępności zasobów, a następnie kliknij przycisk **właściwości**. 

    c. Na karcie zależności Dodaj nazwę zasobu (odbiornika) punktu dostępu klienta.

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kliknij przycisk **OK**.

5. <a name="listname"></a>Sprawdź zależne od adresu IP zasobu punktu dostępu klienta.

    a. W Menedżerze klastra trybu Failover kliknij **ról**, a następnie kliknij przycisk grupy dostępności. 

    b. Na **zasobów** karcie, kliknij prawym przyciskiem myszy zasób punktu dostępu klienta w **nazwy serwera**, a następnie kliknij przycisk **właściwości**. 

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kliknij przycisk **zależności** kartę. Sprawdź, czy adres IP jest zależności. Jeśli nie, należy ustawić zależności na adres IP. Jeśli istnieje wiele zasobów na liście, sprawdź, czy adresy IP są lub nie oraz zależności. Kliknij przycisk **OK**. 

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij przycisk **przejdź do trybu Online**. 

    >[!TIP]
    >Aby zweryfikować, że zależności są poprawnie skonfigurowane. W Menedżerze klastra trybu Failover, przejdź do ról, kliknij prawym przyciskiem myszy grupę dostępności, kliknij przycisk **więcej akcji**, a następnie kliknij przycisk **Pokaż raport zależności**. Gdy zależności są poprawnie skonfigurowane, grupa dostępności jest zależna od nazwy sieci, a nazwa sieciowa jest zależny od adresu IP. 


6. <a name="setparam"></a>Ustaw parametry klastra w programie PowerShell.
    
    a. Skopiuj poniższy skrypt programu PowerShell do jednego wystąpienia programu SQL Server. Zaktualizuj zmienne w danym środowisku.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Ustaw parametry klastra przez uruchomienie skryptu programu PowerShell na jednym z węzłów klastra.  

Powtórz powyższe kroki, aby ustawić parametry klastra dla adresu IP klastra usługi WSFC.

1. Pobierz nazwę adres IP adres IP klastra usługi WSFC. W **Menedżera klastra trybu Failover** w obszarze **zasoby podstawowe klastra**, zlokalizuj **nazwy serwera**.

1. Kliknij prawym przyciskiem myszy **adres IP**i wybierz **właściwości**.

1. Kopiuj **nazwa** adresu IP. Być może jest ona `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Ustaw parametry klastra w programie PowerShell.
    
    a. Skopiuj poniższy skrypt programu PowerShell do jednego wystąpienia programu SQL Server. Zaktualizuj zmienne w danym środowisku.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
    $ILBIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Ustaw parametry klastra przez uruchomienie skryptu programu PowerShell na jednym z węzłów klastra.  

    > [!NOTE]
    > Wystąpienia programu SQL Server znajdują się w oddzielnych regionach, należy uruchomić skrypt programu PowerShell dwukrotnie. Po raz pierwszy, użyj `$ILBIP` i `$ProbePort` z pierwszego regionu. Następnie należy użyć `$ILBIP` i `$ProbePort` z drugiego regionu. Nazwa sieciowa klastra i nazwę zasobu klastra IP są takie same. 
