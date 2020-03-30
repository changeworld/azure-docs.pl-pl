---
title: Tworzenie konfiguracji sap multi-SID na platformie Azure | Dokumenty firmy Microsoft
description: Przewodnik po wysokiej dostępności konfiguracji sap NetWeaver multi-SID na maszynach wirtualnych systemu Windows
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7938f7db22f004a0bf6cdf2e22dc8e103896719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617393"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Tworzenie konfiguracji sap NetWeaver z wieloma identyfikatorami SID

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

We wrześniu 2016 r. firma Microsoft wydała funkcję, w której można zarządzać wieloma wirtualnymi adresami IP przy użyciu wewnętrznego modułu równoważenia obciążenia platformy Azure. Ta funkcja już istnieje w zewnętrznym modułem równoważenia obciążenia platformy Azure.

Jeśli masz wdrożenie SAP, możesz użyć wewnętrznego modułu równoważenia obciążenia, aby utworzyć konfigurację klastra systemu Windows dla SAP ASCS/SCS, zgodnie [z instrukcją na temat wysokiej dostępności SAP NetWeaver na maszynach wirtualnych systemu Windows][sap-ha-guide].

W tym artykule skupiono się na tym, jak przenieść się z pojedynczej instalacji ASCS/SCS do konfiguracji wielu identyfikatorów SAP, instalując dodatkowe wystąpienia klastrowane SAP ASCS/SCS w istniejącym klastrze windows server failover clustering (WSFC). Po zakończeniu tego procesu skonfigurowano klaster sap z wieloma identyfikatorami SID.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Klaster WSFC, który jest używany dla jednego wystąpienia SAP ASCS/SCS, jak omówiono w [przewodniku o wysokiej dostępności SAP NetWeaver na maszynach wirtualnych systemu Windows][sap-ha-guide] i jak pokazano na tym diagramie.

![Wystąpienie SAP ASCS/SCS o wysokiej dostępności][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Architektura docelowa

Celem jest zainstalowanie wielu wystąpień klastrowanych SAP ABAP ASCS lub SAP Java SCS w tym samym klastrze WSFC, jak pokazano tutaj:

![Wiele wystąpień klastrowanych SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Istnieje limit liczby prywatnych adresów IP front-end dla każdego modułu równoważenia obciążenia wewnętrznego platformy Azure.
>
>Maksymalna liczba wystąpień SAP ASCS/SCS w jednym klastrze WSFC jest równa maksymalnej liczbie prywatnych adresów IP front-end dla każdego modułu równoważenia obciążenia wewnętrznego platformy Azure.
>

Aby uzyskać więcej informacji na temat limitów równoważenia obciążenia, zobacz "Prywatny frontonowy adres IP na moduł równoważenia obciążenia" w [limitach sieci: Usługa Azure Resource Manager][networking-limits-azure-resource-manager].

Pełny krajobraz z dwoma systemami SAP o wysokiej dostępności wyglądałby następująco:

![Konfiguracja sap o wysokiej dostępności wielu identyfikatorów SID z dwoma identyfikatorami SID systemu SAP][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Konfiguracja musi spełniać następujące warunki:
> - Wystąpienia SAP ASCS/SCS muszą współużytkować ten sam klaster WSFC.
> - Każdy identyfikator SID usługi DBMS musi mieć własny dedykowany klaster WSFC.
> - Serwery aplikacji SAP, które należą do jednego identyfikatora SID systemu SAP, muszą mieć własne dedykowane maszyny wirtualne.


## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury
Aby przygotować infrastrukturę, można zainstalować dodatkowe wystąpienie SAP ASCS/SCS z następującymi parametrami:

| Nazwa parametru | Wartość |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS | PR5 |
| Nazwa hosta wirtualnego SAP | pr5-sap-cl |
| Adres IP hosta wirtualnego SAP ASCS/SCS (dodatkowy adres IP modułu równoważenia obciążenia platformy Azure) | 10.0.0.50 |
| Numer wystąpienia SAP ASCS/SCS | 50 |
| Port sondy równoważenia obciążenia sieciowego dla dodatkowego wystąpienia SAP ASCS/SCS | 62350 |

> [!NOTE]
> W przypadku wystąpień klastra SAP ASCS/SCS każdy adres IP wymaga unikatowego portu sondy. Na przykład jeśli jeden adres IP na wewnętrznym modułem równoważenia obciążenia platformy Azure używa portu sondy 62300, żaden inny adres IP na tym modułze równoważenia obciążenia nie może używać portu sondy 62300.
>
>Do naszych celów, ponieważ port sondy 62300 jest już zarezerwowany, używamy portu sondy 62350.

W istniejącym klastrze WSFC można zainstalować dodatkowe wystąpienia SAP ASCS/SCS za pomocą dwóch węzłów:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Statyczny adres IP |
| --- | --- | --- |
| 1 węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| Drugi węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS na serwerze DNS

Wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS można utworzyć przy użyciu następujących parametrów:

| Nowa nazwa wirtualnego hosta SAP ASCS/SCS | Skojarzony adres IP |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Nowa nazwa hosta i adres IP są wyświetlane w Menedżerze DNS, jak pokazano na poniższym zrzucie ekranu:

![Lista Menedżera DNS z wyróżnioniem zdefiniowanego wpisu DNS dla nowej nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-6004]

Procedura tworzenia wpisu DNS jest również szczegółowo opisana w głównym [przewodniku o wysokiej dostępności SAP NetWeaver na maszynach wirtualnych systemu Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Nowy adres IP przypisany do nazwy hosta wirtualnego dodatkowego wystąpienia ASCS/SCS musi być taki sam jak nowy adres IP przypisany do modułu równoważenia obciążenia platformy SAP Azure.
>
>W naszym scenariuszu adres IP to 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Dodawanie adresu IP do istniejącego wewnętrznego modułu równoważenia obciążenia platformy Azure przy użyciu programu PowerShell

Aby utworzyć więcej niż jedno wystąpienie SAP ASCS/SCS w tym samym klastrze WSFC, użyj programu PowerShell, aby dodać adres IP do istniejącego wewnętrznego modułu równoważenia obciążenia platformy Azure. Każdy adres IP wymaga własnych reguł równoważenia obciążenia, portu sondy, puli adresów IP frontowego i puli zaplecza.

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. Zaktualizuj zmienne programu PowerShell dla swojego środowiska. Skrypt utworzy wszystkie potrzebne reguły równoważenia obciążenia dla wszystkich portów SAP ASCS/SCS.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po uruchomieniu skryptu wyniki są wyświetlane w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Nowa pula adresów IP front-end w witrynie Azure portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Dodawanie dysków do komputerów klastrowych i konfigurowanie dysku udziału klastra SIOS

Należy dodać nowy dysk udziału klastra dla każdego dodatkowego wystąpienia SAP ASCS/SCS. W systemie Windows Server 2012 R2 dysk udziałów klastrowych WSFC jest obecnie używany jako rozwiązanie programowe SIOS DataKeeper.

Wykonaj następujące czynności:
1. Dodaj dodatkowy dysk lub dyski o tym samym rozmiarze (które należy rozebrać) do każdego z węzłów klastra i sformatować je.
2. Skonfiguruj replikację magazynu za pomocą programu SIOS DataKeeper.

W tej procedurze przyjęto założenie, że program SIOS DataKeeper został już zainstalowany na komputerach klastrowych WSFC. Jeśli został zainstalowany, należy teraz skonfigurować replikację między komputerami. Proces ten jest szczegółowo opisany w głównym [przewodniku wysokiej dostępności SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide-8.12.3.3].  

![Synchronizowanie datakeeper dla nowego dysku współużytkującego SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Wdrażanie maszyn wirtualnych dla serwerów aplikacji SAP i klastra DBMS

Aby zakończyć przygotowanie infrastruktury dla drugiego systemu SAP, wykonaj następujące czynności:

1. Wdrażaj dedykowane maszyny wirtualne dla serwerów aplikacji SAP i umieszczaj je we własnej dedykowanej grupie dostępności.
2. Wdrażaj dedykowane maszyny wirtualne dla klastra DBMS i umieść je we własnej dedykowanej grupie dostępności.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Zainstaluj drugi system SAP SID2 NetWeaver

Pełny proces instalacji drugiego systemu SAP SID2 jest opisany w głównym [przewodniku po wysokiej dostępności SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide-9].

Procedura wysokiego poziomu jest następująca:

1. [Zainstaluj pierwszy węzeł klastra SAP][sap-ha-guide-9.1.2].  
 W tym kroku instalujesz sap z wystąpieniem ASCS/SCS o wysokiej dostępności w **istniejącym węźle klastra WSFC 1**.

2. [Zmodyfikuj profil SAP wystąpienia ASCS/SCS][sap-ha-guide-9.1.3].

3. [Konfigurowanie portu sondy][sap-ha-guide-9.1.4].  
 W tym kroku konfigurujesz port sondy SAP za pomocą zasobu klastra SAP SAP SAP-SID2-IP przy użyciu programu PowerShell. Wykonaj tę konfigurację w jednym z węzłów klastra SAP ASCS/SCS.

4. [Instalowanie wystąpienia bazy danych] [sap-ha-guide-9.2].  
 W tym kroku instalujesz system DBMS w dedykowanym klastrze WSFC.

5. [Instalowanie drugiego węzła klastra] [sap-ha-guide-9.3].  
 W tym kroku instalujesz sap z wysokiej dostępności ASCS/SCS wystąpienie w istniejącym węźle klastra WSFC 2.

6. Otwórz porty Zapory systemu Windows dla wystąpienia SAP ASCS/SCS i ProbePort.  
 W obu węzłach klastra, które są używane dla wystąpień SAP ASCS/SCS, są otwierane wszystkie porty Zapory systemu Windows, które są używane przez SAP ASCS/SCS. Te porty są wymienione w [przewodniku dla wysokiej dostępności SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide-8.8].  
 Otwórz również port sondy modułu równoważenia obciążenia platformy Azure, który jest 62350 w naszym scenariuszu.

7. [Zmień typ początkowy wystąpienia usługi SAP ERS Windows][sap-ha-guide-9.4].

8. [Zainstaluj podstawowy serwer aplikacji SAP][sap-ha-guide-9.5] na nowej dedykowanej maszynie wirtualnej.

9. [Zainstaluj serwer aplikacji SAP na][sap-ha-guide-9.6] nowej dedykowanej maszynie wirtualnej.

10. [Przetestuj proces pracy awaryjnej wystąpienia SAP ASCS/SCS i replikację SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Następne kroki

- [Limity sieci: Usługa Azure Resource Manager][networking-limits-azure-resource-manager]
- [Wiele adresów VIP dla modułu równoważenia obciążenia platformy Azure][load-balancer-multivip-overview]
- [Przewodnik dotyczący wysokiej dostępności maszyn wirtualnych SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide]
