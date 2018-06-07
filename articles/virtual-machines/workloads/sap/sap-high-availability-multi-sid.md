---
title: Tworzenie konfiguracji wielu SID SAP na platformie Azure | Dokumentacja firmy Microsoft
description: Przewodnik po konfiguracji wielu SID SAP NetWeaver wysokiej dostępności w systemie Windows maszyny wirtualne
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
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
ms.openlocfilehash: 570c427166c07e93d3c8a7d3be079d9007238243
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656826"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Tworzenie konfiguracji wielu SID SAP NetWeaver

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

We wrześniu 2016 roku firma Microsoft opublikowała funkcja, której mogą zarządzać wielu wirtualnych adresów IP przy użyciu usługi Azure wewnętrznego modułu równoważenia obciążenia. Ta funkcja jest już istnieje w Azure zewnętrznej usługi równoważenia obciążenia.

W przypadku wdrożenia SAP, służy wewnętrznego modułu równoważenia obciążenia do tworzenia konfiguracji klastra systemu Windows dla programu SAP ASCS/SCS, zgodnie z opisem w [przewodnik wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide].

W tym artykule przedstawiono sposób przenoszenia z jednej instalacji ASCS/SCS do konfiguracji wielu SID SAP, instalując dodatkowe wystąpienia SAP ASCS/SCS w klastrze do istniejącego klastra systemu Windows Server Failover Clustering (WSFC). Po zakończeniu tego procesu został skonfigurowany klaster identyfikatora SID multi SAP.


## <a name="prerequisites"></a>Wymagania wstępne
Został już skonfigurowany klaster usługi WSFC, służący do jednego wystąpienia SAP ASCS/SCS, zgodnie z opisem w [przewodnik wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych Windows] [ sap-ha-guide] i jak pokazano na tym diagramie.

![Wystąpienie programu SAP ASCS/SCS wysokiej dostępności][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Architektura docelowa

Celem jest zainstalowanie wielu ASCS ABAP SAP lub SAP Java SCS klastrowanego wystąpienia tego samego klastra usługi WSFC, jako ilustrowane tutaj:

![Wiele wystąpień programu SAP ASCS/SCS klastrowanego na platformie Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Istnieje ograniczona liczba prywatnych adresów IP frontonu, dla każdej platformy Azure wewnętrznego modułu równoważenia obciążenia.
>
>Maksymalna liczba wystąpień SAP ASCS/SCS w jednym klastrze usługi WSFC jest taki sam, jak maksymalna liczba prywatnych adresów IP frontonu, dla każdej platformy Azure wewnętrznego modułu równoważenia obciążenia.
>

Aby uzyskać więcej informacji na temat limitów modułu równoważenia obciążenia, zobacz "frontonu prywatnego adresu IP dla modułu równoważenia obciążenia" w [limity sieci: usługi Azure Resource Manager][networking-limits-azure-resource-manager].

Zakończenie pozioma z dwoma systemami SAP wysokiej dostępności będzie wyglądać następująco:

![SAP wysokiej dostępności multi-SID Instalatorowi systemu SAP identyfikatorów SID][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Instalator musi spełniać następujące warunki:
> - Wystąpienia SAP ASCS/SCS muszą współużytkować tego samego klastra usługi WSFC.
> - Każdy identyfikator SID systemu DBMS musi mieć własny dedykowany klaster usługi WSFC.
> - Serwery aplikacji SAP, które należą do jednego systemu SAP identyfikatora SID musi mieć własne dedykowanych maszyn wirtualnych.


## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury
Aby przygotować infrastrukturę, można zainstalować dodatkowe wystąpienia SAP ASCS/SCS z następującymi parametrami:

| Nazwa parametru | Wartość |
| --- | --- |
| SAP ASCS/SCS SID |PR1-lb-ascs |
| System DBMS SAP wewnętrznego modułu równoważenia obciążenia | PR5 |
| Nazwa hosta wirtualnego SAP | cl-pr5-sap |
| Adres IP hosta wirtualnego SAP ASCS/SCS (adres IP usługi równoważenia obciążenia Azure dodatkowe) | 10.0.0.50 |
| SAP ASCS/SCS liczby wystąpień | 50 |
| Port sondy ILB dodatkowe wystąpienia SAP ASCS/SCS | 62350 |

> [!NOTE]
> Każdy adres IP wystąpienia klastra programu SAP ASCS/SCS, wymaga port sondy unikatowy. Na przykład jeśli jeden adres IP na Azure wewnętrznego modułu równoważenia obciążenia używa portu sondowania 62300, żaden adres IP w tej usługi równoważenia obciążenia można użyć portu sondowania 62300.
>
>W naszym celów ponieważ port sondy 62300 jest już zarezerwowany, użyto port sondy 62350.

Można zainstalować dodatkowe wystąpienia SAP ASCS/SCS w istniejącym klastrze usługi WSFC z dwoma węzłami:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Statyczny adres IP |
| --- | --- | --- |
| 1. węzła klastra dla wystąpienia ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| 2 węzła klastra dla wystąpienia ASCS/SCS |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Utwórz nazwę hosta wirtualnego wystąpienia klastra programu SAP ASCS/SCS na serwerze DNS

Można utworzyć wpis DNS dla nazwy hostów wirtualnych wystąpienia ASCS/SCS przy użyciu następujących parametrów:

| Nowa nazwa hosta wirtualnego SAP ASCS/SCS | Skojarzony adres IP |
| --- | --- | --- |
|cl-pr5-sap |10.0.0.50 |

Nowa nazwa hosta i adresu IP są wyświetlane w Menedżerze DNS, jak pokazano na poniższym zrzucie ekranu:

![Lista Menedżera DNS wyróżnianie zdefiniowanych wpis DNS dla nowej SAP ASCS/SCS klastra wirtualnego nazwę i adres TCP/IP][sap-ha-guide-figure-6004]

Procedurę tworzenia wpisu DNS jest również opisane w artykule szczegółowo w głównym [przewodnik wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Nowy adres IP przypisany do nazwy hostów wirtualnych dodatkowe wystąpienia ASCS/SCS musi być taka sama jak nowego adresu IP przypisanego do usługi równoważenia obciążenia SAP Azure.
>
>W naszym scenariuszu adres IP jest 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Dodaj adres IP do istniejących Azure wewnętrznego modułu równoważenia obciążenia przy użyciu programu PowerShell

Aby utworzyć więcej niż jedno wystąpienie SAP ASCS/SCS w tym samym klastrze usługi WSFC, Dodaj adres IP do istniejących Azure wewnętrznego modułu równoważenia obciążenia za pomocą programu PowerShell. Każdy adres IP wymaga własne reguły równoważenia obciążenia, port sondy frontonu puli adresów IP i puli zaplecza.

Poniższy skrypt dodaje nowy adres IP do istniejącej usługi równoważenia obciążenia. Zaktualizuj zmienne środowiska PowerShell. Skrypt spowoduje utworzenie wszystkich niezbędnych reguły równoważenia obciążenia dla wszystkich portów SAP ASCS/SCS.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po uruchomieniu skryptu, wyniki są wyświetlane w portalu Azure, jak pokazano na poniższym zrzucie ekranu:

![Nowa pula IP frontonu, w portalu Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Dodawanie dysków do klastra maszyny i skonfiguruj udział dysk klastrowy SIOS

Należy dodać nowy dysk udziału klastra dla każdego dodatkowego wystąpienia SAP ASCS/SCS. Dla systemu Windows Server 2012 R2 dysk udziału klastra usługi WSFC obecnie w użyciu jest rozwiązanie programowe SIOS DataKeeper.

Wykonaj następujące czynności:
1. Dodaj dodatkowy dysk lub dyski o tym samym rozmiarze (co trzeba paskowych) do każdego z węzłów klastra, a ich format.
2. Skonfigurować SIOS DataKeeper replikacji magazynu.

W tej procedurze przyjęto założenie, zainstalowano SIOS DataKeeper na komputerach klastra usługi WSFC. Po zainstalowaniu go teraz należy skonfigurować replikacji między komputerami. Proces jest szczegółowo opisane w głównym [przewodnik wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-8.12.3.3].  

![DataKeeper synchroniczne dublowania dla nowych ASCS SAP/SCS współużytkować dysku][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Wdrażanie maszyn wirtualnych dla serwerów aplikacji SAP i bazami danych klastra

Aby ukończyć przygotowanie infrastruktury do drugiego systemu SAP, wykonaj następujące czynności:

1. Wdrażanie dedykowanych maszyn wirtualnych dla serwerów aplikacji SAP i umieść je w grupie własne dedykowane dostępności.
2. Wdrażanie dedykowanych maszyn wirtualnych klastra systemu DBMS i umieść je w grupie własne dedykowane dostępności.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalowanie systemu SAP SID2 NetWeaver drugi

Zakończ proces instalacji drugiego systemu SAP SID2 jest opisana w głównym [przewodnik wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-9].

Procedury ogólne wygląda następująco:

1. [Zainstaluj pierwszym węźle klastra SAP][sap-ha-guide-9.1.2].  
 W tym kroku jest instalowany SAP przy użyciu wystąpienia ASCS/SCS wysokiej dostępności na **węzeł klastra usługi WSFC istniejących 1**.

2. [Modyfikowanie profilu SAP wystąpienia ASCS/SCS][sap-ha-guide-9.1.3].

3. [Skonfiguruj port sondy][sap-ha-guide-9.1.4].  
 W tym kroku konfigurowana jest zasób klastra SAP port sondy SAP SID2 IP przy użyciu programu PowerShell. Tej konfiguracji należy wykonać na jednym z węzłów klastra SAP ASCS/SCS.

4. [Zainstalować wystąpienie bazy danych] [sap-ha przewodnik-9.2].  
 W tym kroku jest instalowany system DBMS na dedykowany klaster usługi WSFC.

5. [Instaluj drugiego węzła klastra] [sap-ha przewodnik-9.3].  
 W tym kroku jest instalowany przy użyciu wystąpienia ASCS/SCS wysokiej dostępności na istniejący węzeł klastra usługi WSFC 2 SAP.

6. Otwórz porty zapory systemu Windows dla programu SAP ASCS/SCS wystąpienia i ProbePort.  
 Na obu węzłów klastra używane dla wystąpień SAP ASCS/SCS otwierasz wszystkie porty zapory systemu Windows, które są używane przez SAP ASCS/SCS. Porty te są wymienione w [przewodnik wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych Windows][sap-ha-guide-8.8].  
 Również otworzyć portu sondy modułu równoważenia obciążenia wewnętrznego platformy Azure, który 62350 w naszym scenariuszu jest.

7. [Zmień typ uruchomienia wystąpienia usługi Windows Wywołujących SAP][sap-ha-guide-9.4].

8. [Zainstaluj serwer aplikacji głównej SAP] [ sap-ha-guide-9.5] na nowym dedykowane maszyny Wirtualnej.

9. [Instalowanie serwera aplikacji dodatkowe SAP] [ sap-ha-guide-9.6] na nowym dedykowane maszyny Wirtualnej.

10. [Testowanie trybu failover wystąpienia programu SAP ASCS/SCS i replikacji SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Kolejne kroki

- [Ograniczenia sieciowe: Usługa Azure Resource Manager][networking-limits-azure-resource-manager]
- [Moduł równoważenia obciążenia z wieloma wirtualnymi adresami IP na platformie Azure][load-balancer-multivip-overview]
- [Przewodnik dla wysokiej dostępności programu SAP NetWeaver na maszynach wirtualnych systemu Windows][sap-ha-guide]
