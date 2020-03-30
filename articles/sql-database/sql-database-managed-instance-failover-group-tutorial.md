---
title: 'Samouczek: Dodawanie wystąpienia zarządzanego do grupy trybu failover'
description: Dowiedz się, jak skonfigurować grupę trybu failover dla wystąpienia zarządzanego usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462653"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Samouczek: Dodawanie wystąpienia zarządzanego bazy danych SQL do grupy trybu failover

Dodawanie wystąpienia zarządzanego bazy danych SQL do grupy trybu failover. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> - Tworzenie podstawowego wystąpienia zarządzanego
> - Utwórz pomocnicze wystąpienie zarządzane jako część [grupy trybu failover](sql-database-auto-failover-group.md). 
> - Testowanie pracy w trybie failover

  > [!NOTE]
  > - Podczas przechodzenia przez ten samouczek upewnij się, że konfigurujesz zasoby z [wymaganiami wstępnymi do konfigurowania grup trybu failover dla wystąpienia zarządzanego](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Tworzenie wystąpienia zarządzanego może zająć dużo czasu. W rezultacie ten samouczek może potrwać kilka godzin. Aby uzyskać więcej informacji na temat czasów inicjowania obsługi administracyjnej, zobacz [operacje zarządzania wystąpieniami zarządzanymi](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - Wystąpienia zarządzane uczestniczące w grupie trybu failover wymagają [usługi ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) lub dwóch połączonych bram sieci VPN. Ten samouczek zawiera kroki dotyczące tworzenia i łączenia bram sieci VPN. Pomiń te kroki, jeśli masz już skonfigurowany program ExpressRoute. 


## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto,](https://azure.microsoft.com/free/) jeśli jeszcze go nie masz.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby ukończyć samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto,](https://azure.microsoft.com/free/) jeśli jeszcze go nie masz.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Tworzenie grupy zasobów i podstawowego wystąpienia zarządzanego
W tym kroku utworzysz grupę zasobów i podstawowe wystąpienie zarządzane dla grupy trybu failover przy użyciu witryny Azure portal lub programu PowerShell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Utwórz grupę zasobów i podstawowe wystąpienie zarządzane za pomocą witryny Azure Portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie witryny Azure portal. Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz **+ Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Dodatkowe informacje o różnych bazach danych można wyświetlić, wybierając pozycję Pokaż szczegóły na kafelku Bazy danych.
1. Wybierz **pozycję Utwórz** na kafelku **wystąpieniami zarządzanymi SQL.** 

    ![Wybierz wystąpienie zarządzane](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na stronie **Tworzenie wystąpienia zarządzanego bazy danych SQL platformy Azure** na karcie **Podstawy**
    1. W obszarze **Szczegóły projektu**wybierz **subskrypcję** z listy rozwijanej, a następnie wybierz pozycję **Utwórz nową** grupę zasobów. Wpisz nazwę grupy zasobów, na `myResourceGroup`przykład . 
    1. W obszarze **Szczegóły wystąpienia zarządzanego**podaj nazwę wystąpienia zarządzanego i region, w którym chcesz wdrożyć wystąpienie zarządzane. Pozostaw **magazyn Compute + przy** wartościach domyślnych. 
    1. W obszarze **Konto administratora**podaj login administratora, taki jak `azureuser`, i złożone hasło administratora. 

    ![Tworzenie podstawowego MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Pozostaw pozostałe ustawienia przy wartościach domyślnych i wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć ustawienia wystąpienia zarządzanego. 
1. Wybierz **pozycję Utwórz,** aby utworzyć główne wystąpienie zarządzane. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz grupę zasobów i podstawowe wystąpienie zarządzane za pomocą programu PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieci w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Grupa bezpieczeństwa Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera grupę zabezpieczeń sieci. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguł zabezpieczeń sieciowych do sieciowej grupy zabezpieczeń. |
| [Grupa bezpieczeństwa Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Dodatek AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Tabela Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [Nowy-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzanej usługi Azure SQL Database.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Tworzenie dodatkowej sieci wirtualnej
Jeśli używasz witryny Azure portal do tworzenia wystąpienia zarządzanego, należy utworzyć sieć wirtualną oddzielnie, ponieważ istnieje wymóg, że podsieć podstawowego i pomocniczego wystąpienia zarządzanego nie mają nakładających się zakresów. Jeśli używasz programu PowerShell do konfigurowania wystąpienia zarządzanego, przejdź do kroku 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 
Aby zweryfikować zakres podsieci podstawowej sieci wirtualnej, wykonaj następujące kroki:
1. W [witrynie Azure portal](https://portal.azure.com)przejdź do grupy zasobów i wybierz sieć wirtualną dla wystąpienia podstawowego. 
1. Wybierz **podsieci** w obszarze **Ustawienia** i zanotuj **zakres adresów**. Zakres adresów podsieci sieci wirtualnej dla pomocniczego wystąpienia zarządzanego nie może nakładać się na to. 


   ![Podsieć podstawowa](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Aby utworzyć sieć wirtualną, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** i wyszukaj *sieć wirtualną*. 
1. Wybierz opcję **Sieć wirtualna** opublikowaną przez firmę Microsoft, a następnie wybierz pozycję **Utwórz** na następnej stronie. 
1. Wypełnij pola wymagane do skonfigurowania sieci wirtualnej pomocniczego wystąpienia zarządzanego, a następnie wybierz pozycję **Utwórz**. 

   W poniższej tabeli przedstawiono wartości niezbędne dla pomocniczej sieci wirtualnej:

    | **Pole** | Wartość |
    | --- | --- |
    | **Nazwa** |  Nazwa sieci wirtualnej, która ma być używana przez `vnet-sql-mi-secondary`pomocnicze wystąpienie zarządzane, takie jak . |
    | **Przestrzeń adresowa** | Przestrzeń adresowa sieci wirtualnej, `10.128.0.0/16`na przykład . | 
    | **Subskrypcja** | Subskrypcja, w której znajduje się główne wystąpienie zarządzane i grupa zasobów. |
    | **Region** | Lokalizacja, w której zostanie wdrożone pomocnicze wystąpienie zarządzane. |
    | **Podsieci** | Nazwa podsieci. `default`jest domyślnie dostępna. |
    | **Zakres adresów**| Zakres adresów podsieci. Musi to być inny niż zakres adresów podsieci używany przez `10.128.0.0/24`sieć wirtualną podstawowego wystąpienia zarządzanego, na przykład .  |
    | &nbsp; | &nbsp; |

    ![Dodatkowe wartości sieci wirtualnej](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ten krok jest konieczne tylko wtedy, gdy używasz witryny Azure Portal do wdrażania wystąpienia zarządzanego. Przejdź do kroku 3, jeśli używasz programu PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Tworzenie pomocniczego wystąpienia zarządzanego
W tym kroku utworzysz pomocnicze wystąpienie zarządzane w witrynie Azure Portal, która również skonfiguruje sieć między dwoma wystąpieniami zarządzanymi. 

Drugie wystąpienie zarządzane musi:
- Bądź pusty. 
- Mają inny zakres podsieci i adresu IP niż główne wystąpienie zarządzane. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Utwórz pomocnicze wystąpienie zarządzane przy użyciu witryny Azure Portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie witryny Azure portal. Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz **+ Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Dodatkowe informacje o różnych bazach danych można wyświetlić, wybierając pozycję Pokaż szczegóły na kafelku Bazy danych.
1. Wybierz **pozycję Utwórz** na kafelku **wystąpieniami zarządzanymi SQL.** 

    ![Wybierz wystąpienie zarządzane](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na karcie **Podstawy** na stronie **Tworzenie wystąpienia zarządzanego bazy danych SQL azure** wypełnij pola wymagane do skonfigurowania pomocniczego wystąpienia zarządzanego. 

   W poniższej tabeli przedstawiono wartości niezbędne dla pomocniczego wystąpienia zarządzanego:
 
    | **Pole** | Wartość |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Grupa zasobów**| Grupa zasobów, w której znajduje się główne wystąpienie zarządzane. |
    | **Nazwa wystąpienia zarządzanego** | Nazwa nowego pomocniczego wystąpienia zarządzanego, takiego jak`sql-mi-secondary`  | 
    | **Region**| Lokalizacja pomocniczego wystąpienia zarządzanego.  |
    | **Identyfikator logowania administratora wystąpienia zarządzanego** | Login, którego chcesz użyć dla nowego pomocniczego `azureuser`wystąpienia zarządzanego, takiego jak . |
    | **Hasło** | Złożone hasło, które będzie używane przez logowania administratora dla nowego pomocniczego wystąpienia zarządzanego.  |
    | &nbsp; | &nbsp; |

1. Na karcie **Sieć** dla **sieci wirtualnej**wybierz z listy rozwijanej sieć wirtualną utworzoną dla pomocniczego wystąpienia zarządzanego.

   ![Sieć pomocnicza MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na karcie **Ustawienia dodatkowe** w obszarze **Replikacja geograficzna**wybierz opcję **Tak,** aby _użyć jako pomocniczego trybu failover_. Wybierz główne wystąpienie zarządzane z listy rozwijanej. 
    1. Upewnij się, że sortowanie i strefa czasowa jest zgodna z podstawowym wystąpieniem zarządzanym. Podstawowe wystąpienie zarządzane utworzone w tym `SQL_Latin1_General_CP1_CI_AS` samouczku `(UTC) Coordinated Universal Time` używane domyślne sortowania i strefy czasowej. 

   ![Sieć pomocnicza MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć ustawienia pomocniczego wystąpienia zarządzanego. 
1. Wybierz **pozycję Utwórz,** aby utworzyć pomocnicze wystąpienie zarządzane. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz pomocnicze wystąpienie zarządzane przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieci w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Grupa bezpieczeństwa Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera grupę zabezpieczeń sieci. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguł zabezpieczeń sieciowych do sieciowej grupy zabezpieczeń. |
| [Grupa bezpieczeństwa Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Dodatek AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Tabela Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [Nowy-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzanej usługi Azure SQL Database.  |

---

## <a name="4---create-primary-gateway"></a>4 - Tworzenie bramy podstawowej 
Aby dwa wystąpienia zarządzane uczestniczyły w grupie trybu failover, musi istnieć usługa ExpressRoute lub brama skonfigurowana między sieciami wirtualnymi dwóch wystąpień zarządzanych, aby umożliwić komunikację sieciową. Jeśli zdecydujesz się skonfigurować usługę [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) zamiast łączyć dwie bramy sieci VPN, przejdź do [kroku 7](#7---create-a-failover-group).  

W tym artykule przedstawiono kroki tworzenia dwóch bram sieci VPN i łączenia ich, ale można przejść do przodu do tworzenia grupy trybu failover, jeśli zamiast tego skonfigurowano usługę ExpressRoute. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz bramę dla sieci wirtualnej podstawowego wystąpienia zarządzanego przy użyciu witryny Azure Portal. 


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
    | **Nazwa** | Nazwa bramy sieci wirtualnej, `primary-mi-gateway`na przykład . | 
    | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
    | **Typ bramy** | wybierz pozycję **VPN**. |
    | **Typ sieci VPN** | Wybierz **trasę opartą na trasie** |
    | **Numer jednostki magazynowej**| Pozostaw `VpnGw1`domyślnie . |
    | **Lokalizacja**| Lokalizacja, w której znajduje się główne wystąpienie zarządzane i podstawowa sieć wirtualna.   |
    | **Sieć wirtualna**| Wybierz sieć wirtualną utworzoną w sekcji `vnet-sql-mi-primary`2, na przykład . |
    | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
    | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, na `primary-gateway-IP`przykład . |
    | &nbsp; | &nbsp; |

1. Pozostaw inne wartości jako domyślne, a następnie wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć ustawienia bramy sieci wirtualnej.

   ![Ustawienia bramy podstawowej](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć nową bramę sieci wirtualnej. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz bramę dla sieci wirtualnej podstawowego wystąpienia zarządzanego przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieci w sieci wirtualnej. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację IP dla bramy sieci wirtualnej |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej |


---


## <a name="5---create-secondary-gateway"></a>5 - Tworzenie bramy pomocniczej 
W tym kroku utwórz bramę dla sieci wirtualnej pomocniczego wystąpienia zarządzanego przy użyciu portalu Azure, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Korzystając z witryny Azure portal, powtórz kroki opisane w poprzedniej sekcji, aby utworzyć podsieć sieci wirtualnej i bramę dla pomocniczego wystąpienia zarządzanego. Wypełnij pola wymagane do skonfigurowania bramy pomocniczego wystąpienia zarządzanego. 

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

Utwórz bramę dla sieci wirtualnej pomocniczego wystąpienia zarządzanego przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieci w sieci wirtualnej. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację IP dla bramy sieci wirtualnej |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej |

---


## <a name="6---connect-the-gateways"></a>6 - Podłączanie bram
W tym kroku utwórz dwukierunkowe połączenie między dwiema bramami dwóch sieci wirtualnych. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Połącz dwie bramy przy użyciu witryny Azure portal. 


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

Połącz dwie bramy za pomocą programu PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Ta część samouczka używa następującego polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Tworzy połączenie między dwiema bramami sieci wirtualnej.   |

---


## <a name="7---create-a-failover-group"></a>7 - Tworzenie grupy trybu failover
W tym kroku utworzysz grupę trybu failover i dodasz do niej oba wystąpienia zarządzane. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover przy użyciu witryny Azure portal. 


1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz podstawowe wystąpienie zarządzane utworzone w pierwszej `sql-mi-primary`sekcji, takie jak . 
1. W obszarze **Ustawienia**przejdź do **pozycji Grupy trybu failover wystąpienia,** a następnie wybierz pozycję **Dodaj grupę,** aby otworzyć stronę **Grupa trybu failover wystąpienia.** 

   ![Dodawanie grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na stronie **Grupa trybu failover wystąpienia** wpisz nazwę grupy `failovergrouptutorial` trybu failover, na przykład, `sql-mi-secondary` a następnie wybierz pomocnicze wystąpienie zarządzane, na przykład z listy rozwijanej. Wybierz **pozycję Utwórz,** aby utworzyć grupę trybu failover. 

   ![Tworzenie grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Po zakończeniu wdrażania grupy trybu failover zostaniesz przejmąc się z powrotem do strony **grupy trybu failover.** 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Utwórz grupę trybu failover przy użyciu programu PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Ta część samouczka używa następującego polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Tworzy nową grupę trybu failover wystąpienia zarządzanego usługi Azure SQL Database.  |


---


## <a name="8---test-failover"></a>8 - Test pracy awaryjnej
W tym kroku zakończy się niepowodzeniem grupy trybu failover na serwerze pomocniczym, a następnie po awarii przy użyciu witryny Azure portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Przetestuj przewija po awarii przy użyciu witryny Azure portal. 


1. Przejdź do _pomocniczego_ wystąpienia zarządzanego w [witrynie Azure portal](https://portal.azure.com) i wybierz grupy trybu **failover wystąpienia** w ustawieniach. 
1. Przejrzyj, które wystąpienie zarządzane jest podstawowym, a które wystąpienie zarządzane jest pomocnicze. 
1. Wybierz **opcję Przewijanie awaryjne,** a następnie wybierz pozycję **Tak** w ostrzeżeniu o rozłączeniu sesji TDS. 

   ![Praca awaryjna grupy trybu failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Sprawdź, które wystąpienie manged jest podstawowym i które wystąpienie jest pomocnicze. Jeśli przełączenie w stan fail over powiodło się, dwa wystąpienia powinny mieć przełączane role. 

   ![Wystąpienia zarządzane zmieniły role po przełączeniu awaryjnym](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Przejdź do nowego _pomocniczego_ wystąpienia zarządzanego i wybierz opcję **Przewijanie awaryjne** ponownie, aby zakończyć niepowodzeniem wystąpienia podstawowego z powrotem do roli podstawowej. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Przetestuj przewijaniec awaryjny przy użyciu programu PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Grupa Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Pobiera lub wyświetla listy grup trybu failover wystąpienia zarządzanego.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Wykonuje przeżycie awaryjne grupy trybu failover wystąpienia zarządzanego. | 

---



## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Oczyść zasoby, najpierw usuwając wystąpienie zarządzane, następnie klaster wirtualny, następnie wszystkie pozostałe zasoby, a na koniec grupę zasobów. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Przejdź do grupy zasobów w [witrynie Azure portal](https://portal.azure.com). 
1. Wybierz wystąpienia zarządzane, a następnie wybierz pozycję **Usuń**. Wpisz `yes` pole tekstowe, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. Ten proces może zająć trochę czasu, aby zakończyć w tle i dopóki nie zostanie to zrobione, nie będzie można usunąć *wirtualnego klastra* lub innych zasobów zależnych. Monitoruj usuwanie na karcie Działanie, aby potwierdzić, że wystąpienie zarządzane zostało usunięte. 
1. Po usunięciu wystąpienia zarządzanego usuń *klaster wirtualny,* zaznaczając go w grupie zasobów, a następnie wybierając pozycję **Usuń**. Wpisz `yes` pole tekstowe, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Usuń wszystkie pozostałe zasoby. Wpisz `yes` pole tekstowe, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Usuń grupę zasobów, wybierając pozycję **Usuń grupę zasobów,** wpisując nazwę grupy zasobów, `myResourceGroup`a następnie wybierając polecenie **Usuń**. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Należy usunąć grupę zasobów dwa razy. Usunięcie grupy zasobów po raz pierwszy spowoduje usunięcie wystąpienia zarządzanego i klastrów `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`wirtualnych, ale spowoduje niepowodzenie z komunikatem o błędzie . Uruchom polecenie Usuń-AzResourceGroup po raz drugi, aby usunąć wszystkie zasoby resztkowe, a także grupę zasobów.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Ta część samouczka używa następującego polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów. |

---

## <a name="full-script"></a>Pełny skrypt

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieci w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Grupa bezpieczeństwa Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera grupę zabezpieczeń sieci. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguł zabezpieczeń sieciowych do sieciowej grupy zabezpieczeń. |
| [Grupa bezpieczeństwa Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Dodatek AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Tabela Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [Nowy-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzanej usługi Azure SQL Database.  |
| [Get-AzSqlInstance (Nieumieja)](/powershell/module/az.sql/get-azsqlinstance)| Zwraca informacje o wystąpieniu zarządzanej bazy danych SQL platformy Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację IP dla bramy sieci wirtualnej |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Tworzy połączenie między dwiema bramami sieci wirtualnej.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Tworzy nową grupę trybu failover wystąpienia zarządzanego usługi Azure SQL Database.  |
| [Grupa Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Pobiera lub wyświetla listy grup trybu failover wystąpienia zarządzanego.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Wykonuje przeżycie awaryjne grupy trybu failover wystąpienia zarządzanego. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Nie ma żadnych skryptów dostępnych dla witryny Azure portal.

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano grupę trybu failover między dwoma wystąpieniami zarządzanymi. W tym samouczku omówiono:

> [!div class="checklist"]
> - Tworzenie podstawowego wystąpienia zarządzanego
> - Utwórz pomocnicze wystąpienie zarządzane jako część [grupy trybu failover](sql-database-auto-failover-group.md). 
> - Testowanie pracy w trybie failover

Przejdź do następnego przewodnika Szybki start, jak połączyć się z wystąpieniem zarządzanym i jak przywrócić bazę danych do wystąpienia zarządzanego: 

> [!div class="nextstepaction"]
> [Łączenie się z wystąpieniem](sql-database-managed-instance-configure-vm.md)
> [zarządzanym Przywracanie bazy danych do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md)


