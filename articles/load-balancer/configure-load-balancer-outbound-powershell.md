---
title: Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego przy użyciu programu Azure PowerShell
titleSuffix: Azure Load Balancer
description: W tym artykule pokazano, jak skonfigurować równoważenie obciążenia i reguły ruchu wychodzącego w standardowym równoważeniu obciążenia przy użyciu programu Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225446"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w standardowym równoważeniu obciążenia przy użyciu programu Azure PowerShell

W tym artykule pokazano, jak skonfigurować reguły ruchu wychodzącego w standardowym równoważku obciążenia przy użyciu programu Azure PowerShell.  

Po zakończeniu tego artykułu scenariusz, zasób modułu równoważenia obciążenia zawiera dwa front ends i ich skojarzone reguły. Masz jeden frontołów dla ruchu przychodzącego i inny front-end dla ruchu wychodzącego.  

Każdy front-end odwołuje się do publicznego adresu IP. W tym scenariuszu publiczny adres IP dla ruchu przychodzącego różni się od adresu dla ruchu wychodzącego.   Reguła równoważenia obciążenia zapewnia tylko równoważenie obciążenia przychodzącego. Reguła ruchu wychodzącego steruje wychodzącym translacji adresów sieciowych (NAT) dla maszyny Wirtualnej.  

Scenariusz używa dwóch pul zaplecza: jeden dla ruchu przychodzącego i jeden dla ruchu wychodzącego. Te pule ilustrują możliwości i zapewniają elastyczność dla scenariusza.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure
Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Następnie postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tworzenie grupy zasobów przy użyciu [programu New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Grupa zasobów platformy Azure jest kontenerem logicznym, w którym są wdrażane zasoby platformy Azure. Zasoby są następnie zarządzane z grupy.

Poniższy przykład tworzy grupę zasobów o nazwie *myresourcegroupoutbound* w lokalizacji *eastus2:*

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną o nazwie *myvnetoutbound*. Nazwij jego *podsieć mysubnetoutbound*. Umieść go w *myresourcegroupoutbound* za pomocą [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) i [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Tworzenie przychodzącego publicznego adresu IP 

Aby uzyskać dostęp do aplikacji internetowej w Internecie, potrzebujesz publicznego adresu IP dla modułu równoważenia obciążenia. Standardowy moduł równoważenia obciążenia obsługuje tylko standardowe publiczne adresy IP. 

Użyj [New-AzPublicIpAddress,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) aby utworzyć standardowy publiczny adres IP o nazwie *mypubliczny* w *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP 

Utwórz standardowy adres IP dla konfiguracji wychodzącej modułu równoważenia obciążenia, używając [adresu New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

W tej sekcji wyjaśniono, jak utworzyć i skonfigurować następujące składniki modułu równoważenia obciążenia:
  - Front-end IP, który odbiera przychodzący ruch sieciowy na moduł równoważenia obciążenia
  - Pula zaplecza, w której front-endowy adres IP wysyła ruch sieciowy z równoważenia obciążenia
  - Pula zaplecza dla łączności wychodzącej
  - Sonda kondycji, która określa kondycję wystąpień maszyny wirtualnej zaplecza
  - Reguła ruchu przychodzącego modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu na maszynach wirtualnych
  - Reguła wychodząca modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu z maszyn wirtualnych

### <a name="create-an-inbound-front-end-ip"></a>Tworzenie przychodzącego adresu IP front-end
Utwórz przychodzącą konfigurację ip frontonu dla modułu równoważenia obciążenia za pomocą [funkcji New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Moduł równoważenia obciążenia powinien zawierać przychodzącą konfigurację IP front-end o nazwie *myfrontendinbound*. Skojarz tę konfigurację z publicznym adresem IP *mypubliczny.*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Tworzenie wychodzącego adresu IP front-endu
Utwórz wychodzącą konfigurację ip frontonu dla modułu równoważenia obciążenia za pomocą [funkcji New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Ten moduł równoważenia obciążenia powinien zawierać wychodzącą konfigurację IP front-end o nazwie *myfrontendoutbound*. Skojarz tę konfigurację z publicznym adresem IP *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Tworzenie przychodzącej puli zaplecza
Utwórz pulę zaplecza dla modułu równoważenia obciążenia przy użyciu [funkcji New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nazwij basen *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Tworzenie wychodzącej puli zaplecza
Użyj następującego polecenia, aby utworzyć inną pulę adresów zaplecza w celu zdefiniowania łączności wychodzącej dla puli maszyn wirtualnych przy użyciu [funkcji New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nazwij tę pulę *bepooloutbound*. 

Tworząc oddzielną pulę wychodzącą, zapewniasz maksymalną elastyczność. Ale można pominąć ten krok i używać tylko *przychodzące bepoolinbound* jeśli wolisz.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny Wirtualnej, które nie powiedzie się sprawdza sondy jest usuwany z modułu równoważenia obciążenia, dopóki nie przejdzie z powrotem do trybu online i sprawdzić sondy określa, że jest w dobrej kondycji. 

Aby monitorować kondycję maszyn wirtualnych, należy utworzyć sondę kondycji przy użyciu [programu New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresów IP front-end dla ruchu przychodzącego i puli zaplecza w celu odebrania ruchu. Definiuje również wymagany port źródłowy i docelowy. 

Utwórz regułę równoważenia obciążenia o nazwie *myinboundlbrule* przy użyciu [funkcji New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Ta reguła będzie słuchać portu 80 w puli front-end *myfrontendinbound*. Będzie również używać portu 80 do wysyłania ruchu sieciowego z równoważenia obciążenia do puli adresów zaplecza *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Ta reguła równoważenia obciążenia wyłącza automatyczny wychodzący bezpieczny NAT (SNAT) z powodu parametru **-DisableOutboundSNAT.** Wychodzący translator z prawa najazdowego jest dostarczany tylko przez regułę ruchu wychodzącego.

### <a name="create-an-outbound-rule"></a>Tworzenie reguły dla ruchu wychodzącego

Reguła wychodząca definiuje publiczny adres IP frontona, który jest reprezentowany przez front-end *myfrontendoutbound*. Ten frontołów będzie używany dla całego ruchu wychodzącego nat, a także puli zaplecza, do której ma zastosowanie reguła.  

Użyj następującego polecenia, aby utworzyć regułę wychodzącą *myoutboundrule* dla wychodzącej translacji sieciowej wszystkich maszyn wirtualnych (w konfiguracjach adresów SIECIOWYCH kart sieciowych) w puli zaplecza *bepool.*  Polecenie zmienia limit czasu bezczynnego ruchu wychodzącego z 4 do 15 minut. Przydziela 10 000 portów SNAT zamiast 1024. Aby uzyskać więcej informacji, zobacz [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Jeśli nie chcesz używać oddzielnej puli wychodzącej, możesz zmienić argument puli adresów w poprzednim poleceniu, aby określić *$bepoolin.*  Zalecamy użycie oddzielnych pul, aby konfiguracja wynikowa była elastyczna i czytelna.

### <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Użyj następującego polecenia, aby utworzyć moduł równoważenia obciążenia dla przychodzącego adresu IP przy użyciu [programu New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nazwij moduł równoważenia obciążenia *lb*. Powinna ona zawierać przychodzącą konfigurację ip front-end. Jego back-end pool *bepoolinbound* powinny być skojarzone z publiczny adres IP *mypubliczny,* który został utworzony w poprzednim kroku.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

W tym momencie można kontynuować dodawanie maszyn wirtualnych do pul zaplecza *bepoolinbound* i *bepooloutbound,* aktualizując konfigurację IP odpowiednich zasobów karty sieciowej. Zaktualizuj konfigurację zasobu za pomocą [dodatku AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie jest już potrzebna grupa zasobów, moduł równoważenia obciążenia i powiązane zasoby, można je usunąć za pomocą [programu Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono standardowy moduł równoważenia obciążenia, skonfigurowano reguły ruchu przychodzące i wychodzące moduły równoważenia obciążenia i skonfigurowano sondę kondycji maszyn wirtualnych w puli zaplecza. 

Aby dowiedzieć się więcej, przejdź do [samouczków dotyczących modułu równoważenia obciążenia usługi Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
