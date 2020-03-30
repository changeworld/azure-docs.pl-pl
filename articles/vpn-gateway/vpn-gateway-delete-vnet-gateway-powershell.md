---
title: 'Brama sieci VPN platformy Azure: usuwanie bramy: Program PowerShell'
description: Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell w modelu wdrażania Menedżera zasobów.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162110"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Istnieje kilka różnych metod, które można podjąć, gdy chcesz usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystko i zacząć od nowa, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Jest to metoda jest zalecane tylko wtedy, gdy nie chcesz zachować żadnych zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilka zasobów przy użyciu tej metody.

- Jeśli chcesz zachować niektóre zasoby w grupie zasobów, usunięcie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Przed usunięciem bramy sieci wirtualnej należy najpierw usunąć wszystkie zasoby zależne od bramy. Kroki, które należy wykonać, zależą od typu połączeń, które zostały utworzone i zasobów zależnych dla każdego połączenia.

## <a name="before-beginning"></a>Przed rozpoczęciem



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Pobierz najnowsze polecenia cmdlet programu Azure Resource Manager programu PowerShell.

Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu Azure Resource Manager programu PowerShell. Aby uzyskać więcej informacji na temat pobierania i instalowania poleceń cmdlet programu PowerShell, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Połącz się ze swoim kontem platformy Azure.

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
```

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Usuwanie bramy sieci VPN między lokacjami

Aby usunąć bramę sieci wirtualnej dla konfiguracji S2S, należy najpierw usunąć każdy zasób odnoszący się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z poniższymi przykładami należy określić niektóre wartości, podczas gdy inne wartości są wynikiem wyjściowym. W przykładach używamy następujących określonych wartości do celów demonstracyjnych:

Nazwa sieci wirtualnej: W sieci wirtualnej1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki dotyczą modelu wdrażania Menedżera zasobów.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, którą chcesz usunąć.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy brama sieci wirtualnej ma jakieś połączenia.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Usuń wszystkie połączenia.

Może zostać wyświetlony monit o potwierdzenie usunięcia każdego z połączeń.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Usuń bramę sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy. Jeśli oprócz konfiguracji S2S masz konfigurację P2S do tej sieci wirtualnej, usunięcie bramy sieci wirtualnej spowoduje automatyczne odłączenie wszystkich klientów P2S bez ostrzeżenia.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie brama sieci wirtualnej została usunięta. Można użyć następnych kroków, aby usunąć wszystkie zasoby, które nie są już używane.

### <a name="5-delete-the-local-network-gateways"></a>5 Usuń bramy sieci lokalnej.

Pobierz listę odpowiednich bram sieci lokalnej.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Usuń bramy sieci lokalnej. Może zostać wyświetlony monit o potwierdzenie usunięcia każdej bramy sieci lokalnej.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Usuń zasoby publicznego adresu IP.

Pobierz konfiguracje IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów publicznego adresu IP używanych dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej była aktywna jako aktywna, zostaną wyświetlone dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń publiczne zasoby IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy i ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Usuwanie bramy sieci VPN sieci wirtualnej do sieci wirtualnej

Aby usunąć bramę sieci wirtualnej dla konfiguracji V2V, należy najpierw usunąć każdy zasób odnoszący się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z poniższymi przykładami należy określić niektóre wartości, podczas gdy inne wartości są wynikiem wyjściowym. W przykładach używamy następujących określonych wartości do celów demonstracyjnych:

Nazwa sieci wirtualnej: W sieci wirtualnej1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki dotyczą modelu wdrażania Menedżera zasobów.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, którą chcesz usunąć.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy brama sieci wirtualnej ma jakieś połączenia.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Mogą istnieć inne połączenia z bramą sieci wirtualnej, które są częścią innej grupy zasobów. Sprawdź, czy w każdej dodatkowej grupie zasobów nie ma dodatkowych połączeń. W tym przykładzie sprawdzamy połączenia z RG2. Uruchom to dla każdej grupy zasobów, która może mieć połączenie z bramą sieci wirtualnej.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Pobierz listę połączeń w obu kierunkach.

Ponieważ jest to konfiguracja sieci wirtualnej do sieci wirtualnej, potrzebna jest lista połączeń w obu kierunkach.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
W tym przykładzie sprawdzamy połączenia z RG2. Uruchom to dla każdej grupy zasobów, która może mieć połączenie z bramą sieci wirtualnej.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Usuń wszystkie połączenia.

Może zostać wyświetlony monit o potwierdzenie usunięcia każdego z połączeń.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Usuń bramę sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej. Jeśli oprócz konfiguracji V2V masz konfiguracje P2S do sieci wirtualnych, usunięcie bram sieci wirtualnej spowoduje automatyczne odłączenie wszystkich klientów P2S bez ostrzeżenia.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie brama sieci wirtualnej została usunięta. Można użyć następnych kroków, aby usunąć wszystkie zasoby, które nie są już używane.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Usuwanie zasobów publicznego adresu IP

Pobierz konfiguracje IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów publicznego adresu IP używanych dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej była aktywna jako aktywna, zostaną wyświetlone dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń publiczne zasoby IP. Może zostać wyświetlony monit o potwierdzenie usunięcia publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy i ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Usuwanie bramy sieci VPN typu punkt-lokacja

Aby usunąć bramę sieci wirtualnej dla konfiguracji P2S, należy najpierw usunąć każdy zasób odnoszący się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z poniższymi przykładami należy określić niektóre wartości, podczas gdy inne wartości są wynikiem wyjściowym. W przykładach używamy następujących określonych wartości do celów demonstracyjnych:

Nazwa sieci wirtualnej: W sieci wirtualnej1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki dotyczą modelu wdrażania Menedżera zasobów.


>[!NOTE]
> Po usunięciu bramy sieci VPN wszyscy połączeni klienci zostaną odłączeni od sieci wirtualnej bez ostrzeżenia.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, którą chcesz usunąć.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Usuń bramę sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie brama sieci wirtualnej została usunięta. Można użyć następnych kroków, aby usunąć wszystkie zasoby, które nie są już używane.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Usuwanie zasobów publicznego adresu IP

Pobierz konfiguracje IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę publicznych adresów IP używanych dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej była aktywna jako aktywna, zostaną wyświetlone dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń publiczne pliki IP. Może zostać wyświetlony monit o potwierdzenie usunięcia publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Usuń podsieć bramy i ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie obawiasz się, że nie masz żadnych zasobów w grupie zasobów i chcesz zacząć od nowa, możesz usunąć całą grupę zasobów. Jest to szybki sposób, aby usunąć wszystko. Poniższe kroki dotyczą tylko modelu wdrażania Menedżera zasobów.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Pobierz listę wszystkich grup zasobów w subskrypcji.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Znajdź grupę zasobów, którą chcesz usunąć.

Znajdź grupę zasobów, którą chcesz usunąć, i wyświetl listę zasobów w tej grupie zasobów. W tym przykładzie nazwa grupy zasobów to RG1. Zmodyfikuj przykład, aby pobrać listę wszystkich zasobów.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Sprawdź zasoby na liście.

Po zwróceniu listy przejrzyj ją, aby sprawdzić, czy chcesz usunąć wszystkie zasoby w grupie zasobów, a także samą grupę zasobów. Jeśli chcesz zachować niektóre zasoby w grupie zasobów, użyj kroków we wcześniejszych sekcjach tego artykułu, aby usunąć bramę.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Usuń grupę zasobów i zasoby.

Aby usunąć grupę zasobów i cały zasób zawarty w grupie zasobów, zmodyfikuj przykład i uruchom.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Sprawdź stan.

Usunięcie wszystkich zasobów na platformie Azure zajmuje trochę czasu. Stan grupy zasobów można sprawdzić za pomocą tego polecenia cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Wynik, który jest zwracany pokazuje "Udało się".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
