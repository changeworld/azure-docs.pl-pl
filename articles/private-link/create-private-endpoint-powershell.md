---
title: Tworzenie prywatnego punktu końcowego platformy Azure przy użyciu Azure PowerShell | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure Private link
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: ca3fec3dbb4fbe77a1d375c0329275b7b799d06b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067850"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell
Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. 

W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną na platformie Azure Virtual Network, SQL Database serwerze z prywatnym punktem końcowym platformy Azure przy użyciu Azure PowerShell. Następnie można bezpiecznie uzyskać dostęp do serwera SQL Database z maszyny wirtualnej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobów należy utworzyć grupę zasobów, która będzie hostować Virtual Network i prywatny punkt końcowy z poleceniem [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *zachodniej* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Tworzenie Virtual Network
W tej sekcji utworzysz sieć wirtualną i podsieć. Następnie należy skojarzyć podsieć Virtual Network.

### <a name="create-a-virtual-network"></a>Tworzenie Virtual Network

Utwórz sieć wirtualną dla prywatnego punktu końcowego za pomocą elementu [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy Virtual Network o nazwie *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Dodawanie podsieci

Platforma Azure wdraża zasoby w podsieci w ramach Virtual Network, dlatego należy utworzyć podsieć. Utwórz konfigurację podsieci o nazwie Moja *podsieć* z [dodatkiem Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Poniższy przykład tworzy podsieć o nazwie Moja *podsieć* z flagą zasad sieci prywatnego punktu końcowego ustawioną na wartość **wyłączone**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet ` 
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Skojarz podsieć z Virtual Network

Konfigurację podsieci można zapisać do Virtual Network przy użyciu [opcji Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). To polecenie tworzy podsieć:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w Virtual Network przy użyciu elementu [New-AzVM](/powershell/module/az.compute/new-azvm). Po uruchomieniu następnego polecenia zostanie wyświetlony monit o poświadczenia. Wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

Opcja `-AsJob` tworzy maszynę wirtualną w tle. Możesz przejść do następnego kroku.

Kiedy platforma Azure zacznie tworzyć maszynę wirtualną w tle, otrzymasz wynik podobny do poniższego:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Utwórz serwer SQL Database 

Utwórz serwer SQL Database przy użyciu polecenia New-AzSqlServer. Należy pamiętać, że nazwa serwera SQL Database musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami:

$adminSqlLogin = "sqladmin" $password = "ChangeYourAdminPassword1"

$Server = New-AzSqlServer-ResourceGroupName "Moja resourceName" `
    -ServerName "myserver" ` — lokalizacja "WestCentralUS" "-SqlAdministratorCredentials $ (New-Object-TypeName system. Management. Automation. PSCredential-listaargumentów $adminSqlLogin, $ ( ConvertTo-SecureString-String $password-AsPlainText-Force))

New-AzSqlDatabase-ResourceGroupName "webresourceing" `
    -ServerName "myserver"` -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" ` -samplename "AdventureWorksLT"


## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Prywatny punkt końcowy dla serwera SQL Database w Virtual Network za pomocą usługi [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" ` 
  -PrivateLinkServiceId $server.ResourceId ` 
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" ` 
  -Name "myPrivateEndpoint" ` 
  -Location "westcentralus" ` 
  -Subnet  $subnet` 
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS 
Utwórz prywatną strefę DNS dla domeny serwera SQL Database i Utwórz link skojarzenia z siecią wirtualną: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" ` 
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" ` 
  -ZoneName "privatelink.database.windows.net"` 
  -Name "mylink" `  
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  ` 
-ResourceGroupName "myResourceGroup" -Ttl 600 ` 
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Użyj [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) , aby zwrócić publiczny adres IP maszyny wirtualnej. Ten przykład zwraca publiczny adres IP maszyny wirtualnej *myVM* :

```azurepowershell
Get-AzPublicIpAddress ` 
  -Name myPublicIpAddress ` 
  -ResourceGroupName myResourceGroup ` 
  | Select IpAddress 
```  
Otwórz wiersz polecenia na komputerze lokalnym. Uruchom polecenie mstsc.  <publicIpAddress>Zamień na publiczny adres IP zwrócony z ostatniego kroku: 


> [!NOTE]
> Jeśli te polecenia były uruchamiane w wierszu polecenia programu PowerShell na komputerze lokalnym i używasz modułu Az PowerShell w wersji 1.0 lub nowszej, możesz kontynuować pracę w tym interfejsie.
```
mstsc /v:<publicIpAddress>
```

1. Jeśli zostanie wyświetlony monit, wybierz pozycję **Połącz**. 
2. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.
  > [!NOTE]
  > Może być konieczne wybranie pozycji więcej opcji, > użyć innego konta, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
  
3. Wybierz **przycisk OK**. 
4. Może zostać wyświetlone ostrzeżenie o certyfikacie. Jeśli to zrobisz, wybierz pozycję **tak** lub **Kontynuuj**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera SQL Database prywatnie z poziomu maszyny wirtualnej

1. W Pulpit zdalny myVM Otwórz program PowerShell.
2. Wprowadź `nslookup myserver.database.windows.net`. 

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information:
    Setting Value
      Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net
      Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## Clean up resources 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)
