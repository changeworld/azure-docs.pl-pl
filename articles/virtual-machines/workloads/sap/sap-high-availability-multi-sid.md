---
title: Tworzenie konfiguracji z wieloma identyfikatorami SID SAP na platformie Azure | Dokumentacja firmy Microsoft
description: Przewodnik po konfiguracji — wiele identyfikatorów SID oprogramowanie SAP NetWeaver wysokiej dostępności na Windows maszyn wirtualnych
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fddb0f7cceea167885f56cf6ff5e8639bec1d937
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710351"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Utwórz konfigurację adresu oprogramowanie SAP NetWeaver — wiele identyfikatorów SID

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
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

We wrześniu 2016 roku firma Microsoft opublikowała funkcji, na której możesz zarządzać wiele wirtualnych adresów IP przy użyciu platformy Azure wewnętrznego modułu równoważenia obciążenia. Ta funkcja już istnieje w module równoważenia obciążenia zewnętrznych platformy Azure.

W przypadku wdrożenia SAP służy wewnętrznego modułu równoważenia obciążenia do tworzenia konfiguracji klastra Windows SAP ASCS/SCS, zgodnie z opisem w [przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide].

Ten artykuł koncentruje się na temat sposobu przenieść z jednej instalacji ASCS/SCS konfiguracji SAP — wiele identyfikatorów SID, instalując dodatkowe wystąpienia SAP ASCS/SCS klastrowane w istniejącym klastrze systemu Windows Server Failover Clustering (WSFC). Po zakończeniu tego procesu został skonfigurowany klaster SAP — wiele identyfikatorów SID.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Skonfigurowano już klaster usługi WSFC, używany do jednego wystąpienia SAP ASCS/SCS, zgodnie z opisem w [przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide] i jak pokazano na poniższym diagramie.

![Wystąpienia SAP ASCS/SCS wysokiej dostępności][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Architektura docelowa

Celem jest zainstalowanie wielu SAP ABAP ASCS lub SAP Java SCS klastrowanych wystąpień w tym samym klastrze usługi WSFC, jak przedstawiono poniżej:

![Wiele wystąpień SAP ASCS/SCS klastra na platformie Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Ma limitu liczby prywatnych adresów IP frontonu, dla każdej platformy Azure wewnętrznego modułu równoważenia obciążenia.
>
>Maksymalna liczba wystąpień SAP ASCS/SCS w jednym klastrze usługi WSFC jest równy maksymalna liczba prywatnych adresów IP frontonu, dla każdej platformy Azure wewnętrznego modułu równoważenia obciążenia.
>

Aby uzyskać więcej informacji na temat limitów modułu równoważenia obciążenia, zobacz "adresy IP prywatnej frontonu na moduł równoważenia obciążenia" w [limity dotyczące sieci: Azure Resource Manager][networking-limits-azure-resource-manager].

Pełne orientacja pozioma, z dwoma systemami SAP o wysokiej dostępności będzie wyglądać następująco:

![Konfiguracja wysokiej dostępności — wiele identyfikatorów SID SAP za pomocą dwóch systemów SAP identyfikatorów SID][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Instalator musi spełniać następujące warunki:
> - Wystąpienia SAP ASCS/SCS muszą współużytkować ten sam klaster usługi WSFC.
> - Każdy identyfikator SID DBMS musi mieć swój własny dedykowany klaster usługi WSFC.
> - Serwery aplikacji SAP, które należą do jednego systemu SAP identyfikatora SID muszą mieć własne dedykowanych maszyn wirtualnych.


## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury
Aby przygotować infrastrukturę, można zainstalować dodatkowe wystąpienia SAP ASCS/SCS z następującymi parametrami:

| Nazwa parametru | Value |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| Systemu SAP DBMS wewnętrznego modułu równoważenia obciążenia | PR5 |
| Nazwa hosta wirtualnego SAP | pr5-sap-cl |
| Adres IP hosta wirtualnego SAP ASCS/SCS (adres IP modułu równoważenia dodatkowego obciążenia platformy Azure) | 10.0.0.50 |
| Numer wystąpienia SAP ASCS/SCS | 50 |
| Port sondy wewnętrznego modułu równoważenia obciążenia na potrzeby dodatkowe wystąpienia SAP ASCS/SCS | 62350 |

> [!NOTE]
> W przypadku wystąpienia klastra SAP ASCS/SCS poszczególnych adresów IP wymaga port sondy unikatowy. Na przykład jeśli jeden adres IP w usłudze Azure wewnętrznego modułu równoważenia obciążenia używa port sondy 62300, żaden adres IP tego modułu równoważenia obciążenia można użyć port sondy 62300.
>
>Dla naszych potrzeb ponieważ port sondy 62300 jest już zarezerwowany, używamy port sondy 62350.

Można zainstalować dodatkowe wystąpienia SAP ASCS/SCS w istniejącym klastrze usługi WSFC, z dwoma węzłami:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Statyczny adres IP |
| --- | --- | --- |
| 1\. węzła klastra ASCS/SCS wystąpienia |pr1-ascs-0 |10.0.0.10 |
| 2nd węźle klastra w przypadku wystąpienia ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Utwórz nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS na serwerze DNS

Można utworzyć wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS przy użyciu następujących parametrów:

| Nowa nazwa hosta wirtualnego SAP ASCS/SCS | Skojarzony adres IP |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Nowe nazwy hosta i adresu IP są wyświetlane w Menedżerze DNS, jak pokazano na poniższym zrzucie ekranu:

![Lista Menedżera DNS wyróżnianie zdefiniowanych wpis DNS dla nowej SAP ASCS/SCS klastra wirtualnego nazwy i adresu TCP/IP][sap-ha-guide-figure-6004]

Procedura tworzenia wpisu DNS opisana szczegółowo w głównym [przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Nowy adres IP, który zostanie przypisany do nazwy hostów wirtualnych dodatkowe wystąpienia ASCS/SCS musi być taka sama jak nowy adres IP przypisany do modułu równoważenia obciążenia SAP Azure.
>
>W naszym scenariuszu adres IP jest 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Dodaj adres IP do istniejącego modułu równoważenia obciążenia wewnętrznego platformy Azure przy użyciu programu PowerShell

Aby utworzyć więcej niż jedno wystąpienie SAP ASCS/SCS, w tym samym klastrze usługi WSFC, Dodaj do istniejącego modułu równoważenia obciążenia platformy Azure wewnętrzny adres IP za pomocą programu PowerShell. Poszczególnych adresów IP wymaga własne reguły równoważenia obciążenia, port sondy, puli adresów IP frontonu i puli zaplecza.

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. Zaktualizuj zmienne programu PowerShell dla danego środowiska. Skrypt utworzy wszystkie niezbędne reguły równoważenia obciążenia dla wszystkich portów SAP ASCS/SCS.

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

![Nowej puli adresów IP frontonu w witrynie Azure portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Dodaj dyski do klastrowania maszyn i konfigurowanie udziału dysk klastrowy oprogramowanie SIOS

Należy dodać nowy dysk udział klastra, za każde dodatkowe wystąpienie SAP ASCS/SCS. Dla systemu Windows Server 2012 R2 dysku udział klastra usługi WSFC, obecnie w użyciu jest rozwiązanie programowe oprogramowanie SIOS DataKeeper.

Wykonaj następujące czynności:
1. Dodaj dodatkowy dysk lub dyski w tej samej wielkości (który jest potrzebny stripe) do każdego z węzłów klastra i sformatować je.
2. Konfigurowanie replikacji magazynu z oprogramowanie SIOS DataKeeper.

Ta procedura zakłada, że już zainstalowano oprogramowanie SIOS DataKeeper na komputerach klastra usługi WSFC. Po zainstalowaniu go teraz należy skonfigurować replikację między maszynami. Ten proces jest opisany szczegółowo w głównym [przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-8.12.3.3].  

![DataKeeper synchroniczne, dublowania dla nowych SAP ASCS/SCS współużytkować dysku][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Wdrażanie maszyn wirtualnych dla serwerów aplikacji SAP i klastra systemu DBMS

Aby zakończyć przygotowania infrastruktury do drugiego systemu SAP, wykonaj następujące czynności:

1. Wdrażanie dedykowanych maszyn wirtualnych dla serwerów aplikacji SAP i umieść je w swojej grupie dostępności dedykowanych.
2. Wdrażanie dedykowanych maszyn wirtualnych dla klastra systemu DBMS i umieść je w swojej grupie dostępności dedykowanych.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Zainstaluj drugiego systemu SAP SID2 NetWeaver

Pełny proces instalacji drugiego systemu SAP SID2 opisano w głównym [przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-9].

Procedura wysokiego poziomu, jest następujący:

1. [Zainstaluj na pierwszym węźle klastra SAP][sap-ha-guide-9.1.2].  
 W tym kroku jest instalowany SAP za pomocą wystąpienia ASCS/SCS wysokiej dostępności na **węzła klastra usługi WSFC istniejących 1**.

2. [Modyfikowanie profilu SAP ASCS/SCS wystąpienia][sap-ha-guide-9.1.3].

3. [Skonfiguruj port sondy][sap-ha-guide-9.1.4].  
 W tym kroku konfigurowana jest zasobem klastra SAP port sondy SAP SID2 IP przy użyciu programu PowerShell. Wykonaj tę konfigurację w jednym z węzłów klastra SAP ASCS/SCS.

4. [Install wystąpienie bazy danych] [sap-ha-guide-9.2].  
 W tym kroku jest instalowany system DBMS na dedykowany klaster usługi WSFC.

5. [Install drugim węźle klastra] [sap-ha-guide-9.3].  
 W tym kroku zainstalujesz SAP w wystąpieniach ASCS/SCS wysokiej dostępności na istniejący węzeł klastra usługi WSFC 2.

6. Otwórz porty zapory Windows wystąpienia SAP ASCS/SCS i ProbePort.  
 W obu węzłach klastra, które są używane dla wystąpień SAP ASCS/SCS otwierasz wszystkie porty zapory Windows, które są używane przez SAP ASCS/SCS. Te porty są wymienione w [przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-8.8].  
 Również otworzyć port sondy modułu równoważenia obciążenia wewnętrznego platformy Azure, czyli 62350 w naszym scenariuszu.

7. [Zmień typ uruchomienia wystąpienia usługi SAP Wywołujących Windows][sap-ha-guide-9.4].

8. [Instalowanie serwera podstawowego aplikacji SAP][sap-ha-guide-9.5] na nowym dedykowane maszyny Wirtualnej.

9. [Instalowanie serwera dodatkowych aplikacji SAP][sap-ha-guide-9.6] na nowym dedykowane maszyny Wirtualnej.

10. [Testowanie trybu failover wystąpienia SAP ASCS/SCS i replikacji oprogramowanie SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Następne kroki

- [Limity dotyczące sieci: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Wielu adresów VIP dla usługi Azure Load Balancer][load-balancer-multivip-overview]
- [Przewodnik dla wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide]
