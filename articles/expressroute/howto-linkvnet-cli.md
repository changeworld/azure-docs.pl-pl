---
title: 'Łączenie sieci wirtualnej z obwodem usługi ExpressRoute: CLI: Azure| Microsoft Docs'
description: W tym artykule pokazano, jak połączyć sieci wirtualne (Vnet) obwodów usługi ExpressRoute przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.reviewer: anzaman
ms.custom: seodec18
ms.openlocfilehash: d858c83fb6669e5348b4256931e080656be0ebad
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621058"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute za pomocą interfejsu wiersza polecenia

Ten artykuł ułatwia łączenia sieci wirtualnych (Vnet) platformy Azure obwodami usługi ExpressRoute za pomocą interfejsu wiersza polecenia. Aby połączyć przy użyciu wiersza polecenia platformy Azure, można utworzyć sieci wirtualnych przy użyciu modelu wdrażania usługi Resource Manager. One być w tej samej subskrypcji lub części innej subskrypcji. Jeśli chcesz użyć innej metody do łączenia z siecią wirtualną z obwodem usługi ExpressRoute, można wybrać artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Potrzebujesz najnowszej wersji interfejsu wiersza polecenia (CLI). Aby uzyskać więcej informacji, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Musisz sprawdzić [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md) i który powinien zostać włączony przez dostawcę połączenia. 
  * Upewnij się, że prywatnej komunikacji równorzędnej Azure skonfigurowany dla obwodu. Zobacz [Konfigurowanie routingu](howto-routing-cli.md) artykuł, aby uzyskać instrukcje routingu. 
  * Upewnij się, że skonfigurowano prywatnej komunikacji równorzędnej Azure. Komunikację równorzędną BGP między siecią i firmy Microsoft muszą być uruchomione, dzięki czemu można włączyć łączność end-to-end.
  * Upewnij się, że masz sieci wirtualnej i bramy sieci wirtualnej, utworzona i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Należy użyć `--gateway-type ExpressRoute`.

* Maksymalnie 10 sieciami wirtualnymi można połączyć standardowy obwód usługi ExpressRoute. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geograficznymi, korzystając z standardowy obwód usługi ExpressRoute. 

* Pojedynczej sieci wirtualnej można połączyć maksymalnie cztery obwodów usługi ExpressRoute. Użyj poniżej proces, aby utworzyć nowy obiekt połączenia dla każdy obwód usługi ExpressRoute, z którą jest nawiązywane. Obwody usługi ExpressRoute może być w tej samej subskrypcji, w różnych subskrypcjach lub kombinacji obu.

* Po włączeniu dodatku premium usługi ExpressRoute, możesz połączyć sieć wirtualną poza region geopolityczny obwodu usługi ExpressRoute lub połączyć z większej liczby sieci wirtualnych na obwód usługi ExpressRoute. Aby uzyskać więcej informacji na temat używania dodatku premium, zobacz [— często zadawane pytania](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w tej samej subskrypcji z obwodem

Możesz połączyć bramę sieci wirtualnej z obwodem usługi ExpressRoute przy użyciu przykładu. Upewnij się, że brama sieci wirtualnej jest tworzony i jest gotowy do konsolidacji przed uruchomieniem polecenia.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem

Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Na poniższym rysunku przedstawiono prosty schemat działa jak udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każdy z mniejszych chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji. Każdego z działów w organizacji można użyć własnej subskrypcji do wdrażania usług —, ale mogą udostępniać pojedynczy obwód usługi ExpressRoute do łączenia z powrotem do sieci lokalnej. Jednego działu (w tym przykładzie: IT) mogą być właścicielami obwód usługi ExpressRoute. Inne subskrypcje w ramach organizacji, można użyć z obwodem usługi ExpressRoute.

> [!NOTE]
> Połączeniami i przepustowością opłat za obwód dedykowany będą stosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne współdzielą ten sam przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administrowanie — obwodu właścicieli i użytkowników obwodu

Właściciela obwodu jest autoryzowanym użytkownikiem Power zasobu obwodu usługi ExpressRoute. Właściciela obwodu tworzyć autoryzacje do zrealizowania przez "Użytkownicy obwodu". Użytkownicy obwodu są właścicielami bram sieci wirtualnych, które nie znajdują się w innej subskrypcji niż obwód usługi ExpressRoute. Użytkownicy obwodu można realizować autoryzacje (jeden autoryzację dla pojedynczej sieci wirtualnej).

Właściciela obwodu ma uprawnienia do modyfikowania i odwołać autoryzacji w dowolnym momencie. Odwołany autoryzacji wszystkich połączeń są usuwane z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzacji**

Właściciela obwodu tworzy autoryzacji, co spowoduje utworzenie klucza autoryzacji, który może służyć przez użytkowników obwodu połączyć ich bramy sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacja jest prawidłowe dla tylko jedno połączenie.

Poniższy przykład pokazuje, jak utworzyć autoryzacji:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Odpowiedź zawiera klucz autoryzacji i stanu:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Aby zapoznać się z autoryzacjami**

Właściciela obwodu, można przejrzeć wszystkie autoryzacje są wydawane w ramach określonego obwodu przez uruchomieniem następującego przykładu:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Aby dodać autoryzacji**

Aby dodać autoryzacji właściciela obwodu korzystając z następującego przykładu:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Aby usunąć autoryzacji**

Właściciela obwodu można odwołać/usuwanie autoryzacji dla użytkownika, uruchamiając poniższy przykład:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

Obwód trzeba podać identyfikator elementu równorzędnego i klucza autoryzacji z właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzacji połączenia**

Użytkowników obwodu, można uruchomić poniższy przykład Zrealizuj autoryzację łącza:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzacji połączenia**

Autoryzacji można zwolnić, usuwając połączenia prowadzący obwód usługi ExpressRoute w sieci wirtualnej.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia sieci wirtualnej
Możesz zaktualizować niektóre właściwości połączenia sieci wirtualnej. 

**Aby zaktualizować wagi połączenia**

Sieci wirtualne mogą być połączone wieloma obwodami usługi ExpressRoute. Ten sam prefiks może pojawić się z więcej niż jeden obwód usługi ExpressRoute. Aby wybór połączenia do wysyłania ruchu przeznaczonego dla tego prefiksu, możesz zmienić *RoutingWeight* połączenia. Ruch będzie wysyłany w połączeniu z najwyższą *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Zakres *RoutingWeight* 0 do 32 000. Wartość domyślna to 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurowanie usługi ExpressRoute FastPath 
Możesz włączyć [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) Jeśli obwód usługi ExpressRoute znajduje się na [ExpressRoute bezpośrednio](expressroute-erdirect-about.md) i bramy sieci wirtualnej newtork jest największa wydajność lub ErGw3AZ. FastPath zwiększa preformance ścieżki danych, takich jak pakietów na sekundę i połączeń na sekundę między siecią lokalną i siecią wirtualną. 

> [!NOTE] 
> Jeśli masz już połączenia sieci wirtualnej, ale nie zostały włączone FastPath musisz usunąć połączenie z siecią wirtualną i Utwórz nowe. 
> 
>  

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```


## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
