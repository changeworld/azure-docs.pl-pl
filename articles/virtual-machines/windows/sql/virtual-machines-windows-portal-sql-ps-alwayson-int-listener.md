---
title: Konfigurowanie zawsze na odbiorniki grupy dostępności — Microsoft Azure | Dokumentacja firmy Microsoft
description: Skonfiguruj odbiorniki grupy dostępności w modelu usługi Azure Resource Manager, używając wewnętrznego modułu równoważenia obciążenia z co najmniej jeden adres IP.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/22/2017
ms.author: mikeray
ms.openlocfilehash: 11aecd9b2bc1bc1521a0e27fc3cd06fe7426a26d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Skonfiguruj co najmniej jeden zawsze na dostępność odbiorniki grupy - Resource Manager
W tym temacie przedstawiono sposób:

* Utwórz wewnętrznego modułu równoważenia obciążenia dla grupy dostępności programu SQL Server przy użyciu poleceń cmdlet programu PowerShell.
* Dodaj dodatkowe adresy IP do modułu równoważenia obciążenia dla więcej niż jednej grupy dostępności. 

Odbiornik grupy dostępności to nazwa sieci wirtualnej, z którym łączą się klienci dla dostępu do bazy danych. Na maszynach wirtualnych Azure usługi równoważenia obciążenia zawiera adres IP dla odbiornika. Load balancer kieruje ruch z wystąpieniem programu SQL Server nasłuchuje na porcie sondowania. Zazwyczaj grupa dostępności używa wewnętrznego modułu równoważenia obciążenia. Azure wewnętrznego modułu równoważenia obciążenia może obsługiwać jeden lub wiele adresów IP. Każdy adres IP używa portu określonych sondowania. Ten dokument przedstawia sposób tworzenia modułu równoważenia obciążenia lub Dodaj adresy IP do istniejącej usługi równoważenia obciążenia dla grupy dostępności programu SQL Server przy użyciu programu PowerShell. 

Możliwość przypisania wielu adresów IP do wewnętrznego modułu równoważenia obciążenia jest nowa w systemie Azure i jest dostępna tylko w modelu usługi Resource Manager. Aby zakończyć to zadanie, należy mieć wdrożonych na maszynach wirtualnych Azure w modelu usługi Resource Manager grupy dostępności programu SQL Server. Maszyny wirtualne zarówno programu SQL Server musi należeć do tego samego zestawu dostępności. Można użyć [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) można automatycznie utworzyć grupy dostępności usługi Azure Resource Manager. Ten szablon tworzy automatycznie grupy dostępności, w tym wewnętrznego modułu równoważenia obciążenia. Jeśli wolisz, możesz [ręczne konfigurowanie zawsze włączonej grupy dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

W tym temacie wymaga już skonfigurowania grup dostępności.  

Tematy pokrewne obejmują:

* [Konfigurowanie grup dostępności AlwaysOn w maszynie Wirtualnej platformy Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurowanie połączenia do wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Konfigurowanie Zapory systemu Windows
Konfigurowanie Zapory systemu Windows, aby zezwolić na dostęp do programu SQL Server. Reguły zapory zezwala na połączenia TCP użytku portów w wystąpieniu programu SQL Server i badania odbiornika. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie Zapory systemu Windows dla dostępu aparatu bazy danych](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Utwórz regułę ruchu przychodzącego dla portu programu SQL Server i port sondy.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Przykładowy skrypt: Tworzenie wewnętrznego modułu równoważenia obciążenia przy użyciu programu PowerShell
> [!NOTE]
> Jeśli utworzono grupy dostępności z [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), wewnętrzny moduł równoważenia obciążenia został już utworzony. 
> 
> 

Poniższy skrypt programu PowerShell tworzy wewnętrznego modułu równoważenia obciążenia, konfiguruje reguły równoważenia obciążenia i ustawia adres IP usługi równoważenia obciążenia. Aby uruchomić skrypt, Otwórz program Windows PowerShell ISE, a następnie wklej skrypt w okienku skryptów. Użyj `Connect-AzureRmAccount` logować się do programu PowerShell. Jeśli masz wiele subskrypcji Azure, użyj `Select-AzureRmSubscription ` można ustawić subskrypcji. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

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

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Przykładowy skrypt: Dodaj adres IP do istniejącej usługi równoważenia obciążenia przy użyciu programu PowerShell
Aby używać więcej niż jednej grupy dostępności, Dodaj dodatkowy adres IP do modułu równoważenia obciążenia. Każdy adres IP wymaga własne reguły równoważenia obciążenia, port sondy oraz portu frontonu.

Frontonu jest port aplikacje używają do nawiązania połączenia z wystąpieniem serwera SQL. Adresy IP dla grup dostępności różnych można używać tego samego portu frontonu.

> [!NOTE]
> Dla grup dostępności programu SQL Server każdy adres IP wymaga sondowania określonego portu. Na przykład jeśli jeden adres IP modułu równoważenia obciążenia używa portu sondowania 59999, nie adresów IP w tej usługi równoważenia obciążenia można użyć portu sondowania 59999.

* Uzyskać informacji dotyczących ograniczeń usługi równoważenia obciążenia, zobacz **adresu IP frontonu prywatne dla usługi równoważenia obciążenia** w obszarze [sieci limity — usługi Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Aby uzyskać informacji na temat limitów grupy dostępności, zobacz [ograniczenia (grupy dostępności)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Poniższy skrypt dodaje nowy adres IP do istniejącej usługi równoważenia obciążenia. ILB korzysta z portu odbiornika dla portów frontonu równoważenia obciążenia. Ten port może być port, którego nasłuchuje program SQL Server. Dla domyślnego wystąpienia programu SQL Server numer portu to 1433. Reguły dla grupy dostępności równoważenia obciążenia wymaga pływającego adresu IP (bezpośredni zwrot serwera), więc portu zaplecza jest taki sam jak port frontonu. Zaktualizuj zmienne w danym środowisku. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurowanie odbiornika

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ustaw numer portu odbiornika w programie SQL Server Management Studio

1. Uruchom program SQL Server Management Studio i połącz się repliką podstawową.

1. Przejdź do **AlwaysOn wysokiej dostępności** | **grup dostępności** | **odbiorniki grupy dostępności**. 

1. Powinna zostać wyświetlona nazwa odbiornika utworzonego w Menedżerze klastra trybu Failover. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij przycisk **właściwości**.

1. W **portu** polu Określ numer portu dla odbiornika grupy dostępności, używając $EndpointPort użyto wcześniej (1433 jest wartość domyślna), następnie kliknij przycisk **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testuj połączenie odbiornika

Aby przetestować połączenie:

1. RDP do programu SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Może to być inny serwer SQL w klastrze.

1. Użyj **sqlcmd** narzędzia do testowania połączenia. Na przykład poniższy skrypt ustanawia **sqlcmd** połączenie z repliką podstawową za pośrednictwem odbiornika z uwierzytelnianiem systemu Windows:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Jeśli odbiornik używa portu innego niż domyślny port (1433), określ numer portu w parametrach połączenia. Na przykład następujące polecenie narzędzia sqlcmd łączy odbiornik portu 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Połączenia narzędzia SQLCMD automatycznie łączy się z innego wystąpienia programu SQL Server obsługuje replikę podstawową. 

> [!NOTE]
> Upewnij się, że port, który określisz jest otwarty na zaporze oba serwery SQL. Oba serwery wymagają regułę ruchu przychodzącego dla portu TCP, którego używasz. Zobacz [Dodawanie lub edytowanie reguły zapory](http://technet.microsoft.com/library/cc753558.aspx) Aby uzyskać więcej informacji. 
> 
> 

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia
Należy zauważyć, że następujące wytyczne dotyczące odbiornika grupy dostępności na platformie Azure przy użyciu wewnętrznego modułu równoważenia obciążenia:

* Wewnętrzny moduł równoważenia obciążenia należy tylko uzyskiwanie dostępu do odbiornika ze w tej samej sieci wirtualnej.


## <a name="for-more-information"></a>Więcej informacji
Aby uzyskać więcej informacji, zobacz [dostępności Konfigurowanie zawsze włączonej grupy w maszynie Wirtualnej platformy Azure ręcznie](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
Użyj następujących poleceń cmdlet programu PowerShell, aby utworzyć wewnętrznego modułu równoważenia obciążenia maszyn wirtualnych platformy Azure.

* [Nowy AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) tworzy moduł równoważenia obciążenia. 
* [Nowy AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) tworzy konfigurację IP frontonu modułu równoważenia obciążenia. 
* [Nowy AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) tworzona jest reguła Konfiguracja usługi równoważenia obciążenia. 
* [Nowy AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) tworzy Konfiguracja puli adresów zaplecza, usługi równoważenia obciążenia. 
* [Nowy AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) tworzy badania konfiguracji usługi równoważenia obciążenia.
* [Usuń AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) usuwa modułu równoważenia obciążenia z grupy zasobów platformy Azure.
