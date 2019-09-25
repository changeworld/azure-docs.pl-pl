---
title: Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego przy użyciu Azure PowerShell
titlesuffix: Azure Load Balancer
description: W tym artykule opisano sposób konfigurowania równoważenia obciążenia i reguł ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262624"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w ramach standardowego modułu równoważenia obciążenia za pomocą Azure PowerShell

W tym artykule opisano sposób konfigurowania reguł ruchu wychodzącego w standardowym module równoważenia obciążenia przy użyciu Azure PowerShell.  

Gdy wszystko będzie gotowe, zasób modułu równoważenia obciążenia zawiera dwa Frontony i skojarzone z nimi reguły: jeden dla ruchu przychodzącego i innego dla ruchu wychodzącego.  Każdym frontonem odwołuje się do publicznego adresu IP i używa tego scenariusza, inny publiczny adres IP dla ruchu przychodzącego i wychodzącego.   Reguły równoważenia obciążenia zapewnia tylko dla ruchu przychodzącego równoważenia obciążenia i reguły ruchu wychodzącego kontroluje NAT dla ruchu wychodzącego podane dla maszyny Wirtualnej.  W tym artykule są używane dwie oddzielne pule zaplecza, jeden dla ruchu przychodzącego i jeden dla ruchu wychodzącego, aby zilustrować możliwości i zapewnić elastyczność w tym scenariuszu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Połącz z kontem platformy Azure
Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myresourcegroupoutbound* w *eastus2* lokalizacji:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną o nazwie *myvnetoutbound* z podsiecią o nazwie *mysubnetoutbound* w *myresourcegroupoutbound* za pomocą polecenia [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) i [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Utwórz publiczny adres IP dla ruchu przychodzącego 

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. W ramach standardowego modułu równoważenia obciążenia obsługiwane są tylko standardowe publiczne adresy IP. Użyj [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) , aby utworzyć standardowy publiczny adres IP o nazwie *mypublicipinbound* w *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP 

Utwórz standardowy adres IP dla konfiguracji wychodzącej frontonu modułu równoważenia obciążenia przy użyciu polecenia [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - Adres IP do serwera sieci Web, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  - Pula zaplecza, w której adres IP frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  - Pula zaplecza dla łączności wychodzącej. 
  - Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  - Reguły modułu równoważenia obciążenia dla ruchu przychodzącego, który definiuje sposób dystrybucji ruchu do maszyn wirtualnych.
  - Reguły modułu równoważenia obciążenia ruchu wychodzącego, który definiuje sposób dystrybucji ruchu z maszyn wirtualnych.

### <a name="create-inbound-frontend-ip"></a>Utwórz adres IP frontonu dla ruchu przychodzącego
Utwórz wychodzącą konfigurację adresu IP frontonu dla modułu równoważenia obciążenia za pomocą usługi [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , która zawiera konfigurację PRZYCHODZĄCEGO adresu IP frontonu o nazwie *myfrontendinbound* , która jest skojarzona z publicznym adresem *IP mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Tworzenie adresu IP frontonu ruchu wychodzącego
Utwórz wychodzącą konfigurację adresu IP frontonu dla modułu równoważenia obciążenia za pomocą usługi [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) , która obejmuje wychodzącą konfigurację adresu IP frontonu o nazwie *myfrontendoutbound* , która jest skojarzona z publicznym adresem *IP mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Utwórz pulę zaplecza dla ruchu przychodzącego
Utwórz pulę przychodzącą wewnętrznej bazy danych dla modułu równoważenia obciążenia za pomocą elementu [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) o nazwie *bepoolinbound*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Utwórz pulę zaplecza wychodzącego
Utwórz dodatkową pulę adresów zaplecza, aby zdefiniować łączność wychodzącą dla puli maszyn wirtualnych z [nowym AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) o nazwie *bepooloutbound*. Utworzenie oddzielnej puli wychodzącej zapewnia maksymalną elastyczność, ale można pominąć ten krok i używać tylko *bepoolinbound* dla ruchu przychodzącego.  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) , aby monitorować kondycję maszyn wirtualnych. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Utwórz regułę równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu dla ruchu przychodzącego oraz pulę zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myinboundlbrule* za pomocą elementu [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) do nasłuchiwania na porcie 80 w puli frontonu *myfrontendinbound* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli *adresów zaplecza. bepoolinbound* również przy użyciu portu 80. 

>[!NOTE]
>Ta zasada równoważenia obciążenia wyłącza automatyczne wychodzące NAT w wyniku tej reguły z parametrem **-DisableOutboundSNAT** . NAT dla ruchu wychodzącego jest dostępna wyłącznie przez reguły ruchu wychodzącego.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Tworzenie reguły ruchu wychodzącego

Regułę dla ruchu wychodzącego definiuje publicznego adresu IP frontonu, reprezentowany przez frontonu *myfrontendoutbound*, który będzie używany dla całego ruchu wychodzącego NAT, a także puli zaplecza, do którego zostanie zastosowana ta reguła.  Utwórz regułę dla ruchu wychodzącego *myoutboundrule* sieciowe dotyczące połączeń wychodzących tłumaczenia wszystkich maszyn wirtualnych (konfiguracji adresów IP kart Sieciowych) w *bepool* puli zaplecza.  Poniższe polecenie również zmieni wychodzące limitu czasu bezczynności od 4 do 15 minut, a także przydziela 10000 SNAT porty zamiast 1024.  Aby uzyskać więcej informacji, zapoznaj się z tematem [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) .

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Jeśli nie chcesz używać oddzielnej puli wychodzącej, możesz zmienić argument puli adresów w poprzednim poleceniu, aby określić *$bepoolin* .  Zalecamy używanie oddzielnych pul do zapewnienia elastyczności i czytelności powstającej konfiguracji.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz moduł równoważenia obciążenia z przychodzącym adresem IP przy użyciu polecenia [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) o nazwie *lb* , która zawiera konfigurację przychodzącego adresu IP frontonu oraz pulę zaplecza *bepoolinbound* skojarzoną z publicznym adresem *IP mypublicipinbound* utworzony w poprzednim kroku.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

W tym momencie można kontynuować dodawanie maszyn wirtualnych do puli zaplecza *bepoolinbound* __i__ *bepooloutbound* przez aktualizację konfiguracji protokołu IP odpowiednich zasobów kart sieciowych przy użyciu polecenia [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) .

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie standardowego modułu równoważenia obciążenia, skonfigurowane zasady ruchu przychodzącego modułu równoważenia obciążenia, skonfigurowane i sondę kondycji dla maszyn wirtualnych w puli zaplecza. Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do samouczków dotyczących usługi równoważenia obciążenia Azure.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
