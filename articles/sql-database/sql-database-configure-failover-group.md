---
title: Konfigurowanie grupy trybu failover
description: Dowiedz się, jak skonfigurować grupę automatycznego trybu failover dla pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego przy użyciu portalu Azure, interfejsu wiersza polecenia Az i programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461814"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurowanie grupy trybu failover dla bazy danych SQL usługi Azure

W tym temacie opisano, jak skonfigurować [grupę auto-failover](sql-database-auto-failover-group.md) dla pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego przy użyciu witryny Azure portal lub programu PowerShell. 

## <a name="single-database"></a>Pojedyncza baza danych
Utwórz grupę trybu failover i dodaj do niej pojedynczą bazę danych przy użyciu portalu Azure portal lub programu PowerShell.

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Ustawienia logowania serwera i zapory serwera pomocniczego muszą być zgodne z ustawieniami serwera podstawowego. 

### <a name="create-failover-group"></a>Tworzenie grupy trybu failover

# <a name="portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover i dodaj do niej pojedynczą bazę danych za pomocą witryny Azure Portal.


1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych, którą chcesz dodać do grupy trybu failover. 
1. Wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz pozycję **Dodaj grupę,** aby utworzyć nową grupę trybu failover. 

    ![Dodawanie nowej grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz wymagane wartości, a następnie wybierz pozycję **Utwórz**.

   - **Bazy danych w grupie:** Wybierz bazę danych, którą chcesz dodać do grupy trybu failover. Dodanie bazy danych do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej. 
        
    ![Dodawanie bazy danych SQL do grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Utwórz grupę trybu failover i dodaj do niej pojedynczą bazę danych za pomocą programu PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Testowanie pracy w trybie failover 

Przetestuj fazę failover grupy trybu failover przy użyciu witryny Azure portal lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testowanie pracy awaryjnej grupy trybu failover przy użyciu witryny Azure portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych, którą chcesz dodać do grupy trybu failover. 

   ![Otwórz serwer dla pojedynczej db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz utworzoną grupę trybu failover. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy, a który pomocniczy. 
1. Wybierz opcję **Praca awaryjna** z okienka zadań, aby przejść w tryb failover grupy trybu failover zawierającej pojedynczą bazę danych. 
1. Wybierz **pozycję Tak** w ostrzeżeniu, które powiadamia o rozłączeniu sesji TDS. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest teraz podstawowy, a który pomocniczy. Jeśli przewija się w trybie failover, dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **opcję Przewijanie awaryjne,** aby serwery zostały ponownie powięksne do ich pierwotnie ról. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Przetestuj przewijaniec awaryjny grupy trybu failover przy użyciu programu PowerShell.  

Sprawdź rolę repliki pomocniczej: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Przeładuj awaryjnie do serwera pomocniczego: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Jeśli chcesz usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie. 

## <a name="elastic-pool"></a>Elastyczna pula
Utwórz grupę trybu failover i dodaj do niej pulę elastyczną za pomocą portalu Azure lub programu PowerShell.  

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Ustawienia logowania serwera i zapory serwera pomocniczego muszą być zgodne z ustawieniami serwera podstawowego. 

### <a name="create-the-failover-group"></a>Tworzenie grupy trybu failover 

Utwórz grupę trybu failover dla puli elastycznej przy użyciu portalu Azure lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover i dodaj do niej pulę elastyczną za pomocą witryny Azure Portal.

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover. 
1. W okienku **Przegląd** wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.
  
    ![Otwarty serwer dla puli elastycznej](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz pozycję **Dodaj grupę,** aby utworzyć nową grupę trybu failover. 

    ![Dodawanie nowej grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz wymagane wartości, a następnie wybierz pozycję **Utwórz**. Utwórz nowy serwer pomocniczy lub wybierz istniejący serwer pomocniczy. 

1. Wybierz **pozycję Bazy danych w grupie,** a następnie wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover. Jeśli pula elastyczna nie istnieje jeszcze na serwerze pomocniczym, zostanie wyświetlone ostrzeżenie z monitem o utworzenie puli elastycznej na serwerze pomocniczym. Wybierz ostrzeżenie, a następnie wybierz **przycisk OK,** aby utworzyć pulę elastyczną na serwerze pomocniczym. 
        
    ![Dodawanie puli elastycznej do grupy trybu failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Wybierz **pozycję Wybierz,** aby zastosować ustawienia puli elastycznej do grupy trybu failover, a następnie wybierz pozycję **Utwórz,** aby utworzyć grupę trybu failover. Dodanie puli elastycznej do grupy trybu failover spowoduje automatyczne rozpoczęcie procesu replikacji geograficznej. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz grupę trybu failover i dodaj do niej pulę elastyczną za pomocą programu PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Testowanie pracy w trybie failover

Przetestuj przełączenie w stan failover puli elastycznej przy użyciu witryny Azure portal lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie po awarii przy użyciu witryny Azure portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover. 
1. W okienku **Przegląd** wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.
  
    ![Otwarty serwer dla puli elastycznej](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz grupę trybu failover utworzoną w sekcji 2. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy, a który pomocniczy. 
1. Wybierz opcję **Praca awaryjna** z okienka zadań, aby przejść w stan failover grupy trybu failover zawierającej pulę elastyczną. 
1. Wybierz **pozycję Tak** w ostrzeżeniu, które powiadamia o rozłączeniu sesji TDS. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest podstawowy, który serwer jest pomocniczy. Jeśli przewija się w trybie failover, dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **opcję Praca awaryjna,** aby grupa trybu failover wróciła do oryginalnych ustawień. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Przetestuj przewijaniec awaryjny grupy trybu failover przy użyciu programu PowerShell.

Sprawdź rolę repliki pomocniczej: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Przeładuj awaryjnie do serwera pomocniczego: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Jeśli chcesz usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie. 

## <a name="managed-instance"></a>Wystąpienie zarządzane

Utwórz grupę trybu failover między dwoma wystąpieniami zarządzanymi przy użyciu portalu Azure lub programu PowerShell. 

Konieczne będzie skonfigurowanie usługi [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) lub utworzenie bramy dla sieci wirtualnej każdego wystąpienia zarządzanego, połączenie dwóch bram, a następnie utworzenie grupy trybu failover. 

### <a name="prerequisites"></a>Wymagania wstępne
Należy wziąć pod uwagę następujące wymagania wstępne:

- Pomocnicze wystąpienie zarządzane musi być puste.
- Zakres podsieci dla pomocniczej sieci wirtualnej nie może nakładać się na zakres podsieci podstawowej sieci wirtualnej. 
- Sortowanie i strefa czasowa wystąpienia pomocniczego musi być zgodna z wystąpieniem podstawowym. 
- Podczas łączenia dwóch bram **klucz udostępniony** powinien być taki sam dla obu połączeń. 

### <a name="create-primary-virtual-network-gateway"></a>Tworzenie podstawowej bramy sieci wirtualnej 

Jeśli usługa [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)nie została skonfigurowana, można utworzyć podstawową bramę sieci wirtualnej za pomocą portalu Azure lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz podstawową bramę sieci wirtualnej przy użyciu witryny Azure portal. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do grupy zasobów i wybierz zasób **sieci wirtualnej** dla podstawowego wystąpienia zarządzanego. 
1. Wybierz **podsieci** w obszarze **Ustawienia,** a następnie wybierz pozycję, aby dodać nową **podsieć bramy**. Pozostaw wartości domyślne. 

   ![Dodaj bramę dla podstawowego wystąpienia zarządzanego](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po utworzeniu bramy podsieci wybierz pozycję **Utwórz zasób** z lewego okienka nawigacji, a następnie wpisz `Virtual network gateway` pole wyszukiwania. Wybierz zasób **bramy sieci wirtualnej** opublikowany przez **firmę Microsoft**. 

   ![Tworzenie nowej bramy sieci wirtualnej](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Wypełnij pola wymagane do skonfigurowania bramy podstawowego wystąpienia zarządzanego. 

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla podstawowego wystąpienia zarządzanego:
 
    | **Pole** | Wartość |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Nazwa** | Nazwa bramy sieci wirtualnej. | 
    | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
    | **Typ bramy** | wybierz pozycję **VPN**. |
    | **Typ sieci VPN** | Wybierz **trasę opartą na trasie** |
    | **Numer jednostki magazynowej**| Pozostaw `VpnGw1`domyślnie . |
    | **Lokalizacja**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i pomocnicza sieć wirtualna.   |
    | **Sieć wirtualna**| Wybierz sieć wirtualną pomocniczego wystąpienia zarządzanego. |
    | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
    | **Nazwa publicznego adresu IP**| Wprowadź nazwę swojego adresu IP. |
    | &nbsp; | &nbsp; |

1. Pozostaw inne wartości jako domyślne, a następnie wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć ustawienia bramy sieci wirtualnej.

   ![Ustawienia bramy podstawowej](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć nową bramę sieci wirtualnej. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz podstawową bramę sieci wirtualnej przy użyciu programu PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Tworzenie pomocniczej bramy sieci wirtualnej

Utwórz pomocniczą bramę sieci wirtualnej przy użyciu portalu Azure lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Powtórz kroki opisane w poprzedniej sekcji, aby utworzyć podsieć sieci wirtualnej i bramę pomocniczego wystąpienia zarządzanego. Wypełnij pola wymagane do skonfigurowania bramy pomocniczego wystąpienia zarządzanego. 

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla pomocniczego wystąpienia zarządzanego:

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której znajduje się pomocnicze wystąpienie zarządzane. |
   | **Nazwa** | Nazwa bramy sieci wirtualnej, `secondary-mi-gateway`na przykład . | 
   | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
   | **Typ bramy** | wybierz pozycję **VPN**. |
   | **Typ sieci VPN** | Wybierz **trasę opartą na trasie** |
   | **Numer jednostki magazynowej**| Pozostaw `VpnGw1`domyślnie . |
   | **Lokalizacja**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i pomocnicza sieć wirtualna.   |
   | **Sieć wirtualna**| Wybierz sieć wirtualną utworzoną w sekcji `vnet-sql-mi-secondary`2, na przykład . |
   | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
   | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, na `secondary-gateway-IP`przykład . |
   | &nbsp; | &nbsp; |

   ![Ustawienia bramy pomocniczej](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz pomocniczą bramę sieci wirtualnej przy użyciu programu PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Łączenie bram 
Tworzenie połączeń między dwiema bramami przy użyciu portalu Azure lub programu PowerShell. 

Należy utworzyć dwa połączenia — połączenie z bramy podstawowej do bramy pomocniczej, a następnie połączenie z bramy pomocniczej z bramą podstawową. 

Klucz udostępniony używany dla obu połączeń powinien być taki sam dla każdego połączenia. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Tworzenie połączeń między dwiema bramami przy użyciu witryny Azure Portal. 

1. Wybierz **pozycję Utwórz zasób** z [witryny Azure portal](https://portal.azure.com).
1. Wpisz `connection` pole wyszukiwania, a następnie naciśnij klawisz Enter do wyszukiwania, który przeniesie Cię do zasobu **Połączenie** opublikowanego przez firmę Microsoft.
1. Wybierz **pozycję Utwórz,** aby utworzyć połączenie. 
1. Na karcie **Podstawy** wybierz następujące wartości, a następnie wybierz przycisk **OK**. 
    1. Wybierz `VNet-to-VNet` **dla typu Połączenie**. 
    1. Wybierz subskrypcję z listy rozwijanej. 
    1. Wybierz grupę zasobów dla wystąpienia zarządzanego w rozwijanej. 
    1. Wybierz lokalizację podstawowego wystąpienia zarządzanego z listy rozwijanej 
1. Na karcie **Ustawienia** wybierz lub wprowadź następujące wartości, a następnie wybierz przycisk **OK:**
    1. Wybierz podstawową bramę sieciową dla pierwszej `Primary-Gateway`bramy sieci **wirtualnej,** na przykład .  
    1. Wybierz pomocniczą bramę sieciową dla drugiej `Secondary-Gateway`bramy sieci **wirtualnej,** na przykład . 
    1. Zaznacz pole wyboru obok pozycji **Ustanawianie łączności dwukierunkowej**. 
    1. Pozostaw domyślną nazwę połączenia podstawowego lub zmień jej nazwę na wybraną wartość. 
    1. Podaj **klucz udostępniony (PSK)** dla `mi1m2psk`połączenia, na przykład . 

   ![Tworzenie połączenia bramy](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Na karcie **Podsumowanie** przejrzyj ustawienia połączenia dwukierunkowego, a następnie wybierz przycisk **OK,** aby utworzyć połączenie. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Tworzenie połączeń między dwiema bramami przy użyciu programu PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Tworzenie grupy trybu failover 
Utwórz grupę trybu failover dla wystąpień zarządzanych przy użyciu portalu Azure lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover dla wystąpień zarządzanych przy użyciu witryny Azure Portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz podstawowe wystąpienie zarządzane, które chcesz dodać do grupy trybu failover.  
1. W obszarze **Ustawienia**przejdź do **pozycji Grupy trybu failover wystąpienia,** a następnie wybierz pozycję **Dodaj grupę,** aby otworzyć stronę **Grupa trybu failover wystąpienia.** 

   ![Dodawanie grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na stronie **Grupa trybu failover wystąpienia** wpisz nazwę grupy trybu failover, a następnie wybierz pomocnicze wystąpienie zarządzane z listy rozwijanej. Wybierz **pozycję Utwórz,** aby utworzyć grupę trybu failover. 

   ![Tworzenie grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Po zakończeniu wdrażania grupy trybu failover zostaniesz przejmąc się z powrotem do strony **grupy trybu failover.** 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz grupę trybu failover dla wystąpień zarządzanych przy użyciu programu PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Testowanie pracy w trybie failover

Przetestuj fazę failover grupy trybu failover przy użyciu witryny Azure portal lub programu PowerShell. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testowanie pracy awaryjnej grupy trybu failover przy użyciu witryny Azure portal. 

1. Przejdź do _pomocniczego_ wystąpienia zarządzanego w [witrynie Azure portal](https://portal.azure.com) i wybierz grupy trybu **failover wystąpienia** w ustawieniach. 
1. Przejrzyj, które wystąpienie zarządzane jest podstawowym, a które wystąpienie zarządzane jest pomocnicze. 
1. Wybierz **opcję Przewijanie awaryjne,** a następnie wybierz pozycję **Tak** w ostrzeżeniu o rozłączeniu sesji TDS. 

   ![Praca awaryjna grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Sprawdź, które wystąpienie manged jest podstawowym i które wystąpienie jest pomocnicze. Jeśli przełączenie w stan failover powiodło się, dwa wystąpienia powinny mieć przełączane role. 

   ![Wystąpienia zarządzane zmieniły role po przełączeniu awaryjnym](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Przejdź do nowego _pomocniczego_ wystąpienia zarządzanego i wybierz opcję **Przewijanie awaryjne** ponownie, aby zakończyć niepowodzeniem wystąpienia podstawowego z powrotem do roli podstawowej. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Przetestuj przewijaniec awaryjny grupy trybu failover przy użyciu programu PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Lokalizowanie punktu końcowego odbiornika

Po skonfigurowaniu grupy trybu failover zaktualizuj parametry połączenia aplikacji do punktu końcowego odbiornika. Spowoduje to, że aplikacja będzie podłączona do detektora grupy trybu failover, a nie do podstawowej bazy danych, puli elastycznej lub wystąpienia zarządzanego. W ten sposób nie trzeba ręcznie aktualizować parametry połączenia za każdym razem, gdy jednostka bazy danych SQL platformy Azure przechodzi w tryb fail over, a ruch jest kierowany do dowolnej jednostki jest obecnie podstawowy. 

Punkt końcowy odbiornika ma postać `fog-name.database.windows.net`programu i jest widoczny w witrynie Azure portal podczas wyświetlania grupy trybu failover:

![Parametry połączenia grupy trybu failover](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Uwagi

- Usunięcie grupy trybu failover dla pojedynczej lub puli bazy danych nie zatrzymuje replikacji i nie powoduje usunięcia replikowanej bazy danych. Należy ręcznie zatrzymać replikację geograficzną i usunąć bazę danych z serwera pomocniczego, jeśli chcesz dodać pojedynczą lub pulową bazę danych z powrotem do grupy trybu failover po jej usunięciu. Niepowodzenie jednej z rzeczy może spowodować błąd `The operation cannot be performed due to multiple errors` podobny do podczas próby dodania bazy danych do grupy trybu failover. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe kroki konfigurowania grupy trybu failover, zobacz następujące samouczki:
- [Dodawanie pojedynczej bazy danych do grupy trybu failover](sql-database-single-database-failover-group-tutorial.md)
- [Dodawanie elastycznej puli do grupy trybu failover](sql-database-elastic-pool-failover-group-tutorial.md)
- [Dodawanie wystąpień zarządzanych do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md)
 
Aby zapoznać się z omówieniem opcji wysokiej dostępności usługi Azure SQL Database, zobacz [replikację geograficzną](sql-database-active-geo-replication.md) i [grupy automatycznego trybu failover](sql-database-auto-failover-group.md). 
