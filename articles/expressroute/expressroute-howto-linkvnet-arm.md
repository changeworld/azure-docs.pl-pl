---
title: 'Połączyć sieć wirtualną z obwodem usługi ExpressRoute: środowiska PowerShell: Azure | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualne) do obwody usługi ExpressRoute, przy użyciu modelu wdrażania usługi Resource Manager i programu PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 354f7c455e1a2846bbdd63fa12b1cc01e2a1b9c5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877555"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Połączenie wirtualnej sieci do obwodu usługi ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł pomaga połączyć sieci wirtualnych (sieci wirtualne) do usługi Azure ExpressRoute obwodów przy użyciu modelu wdrażania usługi Resource Manager i programu PowerShell. Sieci wirtualne może być w tej samej subskrypcji lub częścią innej subskrypcji. W tym artykule przedstawiono również sposób aktualizowania łącza sieci wirtualnej. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Zainstaluj najnowszą wersję programu Azure PowerShell modułów. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

* Przegląd [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md) i mieć obwodu włączane przez dostawcą połączenia. 
  * Upewnij się, że masz prywatnej komunikacji równorzędnej platformy Azure skonfigurowane dla obwodu. Zobacz [Konfigurowanie routingu](expressroute-howto-routing-arm.md) artykułu instrukcje routingu. 
  * Upewnij się, że Azure prywatnej komunikacji równorzędnej jest skonfigurowana i komunikację równorzędną BGP między siecią a Microsoft jest uruchomiony, dzięki czemu można włączyć łączność end-to-end.
  * Upewnij się, że masz sieci wirtualnej i Brama sieci wirtualnej utworzone i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa elementu GatewayType "ExpressRoute", nie sieci VPN.

* Standardowa obwodu ExpressRoute można połączyć maksymalnie 10 sieciami wirtualnymi. Wszystkie wirtualne sieci musi być w tym samym regionie geograficznymi, gdy przy użyciu standardowych obwodu usługi expressroute. 

* Pojedynczy sieci wirtualnej może odnosić się do maksymalnie cztery obwody usługi ExpressRoute. Umożliwia utworzenie nowego obiektu połączenia dla każdego obwodu ExpressRoute, który jest nawiązywane z procesem opisanym niżej. Obwody usługi ExpressRoute, można w tej samej subskrypcji, różnych subskrypcji lub kombinację obu.

* Można połączyć sieci wirtualnych poza region geograficznymi obwodu ExpressRoute lub większą liczbę sieci wirtualnych połączyć się z obwodu ExpressRoute włączenie dodatek usługi ExpressRoute w warstwie premium. Sprawdź [— często zadawane pytania](expressroute-faqs.md) uzyskać więcej informacji dotyczących dodatek w warstwie premium.


## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Połączyć się z obwodem sieci wirtualnej w tej samej subskrypcji
Możesz połączyć bramę sieci wirtualnej z obwodem usługi ExpressRoute, za pomocą następującego polecenia cmdlet. Upewnij się, że bramy sieci wirtualnej jest tworzona i jest gotowy do konsolidacji przed uruchomieniem polecenia cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwodu usługi ExpressRoute mogą udostępniać między wieloma subskrypcjami. Na poniższej ilustracji przedstawiono prosty przedstawienie sposobu udostępniania prac dla obwody usługi ExpressRoute między wieloma subskrypcjami.

Każdy z mniejszym chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji. Każdego z działów w organizacji można użyć własnych subskrypcji do wdrażania ich usług —, ale może udostępniać pojedynczy obwodu ExpressRoute do nawiązywania ponownego połączenia z siecią lokalną. Jednego działu (w tym przykładzie: IT) może być właścicielem obwodu usługi expressroute. Inne subskrypcje w organizacji można używać obwodu usługi expressroute.

> [!NOTE]
> Połączeniami i przepustowością opłat za obwodu ExpressRoute zostaną zastosowane do właściciela subskrypcji. Wszystkie sieci wirtualne udostępnianie tego samego przepustowości.
> 
> 

![Łącznością między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administrowanie — obwodu właścicieli i użytkowników obwodu

Właściciela obwodu jest autoryzowanym użytkownikiem zasilania zasobu obwodu usługi ExpressRoute. Właściciel obwodu można utworzyć autoryzacje, które można wykorzystać przez "obwód użytkowników". Użytkownicy obwodu są właścicielami bram sieci wirtualnej, które nie są w tej samej subskrypcji co obwodu usługi expressroute. Użytkownicy obwodu zrealizować autoryzacje (jeden autoryzacji dla sieci wirtualnej).

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływanie autoryzacje w dowolnym momencie. Odwoływanie powoduje autoryzacji wszystkich połączeń usuwany z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzacji**

Właściciel obwodu tworzy autoryzacji. Powoduje to utworzenie klucza autoryzacji, którego użytkownik obwodu można nawiązać ich bram sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacji dotyczy tylko jedno połączenie.

Poniższy fragment polecenia cmdlet przedstawia sposób tworzenia autoryzacji:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Odpowiedzi na to będzie zawierać klucz autoryzacji i stanu:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Aby przejrzeć zezwolenia**

Właściciel obwodu można przejrzeć wszystkie autoryzacje, które są wydawane w szczególności obwód, uruchamiając następujące polecenie cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby dodać zezwolenia**

Właściciel obwodu można dodać autoryzacje za pomocą następującego polecenia cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby usunąć zezwolenia**

Właściciel obwodu można odwołać/usuwania zezwolenia użytkownikowi, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operacje użytkownik obwodu

Użytkownik obwodu wymaga elementu równorzędnego identyfikator i klucz autoryzacji od właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

Identyfikator elementu równorzędnego można sprawdzić następujące polecenia:

```powershell
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzacji połączenia**

Użytkownik obwodu można uruchomić następujące polecenie cmdlet, aby zrealizować autoryzacji łącza:

```powershell
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzacji połączenia**

Można zwolnić autoryzacji, usuwając połączenia prowadzący obwodu ExpressRoute do sieci wirtualnej.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia z siecią wirtualną
Można zaktualizować niektóre właściwości połączenia z siecią wirtualną. 

**Aby zaktualizować wagi połączenia**

Sieci wirtualne można podłączyć do wielu obwody usługi ExpressRoute. Może pojawić się tego samego prefiksu z więcej niż jeden obwód usługi ExpressRoute. Aby wybrać które połączenie do wysłania ruch kierowany do tego prefiksu, można zmienić *RoutingWeight* połączenia. Ruch będzie wysyłany w połączeniu z najwyższą *RoutingWeight*.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Zakres *RoutingWeight* 0 do 32 000. Wartość domyślna to 0.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
