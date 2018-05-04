---
title: 'Usuwanie bramy sieci wirtualnej: środowiska PowerShell: usługi Azure Resource Manager | Dokumentacja firmy Microsoft'
description: Usuń bramę sieci wirtualnej przy użyciu programu PowerShell w modelu wdrażania usługi Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: cherylmc
ms.openlocfilehash: a23a969f1381e3a10c81a903793bad2870b436f6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Istnieje kilka różnych metod, które można wykonać, gdy chcesz usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystkie elementy i rozpocząć od początku, tak jak w przypadku środowiska testowego, można usunąć grupy zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Jest to metoda, jest zalecane tylko jeśli nie chcesz zachować zasoby w grupie zasobów. Nie można selektywnie usunąć tylko kilka zasobów, przy użyciu tej metody.

- Jeśli chcesz zachować niektóre zasoby w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które są zależne od bramy. Kroki, które należy wykonać są zależne od typu połączenia, które zostały utworzone i zasoby zależne, dla każdego połączenia.

## <a name="before-beginning"></a>Przed rozpoczęciem

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Pobierz najnowsze poleceń cmdlet programu PowerShell usługi Azure Resource Manager.

Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell Menedżera zasobów Azure. Aby uzyskać więcej informacji o pobieraniu i instalowaniu poleceń cmdlet programu PowerShell, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Połącz z kontem platformy Azure.

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
```

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzureRmSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, która ma być używany.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Usuwanie bramy sieci VPN typu lokacja-lokacja

Aby usunąć bramę sieci wirtualnej S2S konfiguracji, należy najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z przykłady poniżej, niektóre wartości należy określić, podczas gdy inne wartości są wynik danych wyjściowych. Używamy dla celów demonstracyjnych następujące wartości określonej w przykładach:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki dotyczą modelu wdrażania usługi Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, który chcesz usunąć.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy wszystkie połączenia bramy sieci wirtualnej.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Usuń wszystkie połączenia.

Może być wyświetlony monit o potwierdzenie usunięcia każdego połączenia.

```powershell
$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Usuwanie bramy sieci wirtualnej.

Może być wyświetlony monit o potwierdzenie usunięcia bramy. Jeśli masz konfigurację P2S do tej sieci wirtualnej oprócz konfiguracji S2S usuwanie bramy sieci wirtualnej automatycznie spowoduje odłączenie wszystkich klientów P2S bez ostrzeżenia.


```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie bramy sieci wirtualnej została usunięta. Następne kroki można użyć, aby usunąć wszystkie zasoby, które są już używane.

### <a name="5-delete-the-local-network-gateways"></a>5 usunąć bramy sieci lokalnej.

Pobierz listę odpowiednich bram sieci lokalnej.

```powershell
$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Usuwanie bramy sieci lokalnej. Może być wyświetlony monit o potwierdzenie usunięcia każdego z bramy sieci lokalnej.

```powershell
$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Usuwanie zasobów adresu publicznego adresu IP.

Pobierz konfiguracje adresów IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów adres publiczny adres IP używany dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej był aktywny aktywny, zostanie wyświetlony dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuwanie zasobów publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy i konfiguracji.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Usuwanie bramy sieci VPN do wirtualnymi

Aby usunąć bramę sieci wirtualnej dla konfiguracji V2V, należy najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z przykłady poniżej, niektóre wartości należy określić, podczas gdy inne wartości są wynik danych wyjściowych. Używamy dla celów demonstracyjnych następujące wartości określonej w przykładach:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki dotyczą modelu wdrażania usługi Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, który chcesz usunąć.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy wszystkie połączenia bramy sieci wirtualnej.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Mogą istnieć inne połączenia bramy sieci wirtualnej, które są częścią innej grupie zasobów. Sprawdź, czy dodatkowych połączeń w każdej grupie dodatkowych zasobów. W tym przykładzie firma Microsoft sprawdzanie dla połączeń z RG2. Uruchom to dla każdej grupy zasobów ma, które mogą mieć połączenie bramy sieci wirtualnej.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Pobierz listę połączeń w obu kierunkach.

Ponieważ jest to konfiguracja sieci wirtualnej do sieci wirtualnej, należy na liście połączeń w obu kierunkach.

```powershell
$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
W tym przykładzie firma Microsoft sprawdzanie dla połączeń z RG2. Uruchom to dla każdej grupy zasobów ma, które mogą mieć połączenie bramy sieci wirtualnej.

```powershell
 $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Usuń wszystkie połączenia.

Może być wyświetlony monit o potwierdzenie usunięcia każdego połączenia.

```powershell
$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Usuwanie bramy sieci wirtualnej.

Może być wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej. Jeśli masz konfiguracje P2S do Twojej sieci wirtualnych oprócz konfiguracji V2V usuwanie bramy sieci wirtualnej automatycznie spowoduje odłączenie wszystkich klientów P2S bez ostrzeżenia.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie bramy sieci wirtualnej została usunięta. Następne kroki można użyć, aby usunąć wszystkie zasoby, które są już używane.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Usuwanie zasobów adresu publicznego adresu IP

Pobierz konfiguracje adresów IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów adres publiczny adres IP używany dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej był aktywny aktywny, zostanie wyświetlony dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuwanie zasobów publicznego adresu IP. Może być wyświetlony monit o potwierdzenie usunięcia publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy i konfiguracji.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Usuwanie bramy sieci VPN typu punkt-lokacja

Aby usunąć bramę sieci wirtualnej dla konfiguracji P2S, należy najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z przykłady poniżej, niektóre wartości należy określić, podczas gdy inne wartości są wynik danych wyjściowych. Używamy dla celów demonstracyjnych następujące wartości określonej w przykładach:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: GW1<br>

Poniższe kroki dotyczą modelu wdrażania usługi Resource Manager.


>[!NOTE]
> Po usunięciu bramy sieci VPN, wszyscy połączeni klienci zostanie odłączony od sieci wirtualnej bez ostrzeżenia.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, który chcesz usunąć.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Usuwanie bramy sieci wirtualnej.

Może być wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie bramy sieci wirtualnej została usunięta. Następne kroki można użyć, aby usunąć wszystkie zasoby, które są już używane.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Usuwanie zasobów adresu publicznego adresu IP

Pobierz konfiguracje adresów IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę publiczny adres IP używany dla tej bramy sieci wirtualnej. Jeśli brama sieci wirtualnej był aktywny aktywny, zostanie wyświetlony dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń publicznych adresów IP. Może być wyświetlony monit o potwierdzenie usunięcia publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Usuń podsieć bramy i konfiguracji.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie są dane dotyczące poszczególnych zasobów w grupie zasobów i po prostu chcesz zacząć od nowa, należy usunąć całej grupy zasobów. Jest to szybko Usuń całą. Poniższe kroki dotyczą tylko modelu wdrażania usługi Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Pobranie listy wszystkich grup zasobów w ramach subskrypcji.

```powershell
Get-AzureRmResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Znajdź grupę zasobów, które chcesz usunąć.

Znajdź grupę zasobów, które chcesz usunąć i wyświetlanie listy zasobów w danej grupie zasobów. W tym przykładzie nazwa grupy zasobów jest RG1. Zmodyfikuj przykład, aby pobrać listę wszystkich zasobów.

```powershell
Find-AzureRmResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Sprawdź zasoby na liście.

Gdy lista jest zwracany, przejrzyj go, aby sprawdzić, czy chcesz usunąć wszystkie zasoby w grupie zasobów, a także samej grupy zasobów. Jeśli chcesz zachować niektóre zasoby w grupie zasobów, wykonaj kroki we wcześniejszych sekcjach tego artykułu można usunąć bramy.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Usuń grupę zasobów i zasobów.

Aby usunąć grupę zasobów i wszystkich zasobów znajdujących się w grupie zasobów, zmodyfikuj przykładzie i uruchom.

```powershell
Remove-AzureRmResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Sprawdź stan.

Dopiero po pewnym czasie dla platformy Azure usunąć wszystkie zasoby. Za pomocą tego polecenia cmdlet, można sprawdzić stan grupy zasobów.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName RG1
```

Wynik zwracany zawiera "Powodzenie".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
