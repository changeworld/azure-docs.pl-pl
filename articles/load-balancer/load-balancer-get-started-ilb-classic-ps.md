---
title: Tworzenie wewnętrznego modułu równoważenia obciążenia — PowerShell klasycznego
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć wewnętrzny moduł równoważenia obciążenia w klasycznym modelu wdrażania przy użyciu programu PowerShell
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms:custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: b864a4bf352c547779bb368650971fa8b805fca7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090964"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Wprowadzenie do tworzenia wewnętrznego modułu równoważenia obciążenia (klasycznego) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Program PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Usługi w chmurze](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Tworzenie zestawu wewnętrznego modułu równoważenia obciążenia dla maszyn wirtualnych

Aby utworzyć zestaw wewnętrznego modułu równoważenia obciążenia, a także serwery, które będą przesyłać do niego ruch, wykonaj następujące czynności:

1. Utwórz wystąpienie wewnętrznego równoważenia obciążenia, które będzie punktem końcowym przychodzącego ruchu sieciowego, aby obciążenie było zrównoważone między serwerami w zestawie o zrównoważonym obciążeniu.
2. Dodaj punkty końcowe odpowiadające maszynom wirtualnym, które będą otrzymywać ruch przychodzący.
3. Skonfiguruj serwery wysyłające ruch, którego obciążenie ma zostać zrównoważone, aby wysyłały ruch na wirtualny adres IP (VIP) wystąpienia wewnętrznego równoważenia obciążenia.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Krok 1: Utwórz wystąpienie wewnętrznego równoważenia obciążenia

W przypadku istniejącej usługi w chmurze lub usługi w chmurze wdrożonej w ramach regionalnej sieci wirtualnej można utworzyć wystąpienie wewnętrznego równoważenia obciążenia za pomocą następujących poleceń programu Windows PowerShell:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Należy zauważyć, że to polecenie cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) programu Windows PowerShell korzysta z zestawu parametrów DefaultProbe. Aby uzyskać więcej informacji na temat dodatkowych zestawów parametrów, zobacz artykuł [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Krok 2: Dodawanie punktów końcowych do wystąpienia wewnętrznego równoważenia obciążenia

Oto przykład:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Krok 3: Skonfiguruj serwery do wysyłania ruchu do nowego punktu końcowego wewnętrznego równoważenia obciążenia

Musisz skonfigurować serwery, których obciążenie ruchu ma być równoważone, aby korzystały z nowego adresu IP (VIP) wystąpienia wewnętrznego równoważenia obciążenia. Jest to adres, na którym nasłuchuje wystąpienie wewnętrznego równoważenia obciążenia. W większości przypadków należy po prostu dodać lub zmodyfikować rekord DNS dla adresu VIP wystąpienia wewnętrznego równoważenia obciążenia.

Jeśli określono adres IP podczas tworzenia wystąpienia wewnętrznego równoważenia obciążenia, adres VIP jest już dostępny. W przeciwnym razie można wyświetlić adres VIP za pomocą następujących poleceń:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Aby użyć tych poleceń, wypełnij wartości i usuń znaki < oraz >. Oto przykład:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Po wyświetleniu polecenia Get-AzureInternalLoadBalancer zapisz adres IP i wprowadź niezbędne zmiany w serwerach lub rekordach DNS, aby mieć pewność, że ruch jest przesyłany do adresu VIP.

> [!NOTE]
> Platforma Microsoft Azure używa statycznego, podlegającego publicznemu routingowi adresu IPv4 do różnych scenariuszy administracyjnych. Adres IP to 168.63.129.16. Ten adres IP nie powinien być blokowany przez zapory, ponieważ może to spowodować nieoczekiwane zachowanie.
> Jeśli chodzi o wewnętrzne równoważenie obciążenia Azure, ten adres IP jest używany przez sondy monitorujące wysyłane z modułu równoważenia obciążenia do określenia kondycji maszyn wirtualnych w zestawie o zrównoważonym obciążeniu. Jeśli sieciowa grupa zabezpieczeń jest używana do ograniczania ruchu do maszyn wirtualnych platformy Azure w zestawie o wewnętrznie zrównoważonym obciążeniu lub jest stosowana do podsieci sieci wirtualnej, upewnij się, że dodano regułę zabezpieczeń sieci zezwalającą na ruch z adresu 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Przykład wewnętrznego równoważenia obciążenia

Szczegółowy opis całego procesu tworzenia zestawu o zrównoważonym obciążeniu dla dwóch przykładowych konfiguracji znajduje się w kolejnych sekcjach.

### <a name="an-internet-facing-multi-tier-application"></a>Aplikacja wielowarstwowa, dostępna z Internetu

Chcesz zapewnić usługę bazy danych o zrównoważonym obciążeniu dla zestawu serwerów sieci Web dostępnych z Internetu. Oba zestawy serwerów są hostowane na jednej usłudze w chmurze Azure. Ruch sieciowy do portu 1433 TCP musi być rozdzielony na dwie maszyny wirtualne w warstwie bazy danych. Taką konfigurację przedstawiono na rysunku 1.

![Zestaw o wewnętrznie zrównoważonym obciążeniu dla warstwy bazy danych](./media/load-balancer-internal-getstarted/IC736321.png)

Ta konfiguracja składa się z następujących elementów:

* Istniejąca usługa w chmurze o nazwie mytestcloud hostująca maszyny wirtualne.
* Dwa istniejące serwery bazy danych o nazwach DB1 i DB2.
* Serwery sieci Web w warstwie sieci web połączone z serwerami bazy danych w warstwie bazy danych przy użyciu prywatnego adresu IP. Inną opcją jest użycie własnego serwera DNS dla sieci wirtualnej i ręczna rejestracja rekordu A dla zestawu wewnętrznego modułu równoważenia obciążenia.

Następujące polecenia pozwalają skonfigurować nowe wystąpienie wewnętrznego równoważenia obciążenia o nazwie **ILBset** i dodać punkty końcowe do maszyn wirtualnych odpowiadających dwóm serwerom bazy danych:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Usuwanie konfiguracji wewnętrznego równoważenia obciążenia

Aby usunąć maszynę wirtualną jako punkt końcowy z wystąpienia wewnętrznego modułu równoważenia obciążenia, użyj następujących poleceń:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Aby użyć tych poleceń, wypełnij wartości i usuń znaki < oraz >.

Oto przykład:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Aby usunąć wystąpienie wewnętrznego modułu równoważenia obciążenia z usługi w chmurze, użyj następujących poleceń:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Aby użyć tych poleceń, wypełnij wartość i usuń znaki < oraz >.

Oto przykład:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Dodatkowe informacje na temat poleceń cmdlet wewnętrznego modułu równoważenia obciążenia

Aby uzyskać dodatkowe informacje na temat poleceń cmdlet wewnętrznego równoważenia obciążenia, uruchom następujące polecenia w wierszu programu Windows PowerShell:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Kolejne kroki

[Configure a load balancer distribution mode using source IP affinity](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia przy użyciu koligacji źródłowych adresów IP)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)

