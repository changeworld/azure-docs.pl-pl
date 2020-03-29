---
title: Tworzenie prywatnego punktu końcowego platformy Azure przy użyciu programu Azure PowerShell| Dokumenty firmy Microsoft
description: Dowiedz się więcej o łączu prywatnym platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430342"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Tworzenie prywatnego punktu końcowego przy użyciu programu Azure PowerShell
Prywatny punkt końcowy jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobami łączy prywatnych. 

W tym przewodniku Szybki start dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure, serwerze bazy danych SQL z prywatnym punktem końcowym platformy Azure przy użyciu programu Azure PowerShell. Następnie można bezpiecznie uzyskać dostęp do serwera bazy danych SQL z maszyny Wirtualnej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobów należy utworzyć grupę zasobów, która obsługuje sieć wirtualną i prywatny punkt końcowy za pomocą [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *WestUS:*

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć. Następnie należy skojarzyć podsieć z siecią wirtualną.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną dla swojego prywatnego punktu końcowego za pomocą [Sieci New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *MyVirtualNetwork:*
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Dodawanie podsieci

Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, więc należy utworzyć podsieć. Utwórz konfigurację podsieci o nazwie *mySubnet* z [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Poniższy przykład tworzy podsieć o nazwie *mySubnet* z flagą zasad sieciowej prywatnego punktu końcowego ustawioną na **Wyłączona**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Łatwo pomylić `PrivateEndpointNetworkPoliciesFlag` parametr z inną dostępną flagą, ponieważ są to zarówno długie słowa, `PrivateLinkServiceNetworkPoliciesFlag`jak i podobne wyglądy.  Upewnij się, że używasz `PrivateEndpointNetworkPoliciesFlag`właściwego, .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Skojarzenie podsieci z siecią wirtualną

Konfigurację podsieci można zapisać w sieci wirtualnej za pomocą [sieci Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). To polecenie tworzy podsieć:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w sieci wirtualnej za pomocą [programu New-AzVM](/powershell/module/az.compute/new-azvm). Po uruchomieniu następnego polecenia zostanie wyświetlony monit o poświadczenia. Wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej:

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

## <a name="create-a-sql-database-server"></a>Tworzenie serwera bazy danych SQL 

Utwórz serwer bazy danych SQL przy użyciu polecenia New-AzSqlServer. Należy pamiętać, że nazwa serwera bazy danych SQL musi być unikatowa na platformie Azure, więc zastąp wartość zastępczą w nawiasach własną unikatową wartością:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Prywatny punkt końcowy dla serwera bazy danych SQL w sieci wirtualnej z [new-AzPrivateLinkServiceConnection:](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) 

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

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS 
Utwórz prywatną strefę DNS dla domeny programu SQL Database Server i utwórz łącze skojarzenia z siecią wirtualną: 

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

Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W tym przykładzie zwraca publiczny adres IP maszyny Wirtualnej *myVM:*

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Otwórz wiersz polecenia na komputerze lokalnym. Uruchom polecenie mstsc. Zastąp ciąg <publicIpAddress> publicznym adresem IP zwróconym w ostatnim kroku: 


> [!NOTE]
> Jeśli te polecenia były uruchamiane w wierszu polecenia programu PowerShell na komputerze lokalnym i używasz modułu Az PowerShell w wersji 1.0 lub nowszej, możesz kontynuować pracę w tym interfejsie.
```
mstsc /v:<publicIpAddress>
```

1. Po wyświetleniu monitu wybierz pozycję **Połącz**. 
2. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.
  > [!NOTE]
  > Może być konieczne wybranie opcji Więcej opcji > Użyj innego konta, aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej. 
  
3. Kliknij przycisk **OK**. 
4. Może zostać wyświetlone ostrzeżenie o certyfikacie. W takim przypadku wybierz pozycję **Tak** lub **Kontynuuj**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera bazy danych SQL prywatnie z maszyny Wirtualnej

1. Na pulpicie zdalnym myVM otwórz program PowerShell.
2. Wprowadź polecenie `nslookup myserver.database.windows.net`. 

    Otrzymasz wiadomość podobną do tej:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instalowanie programu SQL Server Management Studio
4. W obszarze Połącz z serwerem wprowadź lub wybierz te informacje: Ustawianie typu serwer wartości Wybierz aparat bazy danych.
      Nazwa serwera Wybierz myserver.database.windows.net Nazwa użytkownika Wprowadź nazwę użytkownika podana podczas tworzenia.
      Hasło Wprowadź hasło podane podczas tworzenia.
      Zapamiętaj hasło Wybierz tak.
5. Wybierz przycisk Połącz.
6. Przeglądaj bazy danych z lewego menu. 
7. (Opcjonalnie) Tworzenie lub wykonywanie zapytań z mydatabase
8. Zamknij połączenie pulpitu zdalnego do *myVM*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Po zakończeniu korzystania z prywatnego punktu końcowego, serwera bazy danych SQL i maszyny Wirtualnej, użyj [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby usunąć grupę zasobów i wszystkie zasoby, które posiada:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [łączu prywatnym platformy Azure](private-link-overview.md)
