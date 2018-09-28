---
title: Wdrażanie i konfigurowanie usługi Azure Firewall w sieci hybrydowej za pomocą programu Azure PowerShell
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować usługę Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 919051a945d423a104b286e9c5703c5b749cf026
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946463"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w sieci hybrydowej za pomocą programu Azure PowerShell


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska sieciowego
> * Konfigurowanie i wdrażanie zapory
> * Tworzenie tras
> * Tworzenie maszyn wirtualnych
> * Testowanie zapory

W tym samouczku zostaną utworzone trzy sieci wirtualne:
- **VNet-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VNet-Spoke** — sieć wirtualna będąca szprychą reprezentuje pakiet roboczy na platformie Azure.
- **VNet-Onprem** — sieć wirtualna OnPrem reprezentuje sieć lokalną. W trakcie rzeczywistego wdrożenia połączenie można nawiązać za pomocą sieci VPN lub usługi Express Route. Dla ułatwienia w tym samouczku zostanie wykorzystane połączenie za pośrednictwem bramy VPN Gateway, a do reprezentowania sieci lokalnej zostanie wykorzystana sieć wirtualna zlokalizowana na platformie Azure.

![Zapora w sieci hybrydowej](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Podstawowe wymagania

Aby ten scenariusz przebiegał prawidłowo, muszą zostać spełnione trzy podstawowe wymagania:

- Trasa zdefiniowana przez użytkownika w podsieci szprychy, która wskazuje adres IP usługi Azure Firewall jako bramę domyślną. Propagacja trasy protokołu BGP musi być **wyłączona** w przypadku tej tabeli tras.
- Trasa zdefiniowana przez użytkownika w podsieci bramy koncentratora musi wskazywać adres IP zapory jako następny przeskok do sieci będącej szprychą.
- W podsieci usługi Azure Firewall nie jest wymagana trasa zdefiniowana przez użytkownika, ponieważ uzyskuje ona informacje o trasach na podstawie protokołu BGP.
- Ustaw wartość **AllowGatewayTransit** na potrzeby komunikacji równorzędnej między sieciami VNet-Hub i VNet-Spoke oraz **UseRemoteGateways** na potrzeby komunikacji równorzędnej VNet-Spoke i VNet-Hub.

Zapoznaj się z sekcją [Tworzenie tras](#create-routes) w tym samouczku, aby poznać sposób tworzenia tych tras.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Deklarowanie zmiennych
W poniższym przykładzie zmienne są deklarowane przy użyciu wartości podanych dla tego samouczka. W większości przypadków należy zastąpić wartości własnymi. Można jednak użyć tych zmiennych, aby wykonać opisane kroki w celu zapoznania się z tego typu konfiguracją. Jeśli jest taka konieczność, zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów zawierającą wszystkie zasoby, które są wymagane do celów tego samouczka:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>Tworzenie i konfigurowanie sieci wirtualnej koncentratora zapory

Zdefiniuj podsieci, które mają być dołączone do sieci wirtualnej:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Teraz utwórz sieć wirtualną koncentratora zapory:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Następnie zostaje przesłane żądanie przydzielenia publicznego adresu IP do bramy sieci VPN, która zostanie utworzona dla sieci wirtualnej. Należy zauważyć, że metoda *AllocationMethod* jest **dynamiczna**. Nie możesz określić adresu IP, którego chcesz użyć. Jest on przydzielany dynamicznie do bramy sieci VPN. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>Tworzenie i konfigurowanie sieci wirtualnej będącej szprychą

Zdefiniuj podsieci, które mają być dołączone do sieci wirtualnej będącej szprychą:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Utwórz sieć wirtualną będącą szprychą:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurowanie i wdrażanie zapory

Teraz należy wdrożyć zaporę w sieci wirtualnej koncentratora.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Konfigurowanie reguł sieci

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80
$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *
$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>Tworzenie i łączenie bram sieci VPN

Sieci wirtualne Hub i OnPrem są połączone za pośrednictwem bram sieci VPN.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>Tworzenie bramy sieci VPN dla sieci wirtualnej koncentratora

Utwórz konfigurację bramy sieci VPN. W ramach konfiguracji bramy sieci VPN zostaje zdefiniowana podsieć i publiczny adres IP do użycia.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Teraz utwórz bramę sieci VPN dla sieci wirtualnej koncentratora. Konfiguracje połączeń między sieciami wirtualnymi wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>Tworzenie bramy sieci VPN dla sieci wirtualnej OnPrem

Utwórz konfigurację bramy sieci VPN. W ramach konfiguracji bramy sieci VPN zostaje zdefiniowana podsieć i publiczny adres IP do użycia.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Teraz utwórz bramę sieci VPN dla sieci wirtualnej OnPrem. Konfiguracje połączeń między sieciami wirtualnymi wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>Tworzenie połączeń sieci VPN
Teraz możesz utworzyć połączenia sieci VPN pomiędzy bramami sieci Hub i OnPrem

#### <a name="get-the-vpn-gateways"></a>Uzyskiwanie bram sieci VPN
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Tworzenie połączeń

W tym momencie utworzysz połączenie pomiędzy siecią wirtualną Hub a siecią wirtualną OnPrem. W przykładach zastosowano odwołania do klucza współużytkowanego. Możesz wybrać własne wartości dla klucza współużytkowanego. Ważne jest, aby klucz współużytkowany był zgodny z obydwoma połączeniami. Tworzenie połączenia może nieco potrwać.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Utwórz połączenie pomiędzy sieciami wirtualnymi OnPrem i Hub. Ten krok jest podobny do poprzedniego, jednak w tym przypadku utworzysz połączenie pomiędzy siecią wirtualną Vnet-Onprem a VNet-Hub. Upewnij się, że klucze współużytkowane są zgodne. Po kilku minutach połączenie zostanie ustanowione.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
#### <a name="verify-the-connection"></a>Weryfikowanie połączenia
 
Możesz również sprawdzić, czy połączenie zostało nawiązane pomyślnie, używając polecenia cmdlet *Get-AzureRmVirtualNetworkGatewayConnection* z opcją *-Debug* lub bez niej. Można skorzystać z następującego przykładu użycia polecenia cmdlet, dopasowując wartości do własnych potrzeb. Jeśli pojawi się monit, wybierz **A**, aby uruchomić **Wszystko**. W podanym przykładzie opcja *-Name* odnosi się do nazwy połączenia, które ma zostać przetestowane.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Po zakończeniu działania polecenia cmdlet sprawdź wartości. W następującym przykładzie stan połączenia jest wyświetlany jako *Połączone* i można zobaczyć bajty przychodzące i wychodzące.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="create-and-configure-the-onprem-vnet"></a>Tworzenie i konfigurowanie sieci wirtualnej OnPrem

Zdefiniuj podsieci, które mają być dołączone do sieci wirtualnej:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Teraz utwórz sieć wirtualną OnPrem:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Następnie zostanie przesłane żądanie przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej. Należy zauważyć, że metoda *AllocationMethod* jest **dynamiczna**. Nie możesz określić adresu IP, którego chcesz użyć. Jest on przydzielany dynamicznie do bramy. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="peer-the-hub-and-spoke-vnets"></a>Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy

Możesz teraz nawiązać komunikację równorzędną pomiędzy sieciami wirtualnymi koncentratora i szprychy.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Tworzenie tras

Następnie należy utworzyć kilka tras: 
- Trasa z podsieci bramy koncentratora do podsieci będącej szprychą za pośrednictwem adresu IP zapory
- Trasa domyślna z podsieci będącej szprychą za pośrednictwem adresu IP zapory

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```
## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne pakietu roboczego szprychy i sieci lokalnej, a następnie umieść je w odpowiednich podsieciach.

### <a name="create-the-workload-virtual-machine"></a>Tworzenie maszyny wirtualnej pakietu roboczego
Utwórz maszynę wirtualną w sieci wirtualnej będącej szprychą z uruchomionymi usługami IIS, bez publicznego adresu IP i zezwól na wysyłanie poleceń ping.
Po wyświetleniu monitu wpisz nazwę użytkownika i hasło maszyny wirtualnej.

```azurepowershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1

#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1
```

### <a name="create-the-onprem-virtual-machine"></a>Tworzenie maszyny wirtualnej OnPrem
Jest to prosta maszyna wirtualna, którą można połączyć z publicznym adresem IP przy użyciu pulpitu zdalnego. Z tego miejsca możesz nawiązać połączenie z serwerem OnPrem za pośrednictwem zapory. Po wyświetleniu monitu wpisz nazwę użytkownika i hasło maszyny wirtualnej.
```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testowanie zapory
Najpierw należy pobrać i zanotować prywatny adres IP dla maszyny wirtualnej **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. W witrynie Azure Portal połącz się z maszyną wirtualną **VM-Onprem**.
2. Otwórz wiersz polecenia programu Windows PowerShell na maszynie wirtualnej **Vm-Onprem**, i wyślij polecenie ping na prywatny adres IP maszyny wirtualnej **VM-spoke-01**.

   Powinna pojawić się odpowiedź.
1. Otwórz przeglądarkę internetową na maszynie wirtualnej **VM-Onprem**, a następnie przejdź do lokalizacji http://\<VM-spoke-01 private IP\>.

   Powinna zostać wyświetlona domyślna strona usług Internet Information Services.

3. Z poziomu maszyny **VM-Onprem**, otwórz pulpit zdalny na maszynie **VM-spoke-01** przy użyciu prywatnego adresu IP.

   Połączenie powinno zostać nawiązane pomyślnie, a użytkownik powinien móc zalogować się za pomocą wybranej nazwy użytkownika i hasła.

Teraz upewnij się, czy reguły zapory działają:

- Możesz wysłać polecenie ping do serwera w sieci wirtualnej będącej szprychą.
- Możesz przeglądać serwer internetowy w sieci wirtualnej będącej szprychą.
- Możesz nawiązać połączenie z serwerem w sieci wirtualnej będącej szprychą, korzystając z protokołu RDP.

Następnie zmień ustawienie akcji kolekcji reguł sieci zapory na **Odmów**, aby sprawdzić, czy reguły zapory działają zgodnie z oczekiwaniami. Uruchom następujący skrypt, aby zmienić ustawienie akcji kolekcji reguł na **Odmów**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Teraz ponownie uruchom testy. Tym razem wszystkie powinny zakończyć się niepowodzeniem. Zamknij wszystkie zdalne pulpity, zanim zaczniesz testować zmienione zasady.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **FW-Hybrid-Test**, aby usunąć wszystkie zasoby związane z zaporą.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska sieciowego
> * Konfigurowanie i wdrażanie zapory
> * Tworzenie tras
> * Tworzenie maszyn wirtualnych
> * Testowanie zapory

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
