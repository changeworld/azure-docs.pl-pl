---
title: 'Usuwanie bramy sieci wirtualnej: Program PowerShell: Azure Resource Manager | Microsoft Docs'
description: Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell w modelu wdrażania usługi Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 7b9503b2db14d4de6c4c8cf983c42bccd6f9f8fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157432"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Istnieje kilka różnych metod, które można podjąć usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystkie elementy i zacząć od początku, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Podczas usuwania grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Ta metoda jest tylko jest zalecane, jeśli nie chcesz zachować zasoby w grupie zasobów. Nie można selektywnie usunąć tylko kilka zasobów, przy użyciu tej metody.

- Jeśli chcesz zachować niektóre zasoby w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które są zależne od bramą. Czynności, które należy wykonać zależą od typu połączenia, które zostały utworzone i zasoby zależne, dla każdego połączenia.

## <a name="before-beginning"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Pobierz najnowsze polecenia cmdlet programu PowerShell usługi Resource Manager platformy Azure.

Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell Resource Manager. Aby uzyskać więcej informacji na temat pobierania i instalowania poleceń cmdlet programu PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Łączenie z kontem platformy Azure.

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
```

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, dla której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Usuwanie bramy sieci VPN typu lokacja-lokacja

Aby usunąć bramę sieci wirtualnej dla konfiguracji S2S, musisz najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z przykładami poniżej niektóre wartości należy określić, podczas gdy inne wartości są wynikiem danych wyjściowych. Stosujemy następujące wartości określonej w przykładach w celach demonstracyjnych:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki mają zastosowanie do modelu wdrażania usługi Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, który chcesz usunąć.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy brama sieci wirtualnej nie ma żadnych połączeń.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Usuń wszystkie połączenia.

Może zostać wyświetlony monit o potwierdzenie usunięcia każdego połączenia.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Usuwanie bramy sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy. Jeśli masz konfiguracji P2S w tej sieci wirtualnej oprócz konfigurację S2S usuwanie bramy sieci wirtualnej automatycznie spowoduje odłączenie wszystkich klientów P2S bez ostrzeżenia.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie bramy sieci wirtualnej została usunięta. Następne kroki można użyć, aby usunąć wszystkie zasoby, które są już używane.

### <a name="5-delete-the-local-network-gateways"></a>5 usuwać bramy sieci lokalnej.

Pobierz listę odpowiednich bram sieci lokalnej.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Usuwanie bramy sieci lokalnej. Może zostać wyświetlony monit o potwierdzenie usunięcia każdej bramy sieci lokalnej.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Usuń zasoby adres publiczny adres IP.

Uzyskaj konfiguracje protokołu IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów adres publiczny adres IP używany dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej było aktywne aktywne, zobaczysz dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń zasoby publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy, a następnie ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Usuwanie bramy sieci VPN typu sieć wirtualna-sieć wirtualna

Aby usunąć bramę sieci wirtualnej dla konfiguracji V2V, musisz najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z przykładami poniżej niektóre wartości należy określić, podczas gdy inne wartości są wynikiem danych wyjściowych. Stosujemy następujące wartości określonej w przykładach w celach demonstracyjnych:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki mają zastosowanie do modelu wdrażania usługi Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, który chcesz usunąć.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy brama sieci wirtualnej nie ma żadnych połączeń.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Mogą istnieć inne połączenia bramy sieci wirtualnej, należących do innej grupy zasobów. Sprawdź, czy dodatkowe połączenia z każdej grupy dodatkowych zasobów. W tym przykładzie sprawdzamy dla połączeń z RG2. Uruchom to dla każdej grupy zasobów ma, które mogą mieć połączenie z bramą sieci wirtualnej.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Pobierz listę połączeń w obu kierunkach.

Jest to konfiguracja sieci wirtualnej między sieciami wirtualnymi, niezbędna, ponieważ lista połączeń w obu kierunkach.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
W tym przykładzie sprawdzamy dla połączeń z RG2. Uruchom to dla każdej grupy zasobów ma, które mogą mieć połączenie z bramą sieci wirtualnej.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Usuń wszystkie połączenia.

Może zostać wyświetlony monit o potwierdzenie usunięcia każdego połączenia.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Usuwanie bramy sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej. Jeśli masz konfiguracje P2S w Twoich sieciach wirtualnych oprócz konfigurację V2V usuwanie bramy sieci wirtualnej automatycznie spowoduje odłączenie wszystkich klientów P2S bez ostrzeżenia.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie bramy sieci wirtualnej została usunięta. Następne kroki można użyć, aby usunąć wszystkie zasoby, które są już używane.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Aby usunąć zasoby adres publiczny adres IP

Uzyskaj konfiguracje protokołu IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów adres publiczny adres IP używany dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej było aktywne aktywne, zobaczysz dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń zasoby publicznego adresu IP. Może zostać wyświetlony monit o potwierdzenie usunięcia z publicznym adresem IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy, a następnie ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Usuwanie bramy sieci VPN typu punkt-lokacja

Aby usunąć bramę sieci wirtualnej dla konfiguracji P2S, musisz najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z przykładami poniżej niektóre wartości należy określić, podczas gdy inne wartości są wynikiem danych wyjściowych. Stosujemy następujące wartości określonej w przykładach w celach demonstracyjnych:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki mają zastosowanie do modelu wdrażania usługi Resource Manager.


>[!NOTE]
> Jeśli usuniesz bramy sieci VPN, wszyscy połączeni klienci zostanie odłączony od sieci wirtualnej bez ostrzeżenia.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, który chcesz usunąć.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Usuwanie bramy sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie bramy sieci wirtualnej została usunięta. Następne kroki można użyć, aby usunąć wszystkie zasoby, które są już używane.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Aby usunąć zasoby adres publiczny adres IP

Uzyskaj konfiguracje protokołu IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę publiczne adresy IP używane dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej było aktywne aktywne, zobaczysz dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń publicznych adresów IP. Może zostać wyświetlony monit o potwierdzenie usunięcia z publicznym adresem IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Usuń podsieć bramy, a następnie ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Usuwanie bramy sieci VPN, usuwając grupę zasobów

Jeśli nie jesteś zajmującym się przechowywanie wszystkich zasobów w grupie zasobów i po prostu chcesz zacząć od początku, możesz usunąć całą grupę zasobów. Jest to szybki sposób, aby usunąć wszystkie elementy. Poniższe kroki mają zastosowanie tylko do modelu wdrażania usługi Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Zostanie wyświetlona lista wszystkich grup zasobów w ramach subskrypcji.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Znajdź grupę zasobów, które chcesz usunąć.

Znajdź grupę zasobów, które chcesz usunąć i wyświetlić listę zasobów w danej grupie zasobów. W tym przykładzie nazwa grupy zasobów jest RG1. Zmodyfikuj przykład tak, aby pobrać listę wszystkich zasobów.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Sprawdź zasoby na liście.

Gdy lista jest zwracany, przejrzyj go w celu zweryfikowania, że chcesz usunąć wszystkie zasoby w grupie zasobów, a także samej grupy zasobów. Jeśli chcesz zachować niektóre zasoby w grupie zasobów, wykonaj kroki we wcześniejszych sekcjach tego artykułu, aby usunąć bramę.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Usuń grupę zasobów i zasoby.

Aby usunąć grupę zasobów i wszystkich zasobów znajdujących się w grupie zasobów, zmodyfikuj przykład i uruchom.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Sprawdź stan.

Dopiero po pewnym czasie dla platformy Azure usunąć wszystkie zasoby. Stan grupy zasobów można sprawdzić za pomocą tego polecenia cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Wynik zwracany pokazuje "Powodzenie".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
