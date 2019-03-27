---
title: Konfigurowanie zawsze włączonych odbiorników grup dostępności — Microsoft Azure | Dokumentacja firmy Microsoft
description: Skonfiguruj odbiorników grup dostępności w modelu usługi Azure Resource Manager przy użyciu co najmniej jeden adres IP wewnętrznego modułu równoważenia obciążenia.
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
ms.date: 02/06/2019
ms.author: mikeray
ms.openlocfilehash: 822dce08d4555d9039ce310464ba49b6e3d4849c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480655"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Skonfiguruj co najmniej jeden Always On odbiorników grup dostępności — Resource Manager
W tym temacie przedstawiono sposób:

* Tworzenie wewnętrznego modułu równoważenia obciążenia dla grup dostępności programu SQL Server przy użyciu poleceń cmdlet programu PowerShell.
* Dodaj dodatkowe adresy IP do modułu równoważenia obciążenia dla więcej niż jednej grupy dostępności. 

Odbiornik grupy dostępności jest nazwa sieci wirtualnej, którą klienci łączą się dostępu do bazy danych. Na maszynach wirtualnych platformy Azure modułu równoważenia obciążenia zawiera adres IP dla odbiornika. Moduł równoważenia obciążenia kieruje ruch do wystąpienia programu SQL Server, która nasłuchuje na porcie sondowania. Zazwyczaj do grupy dostępności używa wewnętrznego modułu równoważenia obciążenia. Usługa Azure wewnętrznego modułu równoważenia obciążenia można hostować jeden lub wiele adresów IP. Każdego adresu IP korzysta z portów określonych sondowania. W tym dokumencie pokazano, jak utworzyć moduł równoważenia obciążenia lub Dodaj adresy IP do istniejącego modułu równoważenia obciążenia dla grup dostępności programu SQL Server przy użyciu programu PowerShell. 

Możliwość przypisywania wiele adresów IP do wewnętrznego modułu równoważenia obciążenia jest nowym użytkownikiem platformy Azure i jest dostępna tylko w modelu usługi Resource Manager. Aby wykonać to zadanie, musisz mieć wdrożony na maszynach wirtualnych platformy Azure w modelu usługi Resource Manager grupy dostępności programu SQL Server. Obie maszyny wirtualne programu SQL Server musi należeć do tego samego zestawu dostępności. Możesz użyć [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) można automatycznie utworzyć grupy dostępności w usłudze Azure Resource Manager. Ten szablon utworzy automatycznie grupy dostępności, w tym wewnętrznego modułu równoważenia obciążenia. Jeśli wolisz, możesz to zrobić [ręczne konfigurowanie zawsze włączonej grupy dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

W tym temacie wymaga grupy dostępności zostały już skonfigurowane.  

Powiązane tematy obejmują:

* [Konfigurowanie grup dostępności AlwaysOn na maszynie wirtualnej platformy Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna za pomocą usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Sprawdź wersję programu PowerShell

Przykłady w niniejszym artykule są badane za pomocą programu Azure PowerShell w wersji 5.4.1 modułu.

Sprawdź modułu programu PowerShell jest 5.4.1 lub nowszej.

Zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Skonfiguruj zaporę Windows

Skonfiguruj zaporę Windows, aby zezwolić na dostęp do programu SQL Server. Reguły zapory zezwalają na połączenia protokołu TCP do użycia portów w programie wystąpienia programu SQL Server i badania odbiornika. Aby uzyskać szczegółowe instrukcje, zobacz [skonfigurować zaporę Windows dla dostępu aparatu bazy danych](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Utwórz regułę ruchu przychodzącego dla portu programu SQL Server i port sondy.

Jeśli jesteś ograniczanie dostępu przy użyciu grupy zabezpieczeń sieci platformy Azure, upewnij się, że reguły Zezwalaj obejmują adresy IP maszyn wirtualnych serwera SQL w wewnętrznej bazie danych, pływający adres IP modułu równoważenia obciążenia adresów dla odbiornika grupy dostępności i adres IP podstawowe klastra, jeśli ma to zastosowanie.

## <a name="determine-the-load-balancer-sku-required"></a>Określić wymagane jednostki SKU modułu równoważenia obciążenia

[Usługa Azure load balancer](../../../load-balancer/load-balancer-overview.md) jest dostępna w 2 jednostki SKU: Podstawowa i standardowa. Zaleca się standardowego modułu równoważenia obciążenia. W przypadku maszyn wirtualnych w zestawie dostępności, podstawowego modułu równoważenia obciążenia jest dozwolony. Standardowego modułu równoważenia obciążenia wymaga, że wszystkie adresy IP maszyn wirtualnych za pomocą standardowe adresy IP.

Bieżący [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) dostępności grupy używa podstawowego modułu równoważenia obciążenia za pomocą podstawowych adresów IP.

Przykłady w niniejszym artykule Określ standardowego modułu równoważenia obciążenia. W przykładach skrypt zawiera `-sku Standard`.

```powershell
$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Aby utworzyć podstawowego modułu równoważenia obciążenia, należy usunąć `-sku Standard` z wiersza, który tworzy moduł równoważenia obciążenia. Na przykład:

```powershell
$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Przykładowy skrypt: Tworzenie wewnętrznego modułu równoważenia obciążenia przy użyciu programu PowerShell

> [!NOTE]
> Jeśli utworzono grupy dostępności z [szablonów Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), wewnętrznego modułu równoważenia obciążenia został już utworzony.

Poniższy skrypt programu PowerShell tworzy wewnętrznego modułu równoważenia obciążenia, konfiguruje reguły równoważenia obciążenia i ustawia adres IP modułu równoważenia obciążenia. Aby uruchomić skrypt, Otwórz program Windows PowerShell ISE, a następnie wklej skrypt w okienku skryptów. Użyj `Connect-AzAccount` zalogować się do programu PowerShell. Jeśli masz wiele subskrypcji platformy Azure, użyj `Select-AzSubscription ` Aby ustawić subskrypcję. 

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

## <a name="Add-IP"></a> Przykładowy skrypt: Dodaj adres IP do istniejącego modułu równoważenia obciążenia przy użyciu programu PowerShell
Aby użyć więcej niż jednej grupy dostępności, należy dodać dodatkowy adres IP do modułu równoważenia obciążenia. Poszczególnych adresów IP wymaga własne reguły równoważenia obciążenia, port sondy oraz port frontonu.

Port frontonu jest port, którego aplikacje za pośrednictwem nawiąż połączenie z wystąpieniem programu SQL Server. Adresy IP dla grupy dostępności innej można użyć tego samego portu frontonu.

> [!NOTE]
> W przypadku grup dostępności programu SQL Server poszczególnych adresów IP wymaga port sondy określonych. Na przykład jeśli jeden adres IP modułu równoważenia obciążenia używa port sondy 59999, nie inne adresy IP dla tego modułu równoważenia obciążenia można użyć port sondy 59999.

* Aby uzyskać informacje o limitach usługi równoważenia obciążenia, zobacz **adresy IP prywatnej frontonu na moduł równoważenia obciążenia** w obszarze [limity dotyczące sieci — usługi Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Aby uzyskać informacje o limitach grupy dostępności, zobacz [ograniczenia (grupy dostępności)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. Wewnętrznego modułu równoważenia obciążenia korzysta z portu odbiornika dla frontonu port równoważenia obciążenia. Ten port może być numer portu, którego nasłuchuje program SQL Server. Dla domyślnego wystąpienia programu SQL Server numer portu to 1433. Regułę grupy dostępności równoważenia obciążenia wymaga pływającego adresu IP (bezpośredni zwrot serwera), dzięki czemu portu zaplecza jest taki sam jak port frontonu. Zaktualizuj zmienne w danym środowisku. 

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

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ustaw port odbiornika w programie SQL Server Management Studio

1. Uruchom program SQL Server Management Studio i Połącz z repliką podstawową.

1. Przejdź do **AlwaysOn wysokiej dostępności** | **grup dostępności** | **odbiorników grup dostępności**. 

1. Powinna zostać wyświetlona nazwa odbiornika, który został utworzony w Menedżerze klastra trybu Failover. Kliknij prawym przyciskiem myszy odbiornik o nazwie, a następnie kliknij przycisk **właściwości**.

1. W **portu** polu Określ numer portu odbiornika grupy dostępności przy użyciu $EndpointPort było wcześniej używane (1433 była wartość domyślna), następnie kliknij przycisk **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testuj połączenie z odbiornikiem

Aby przetestować połączenie:

1. Protokół RDP do programu SQL Server, która znajduje się w tej samej sieci wirtualnej, ale nie jest właścicielem repliki. Może to być inny serwer SQL w klastrze.

1. Użyj **sqlcmd** narzędzie, aby przetestować połączenie. Na przykład, poniższy skrypt ustanawia **sqlcmd** połączenie z repliką podstawową, za pośrednictwem odbiornika z uwierzytelnianiem Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Jeśli odbiornik używa portu innego niż domyślny portu (1433), określ numer portu w parametrach połączenia. Na przykład następujące polecenie sqlcmd łączy odbiornika na porcie 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD automatycznie nawiązania połączenia z dowolnego wystąpienia programu SQL Server obsługuje replikę podstawową. 

> [!NOTE]
> Upewnij się, że port, który określisz jest otwarty na zaporze oba serwery SQL. Oba serwery wymagają regułę ruchu przychodzącego dla portu TCP, którego używasz. Zobacz [apletu Dodaj lub Edytuj regułę zapory](https://technet.microsoft.com/library/cc753558.aspx) Aby uzyskać więcej informacji. 
> 
> 

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia
Należy pamiętać, że następujące wytyczne dotyczące odbiornika grupy dostępności na platformie Azure przy użyciu wewnętrznego modułu równoważenia obciążenia:

* Przy użyciu wewnętrznego modułu równoważenia obciążenia możesz uzyskać dostęp tylko do odbiornika z w ramach tej samej sieci wirtualnej.

* Jeśli jesteś ograniczanie dostępu przy użyciu grupy zabezpieczeń sieci platformy Azure, upewnij się, że reguły Zezwalaj obejmują adresy IP maszyn wirtualnych serwera SQL w wewnętrznej bazie danych, pływający adres IP modułu równoważenia obciążenia adresów dla odbiornika grupy dostępności i adres IP podstawowe klastra, jeśli ma to zastosowanie.

## <a name="for-more-information"></a>Więcej informacji
Aby uzyskać więcej informacji, zobacz [dostępności Konfigurowanie zawsze włączonej grupie w maszynie Wirtualnej platformy Azure ręcznie](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
Użyj następujących poleceń cmdlet programu PowerShell do utworzenia wewnętrznego modułu równoważenia obciążenia maszyn wirtualnych platformy Azure.

* [Nowe AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) tworzy moduł równoważenia obciążenia. 
* [Nowe AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) tworzy konfigurację adresu IP frontonu dla modułu równoważenia obciążenia. 
* [Nowe AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) tworzy konfiguracji reguły dla modułu równoważenia obciążenia. 
* [Nowe AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) tworzy konfigurację puli adresów zaplecza dla modułu równoważenia obciążenia. 
* [Nowe AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) tworzy konfigurację sondowania dla modułu równoważenia obciążenia.
* [Usuń AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) usuwa modułu równoważenia obciążenia z grupy zasobów platformy Azure.
