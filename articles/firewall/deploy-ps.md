---
title: Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu programu Azure PowerShell
description: W tym artykule dowiesz się, jak wdrożyć i skonfigurować zaporę platformy Azure przy użyciu programu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.openlocfilehash: c2d49defa2e0fbbd12c5403ccca74e91cf4ec981
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502116"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu programu Azure PowerShell

Kontrolowanie dostępu do sieciowego ruchu wychodzącego jest ważną częścią ogólnego planu zabezpieczeń sieci. Na przykład możesz ograniczyć dostęp do witryn sieci web. Lub możesz chcieć ograniczyć wychodzące adresy IP i portów, które mogą być udostępniane.

Jednym ze sposobów kontrolowania dostępu do sieciowego ruchu wychodzącego z podsieci platformy Azure jest użycie usługi Azure Firewall. Za pomocą usługi Azure Firewall można skonfigurować następujące reguły:

* Reguły aplikacji, które definiują w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci.
* Reguły sieci, które definiują adres źródłowy, protokół, port docelowy i adres docelowy.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

W tym artykule uproszczone pojedynczej sieci wirtualnej jest tworzony z trzech podsieci w celu łatwiejszego wdrażania. W przypadku wdrożeń produkcyjnych [modelu topologi gwiaździstej](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) jest to zalecane, gdy Zapora jest we własnej sieci wirtualnej. Obciążenia są serwery w wirtualnych sieciach równorzędnych, w tym samym regionie przy użyciu co najmniej jednej podsieci.

* **AzureFirewallSubnet** — w tej podsieci znajduje się zapora.
* **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.
* **Jump-SN** — w tej podsieci znajduje się serwer przesiadkowy. Serwer przesiadkowy ma publiczny adres IP, z którym możesz nawiązać połączenie przy użyciu pulpitu zdalnego. Stamtąd możesz połączyć się (przy użyciu innego pulpitu zdalnego) z serwerem obciążeń.

![Infrastruktura sieci samouczka](media/tutorial-firewall-rules-portal/Tutorial_network.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Skonfiguruj reguły aplikacji, aby umożliwić dostęp do www.google.com
> * Konfigurowanie reguły sieci w celu umożliwienia dostępu do zewnętrznych serwerów DNS
> * Testowanie zapory

Jeśli wolisz, możesz wykonać tej procedury przy użyciu [witryny Azure portal](tutorial-firewall-deploy-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga uruchomienia programu PowerShell lokalnie. Musisz mieć zainstalowany moduł Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="set-up-the-network"></a>Konfigurowanie sieci

Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie utwórz sieć wirtualną, podsieci i serwery do obsługi testowania.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów zawiera wszystkie zasoby dla wdrożenia.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Ta sieć wirtualna ma trzy podsieci:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/24
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```

> [!NOTE]
> Minimalny rozmiar podsieci AzureFirewallSubnet to /26.

Teraz Utwórz sieć wirtualną:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne przesiadkową i obciążeń, a następnie umieść je w odpowiednich podsieciach.
Po wyświetleniu monitu wpisz nazwę użytkownika i hasło maszyny wirtualnej.

Utwórz maszynę wirtualną szybkie Srv.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Utwórz maszynę wirtualną obciążenia za pomocą publicznego adresu IP.
Po wyświetleniu monitu wpisz nazwę użytkownika i hasło maszyny wirtualnej.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Teraz można wdrożyć zaporę w sieci wirtualnej.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Utwórz tabelę, z wyłączyć propagację tras protokołu BGP

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

Zasada aplikacji umożliwia dostęp ruchu wychodzącego do www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Aby uzyskać więcej informacji, zobacz [Infrastrukturalne nazwy FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

Zasada sieci umożliwia dostęp ruchu wychodzącego do dwóch adresów IP na port 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Zmienianie podstawowego i pomocniczego adresu DNS dla interfejsu sieciowego **Srv-Work**

Do celów testowych w tej procedurze, skonfiguruj adresy DNS serwera podstawowego i pomocniczego. Nie jest ona ogólnych wymagań zapory usługi Azure.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testowanie zapory

Teraz należy sprawdzić, zapory, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. Należy pamiętać, prywatnego adresu IP dla **pracy Srv** maszyny wirtualnej:

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Pulpit zdalny, aby połączyć **szybkie Srv** maszynę wirtualną i zaloguj się. Z tego miejsca Otwórz Podłączanie pulpitu zdalnego z **pracy Srv** prywatny adres IP i logowania.

3. Na **pracy SRV**, Otwórz okno programu PowerShell i uruchom następujące polecenia:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Oba polecenia powinno zwrócić odpowiedzi, pokazujący, że zapytania DNS są przejściem przez zaporę.

1. Uruchom następujące polecenia:

   ```
   Invoke-WebRequest -Uri http://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri http://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Żądania www.google.com ma być pomyślnie wykonane, a żądania www.microsoft.com powinna zakończyć się niepowodzeniem. Oznacza to, że reguły zapory działają zgodnie z oczekiwaniami.

Teraz gdy masz pewność, czy działają reguły zapory:

* Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.
* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować swoje zasoby zapory do następnego samouczka, lub jeśli nie będą już potrzebne, Usuń **Test-PD-RG** grupę zasobów, aby usunąć wszystkie zasoby związane z zapory:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Dzienniki zapory usługi Azure Monitor](./tutorial-diagnostics.md)
