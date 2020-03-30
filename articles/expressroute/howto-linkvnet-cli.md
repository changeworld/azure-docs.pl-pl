---
title: 'Azure ExpressRoute: Łączenie sieci wirtualnej z obwodem: wiersz polecenia'
description: W tym artykule pokazano, jak połączyć sieci wirtualne (sieci wirtualne) z obwodami usługi ExpressRoute przy użyciu modelu wdrażania Menedżera zasobów i interfejsu wiersza polecenia.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdd809bcba703dbd8f9ee1e7c18185fd20e4586f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476138"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Podłączanie sieci wirtualnej do obwodu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia

Ten artykuł ułatwia łączenie sieci wirtualnych (sieci wirtualnych) z obwodami usługi Azure ExpressRoute przy użyciu interfejsu wiersza polecenia. Aby połączyć się przy użyciu interfejsu wiersza polecenia platformy Azure, sieci wirtualne muszą być tworzone przy użyciu modelu wdrażania Menedżera zasobów. Mogą one być w tej samej subskrypcji lub część innej subskrypcji. Jeśli chcesz użyć innej metody do podłączenia sieci wirtualnej do obwodu usługi ExpressRoute, możesz wybrać artykuł z poniższej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Potrzebna jest najnowsza wersja interfejsu wiersza polecenia (CLI). Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Przed rozpoczęciem konfiguracji należy przejrzeć [wymagania wstępne,](expressroute-prerequisites.md) [wymagania dotyczące routingu](expressroute-routing.md)i [przepływy pracy.](expressroute-workflows.md)

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md) i włączyć obwód przez dostawcę łączności. 
  * Upewnij się, że masz skonfigurowany private peering platformy Azure dla obwodu. Zapoznaj się z artykułem [konfigurowania routingu,](howto-routing-cli.md) aby uzyskać instrukcje dotyczące routingu. 
  * Upewnij się, że prywatna komunikacja równorzędna platformy Azure jest skonfigurowana. Komunikacja równorzędna BGP między siecią a firmą Microsoft musi być w górę, aby można było włączyć łączność end-to-end.
  * Upewnij się, że masz sieć wirtualną i bramę sieci wirtualnej utworzone i w pełni aprowizowana. Postępuj zgodnie z instrukcjami, aby [skonfigurować bramę sieci wirtualnej dla usługi ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Pamiętaj, aby `--gateway-type ExpressRoute`użyć .

* Do standardowego obwodu usługi ExpressRoute można połączyć do 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym podczas korzystania ze standardowego obwodu usługi ExpressRoute. 

* Pojedyncza sieci wirtualnej mogą być połączone z maksymalnie czterech obwodów usługi ExpressRoute. Użyj poniższego procesu, aby utworzyć nowy obiekt połączenia dla każdego obwodu usługi ExpressRoute, z który się łączysz. Obwody usługi ExpressRoute mogą być w tej samej subskrypcji, różnych subskrypcji lub kombinacji obu.

* Jeśli włączysz dodatek Premium usługi ExpressRoute, możesz połączyć sieć wirtualną poza regionem geopolitycznym obwodu usługi ExpressRoute lub podłączyć większą liczbę sieci wirtualnych do obwodu usługi ExpressRoute. Aby uzyskać więcej informacji na temat dodatku premium, zobacz często zadawane [pytania](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w tej samej subskrypcji z obwodem

Bramę sieci wirtualnej można połączyć z obwodem usługi ExpressRoute, korzystając z przykładu. Przed uruchomieniem polecenia upewnij się, że brama sieci wirtualnej jest tworzona i jest gotowa do łączenia.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem

Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Poniższy rysunek przedstawia prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każda z mniejszych chmur w dużej chmurze jest używana do reprezentowania subskrypcji należących do różnych działów w organizacji. Każdy z działów w organizacji może używać własnej subskrypcji do wdrażania swoich usług — ale może udostępniać jeden obwód usługi ExpressRoute, aby połączyć się z powrotem do sieci lokalnej. Jeden dział (w tym przykładzie: IT) może być właścicielem obwodu usługi ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu usługi ExpressRoute.

> [!NOTE]
> Opłaty za łączność i przepustowość dla dedykowanego obwodu zostaną zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administracja - Właściciele obwodów i użytkownicy obwodów

"Właściciel obwodu" jest autoryzowanym użytkownikiem zasilania zasobu obwodu usługi ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być zrealizowane przez "Użytkowników obwodu". Użytkownicy obwodów są właścicielami bram sieci wirtualnej, które nie są w ramach tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą realizować autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma prawo do modyfikowania i cofania autoryzacji w dowolnym momencie. Po odwołaniu autoryzacji wszystkie połączenia łączy są usuwane z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację**

Właściciel obwodu tworzy autoryzację, która tworzy klucz autoryzacji, który może być używany przez użytkownika obwodu do łączenia swoich bram sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacja jest ważna tylko dla jednego połączenia.

W poniższym przykładzie pokazano, jak utworzyć autoryzację:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Odpowiedź zawiera klucz autoryzacji i stan:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Aby przejrzeć autoryzacje**

Właściciel obwodu może przejrzeć wszystkie uprawnienia wydane na określonym obwodzie, uruchamiając następujący przykład:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Aby dodać uprawnienia**

Właściciel obwodu można dodać autoryzacje przy użyciu następującego przykładu:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Aby usunąć autoryzacje**

Właściciel obwodu może odwołać/usunąć autoryzacje do użytkownika, uruchamiając następujący przykład:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

Użytkownik obwodu potrzebuje identyfikatora równorzędnego i klucza autoryzacji od właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzację połączenia**

Użytkownik obwodu może uruchomić następujący przykład, aby zrealizować autoryzację łącza:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzację połączenia**

Autoryzację można zwolnić, usuwając połączenie łączące obwód usługi ExpressRoute z siecią wirtualną.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia sieci wirtualnej
Można zaktualizować niektóre właściwości połączenia z siecią wirtualną. 

**Aby zaktualizować masę połączenia**

Sieć wirtualna może być podłączona do wielu obwodów usługi ExpressRoute. Ten sam prefiks może zostać wyświetlony z więcej niż jednego obwodu usługi ExpressRoute. Aby wybrać połączenie, które ma być wysyłane dla tego prefiksu, można zmienić *szerokość trasy* połączenia. Ruch zostanie wysłany na połączenie o najwyższej *szerokości trasy*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Zakres *routinguwaga* jest 0 do 32000. Wartość domyślna to 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurowanie programu ExpressRoute FastPath 
Jeśli obwód usługi ExpressRoute Jest włączony, możesz włączyć program [ExpressRoute FastPath,](expressroute-about-virtual-network-gateways.md) jeśli obwód usługi ExpressRoute jest włączony [direct,](expressroute-erdirect-about.md) a wirtualna brama newtork to Ultra Performance lub ErGw3AZ. FastPath poprawia preformance ścieżki danych, takich jak pakiety na sekundę i połączenia na sekundę między siecią lokalną i sieci wirtualnej. 

**Konfigurowanie programu FastPath przy nowym połączeniu**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Aktualizowanie istniejącego połączenia w celu włączenia programu FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
