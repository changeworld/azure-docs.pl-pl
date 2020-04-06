---
title: Konfigurowanie odbiorników grup dostępności & modułu równoważenia obciążenia (PowerShell)
description: Skonfiguruj odbiorniki grup dostępności w modelu usługi Azure Resource Manager przy użyciu wewnętrznego modułu równoważenia obciążenia z co najmniej jednym adresem IP.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: cabfc84d2bc0c9d08a457e67c0182d7550f04ceb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668884"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurowanie jednego lub więcej odbiorników grupy zawsze włączone — Menedżer zasobów
W tym temacie pokazano, jak:

* Utwórz wewnętrzny moduł równoważenia obciążenia dla grup dostępności programu SQL Server przy użyciu poleceń cmdlet programu PowerShell.
* Dodaj dodatkowe adresy IP do modułu równoważenia obciążenia dla więcej niż jednej grupy dostępności. 

Odbiornik grupy dostępności to nazwa sieci wirtualnej, z którą klienci łączą się w celu uzyskania dostępu do bazy danych. Na maszynach wirtualnych platformy Azure moduł równoważenia obciążenia przechowuje adres IP odbiornika. Moduł równoważenia obciążenia kieruje ruch do wystąpienia programu SQL Server, który nasłuchuje na porcie sondy. Zazwyczaj grupa dostępności używa wewnętrznego modułu równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia platformy Azure może obsługiwać jeden lub wiele adresów IP. Każdy adres IP używa określonego portu sondy. W tym dokumencie pokazano, jak używać programu PowerShell do tworzenia modułu równoważenia obciążenia lub dodawania adresów IP do istniejącego modułu równoważenia obciążenia dla grup dostępności programu SQL Server. 

Możliwość przypisywania wielu adresów IP do wewnętrznego modułu równoważenia obciążenia jest nowa na platformie Azure i jest dostępna tylko w modelu Usługi Resource Manager. Aby wykonać to zadanie, musisz wdrożyć grupę dostępności programu SQL Server na maszynach wirtualnych platformy Azure w modelu usługi Resource Manager. Obie maszyny wirtualne programu SQL Server muszą należeć do tego samego zestawu dostępności. Za pomocą [szablonu firmy Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) można automatycznie utworzyć grupę dostępności w usłudze Azure Resource Manager. Ten szablon automatycznie tworzy grupę dostępności, w tym wewnętrzny moduł równoważenia obciążenia. Jeśli wolisz, możesz [ręcznie skonfigurować grupę dostępności Zawsze włączone](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

W tym temacie wymaga, aby grupy dostępności były już skonfigurowane.  

Tematy pokrewne obejmują:

* [Konfigurowanie grup dostępności AlwaysOn w usłudze Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurowanie połączenia sieci wirtualnych przy użyciu usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Weryfikowanie wersji programu PowerShell

Przykłady w tym artykule są testowane przy użyciu modułu programu Azure PowerShell w wersji 5.4.1.

Sprawdź, czy moduł programu PowerShell ma być 5.4.1 lub nowszym.

Zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurowanie Zapory systemu Windows

Skonfiguruj Zaporę systemu Windows, aby zezwolić na dostęp do programu SQL Server. Reguły zapory zezwalają na połączenia TCP z portami używanymi przez wystąpienie programu SQL Server i sondę odbiornika. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie Zapory systemu Windows dla dostępu do aparatu bazy danych](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Utwórz regułę przychodzącą dla portu programu SQL Server i portu sondy.

Jeśli ograniczasz dostęp za pomocą grupy zabezpieczeń sieciowej platformy Azure, upewnij się, że reguły zezwalania obejmują adresy IP maszyny Wirtualnej programu SQL Server zaplecza i zmienne adresy IP modułu równoważenia obciążenia dla odbiornika AG i podstawowy adres IP klastra, jeśli ma to zastosowanie.

## <a name="determine-the-load-balancer-sku-required"></a>Określ wymaganą jednostkę SKU modułu równoważenia obciążenia

[Moduł równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-overview.md) jest dostępny w 2 jednostkach SKU: Basic & Standard. Zaleca się standardowy moduł równoważenia obciążenia. Jeśli maszyny wirtualne są w zestawie dostępności, podstawowy moduł równoważenia obciążenia jest dozwolony. Jeśli maszyny wirtualne znajdują się w strefie dostępności, wymagany jest standardowy moduł równoważenia obciążenia. Standardowy moduł równoważenia obciążenia wymaga, aby wszystkie adresy IP maszyn wirtualnych używały standardowych adresów IP.

Bieżący [szablon firmy Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) dla grupy dostępności używa podstawowego modułu równoważenia obciążenia z podstawowymi adresami IP.

   > [!NOTE]
   > Należy skonfigurować punkt [końcowy usługi,](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) jeśli używasz standardowego modułu równoważenia obciążenia i usługi Azure Storage dla monitora chmury. 


Przykłady w tym artykule określają standardowy moduł równoważenia obciążenia. W przykładach skrypt zawiera `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Aby utworzyć podstawowy moduł równoważenia obciążenia, usuń `-sku Standard` z wiersza, który tworzy moduł równoważenia obciążenia. Przykład:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Przykładowy skrypt: tworzenie wewnętrznego modułu równoważenia obciążenia za pomocą programu PowerShell

> [!NOTE]
> Jeśli grupa dostępności została utworzona z [szablonem firmy Microsoft,](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)wewnętrzny moduł równoważenia obciążenia został już utworzony.

Poniższy skrypt programu PowerShell tworzy wewnętrzny moduł równoważenia obciążenia, konfiguruje reguły równoważenia obciążenia i ustawia adres IP modułu równoważenia obciążenia. Aby uruchomić skrypt, otwórz program Windows PowerShell ISE i wklej skrypt w okienku Skrypt. Służy `Connect-AzAccount` do logowania się do programu PowerShell. Jeśli masz wiele subskrypcji platformy `Select-AzSubscription` Azure, użyj, aby ustawić subskrypcję. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Przykładowy skrypt: dodawanie adresu IP do istniejącego modułu równoważenia obciążenia za pomocą programu PowerShell
Aby użyć więcej niż jednej grupy dostępności, dodaj dodatkowy adres IP do modułu równoważenia obciążenia. Każdy adres IP wymaga własnej reguły równoważenia obciążenia, portu sondy i portu frontowego.

Port front-end jest portem używanym przez aplikacje do łączenia się z wystąpieniem programu SQL Server. Adresy IP dla różnych grup dostępności mogą używać tego samego portu front-end.

> [!NOTE]
> W przypadku grup dostępności programu SQL Server każdy adres IP wymaga określonego portu sondy. Na przykład jeśli jeden adres IP na moduł równoważenia obciążenia używa portu sondy 59999, żadne inne adresy IP na tym modułze równoważenia obciążenia nie mogą używać portu sondy 59999.

* Aby uzyskać informacje na temat limitów równoważenia obciążenia, zobacz **Prywatny adres IP frontonu na moduł równoważenia obciążenia** w obszarze [Limity sieci — Usługa Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Aby uzyskać informacje o limitach grup dostępności, zobacz [Ograniczenia (Grupy dostępności).](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. Moduł ILB używa portu odbiornika dla portu front-end równoważenia obciążenia. Ten port może być portem, na który nasłuchuje program SQL Server. W przypadku domyślnych wystąpień programu SQL Server port to 1433. Reguła równoważenia obciążenia dla grupy dostępności wymaga przestawnego adresu IP (bezpośredniego powrotu serwera), więc port zaplecza jest taki sam jak port front-end. Zaktualizuj zmienne dla swojego środowiska. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurowanie odbiornika

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ustawianie portu odbiornika w programie SQL Server Management Studio

1. Uruchom program SQL Server Management Studio i połącz się z repliką podstawową.

1. Przejdź do **pozycji Stalenajedniejszych** | **Availability Groups** | **odbiorników**grup dostępności o wysokiej dostępności . 

1. Nazwa odbiornika utworzona w Menedżerze klastrów trybu failover powinna być teraz widoczna. Kliknij prawym przyciskiem myszy nazwę odbiornika i kliknij polecenie **Właściwości**.

1. W polu **Port** określ numer portu dla odbiornika grupy dostępności, używając $EndpointPort używanej wcześniej (domyślnie 1433) kliknij przycisk **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testowanie połączenia ze odbiornikiem

Aby przetestować połączenie:

1. RDP do programu SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Może to być inny program SQL Server w klastrze.

1. Użyj narzędzia **sqlcmd,** aby przetestować połączenie. Na przykład następujący skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pośrednictwem odbiornika za pomocą uwierzytelniania systemu Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Jeśli odbiornik używa portu innego niż port domyślny (1433), określ port w ciągu połączenia. Na przykład następujące polecenie sqlcmd łączy się z odbiornikiem na porcie 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Połączenie SQLCMD automatycznie łączy się z którymkolwiek wystąpieniem programu SQL Server hostuje replikę podstawową. 

> [!NOTE]
> Upewnij się, że określony port jest otwarty na zaporze obu serwerów SQL. Oba serwery wymagają reguły przychodzącej dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub edytowanie reguły zapory.](https://technet.microsoft.com/library/cc753558.aspx) 
> 
> 

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia
Należy zwrócić uwagę na następujące wskazówki dotyczące odbiornika grup dostępności na platformie Azure przy użyciu wewnętrznego modułu równoważenia obciążenia:

* Za pomocą wewnętrznego modułu równoważenia obciążenia można uzyskać dostęp tylko do odbiornika z tej samej sieci wirtualnej.

* Jeśli ograniczasz dostęp za pomocą grupy zabezpieczeń sieciowej platformy Azure, upewnij się, że reguły zezwalania obejmują adresy IP maszyny Wirtualnej programu SQL Server zaplecza i zmienne adresy IP modułu równoważenia obciążenia dla odbiornika AG i podstawowy adres IP klastra, jeśli ma to zastosowanie.

* Tworzenie punktu końcowego usługi przy użyciu standardowego modułu równoważenia obciążenia za pomocą usługi Azure Storage dla monitora chmury. Aby uzyskać więcej informacji, zobacz [Udzielanie dostępu z sieci wirtualnej](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>Więcej informacji
Aby uzyskać więcej informacji, zobacz [ręczne konfigurowanie grupy dostępności zawsze włączone w usłudze Azure VM](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
Użyj następujących poleceń cmdlet programu PowerShell, aby utworzyć wewnętrzny moduł równoważenia obciążenia dla maszyn wirtualnych platformy Azure.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) tworzy moduł równoważenia obciążenia. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) tworzy konfigurację ip frontonu dla modułu równoważenia obciążenia. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) tworzy konfigurację reguły dla modułu równoważenia obciążenia. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) tworzy konfigurację puli adresów wewnętrznej bazy danych dla modułu równoważenia obciążenia. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) tworzy konfigurację sondy dla modułu równoważenia obciążenia.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) usuwa moduł równoważenia obciążenia z grupy zasobów platformy Azure.
