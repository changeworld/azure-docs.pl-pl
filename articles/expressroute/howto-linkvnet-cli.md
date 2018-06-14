---
title: 'Połączyć sieć wirtualną z obwodem usługi ExpressRoute: interfejs wiersza polecenia: Azure | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualne) do obwody usługi ExpressRoute, przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: anzaman,cherylmc
ms.openlocfilehash: 5e8d1739aa3d7f5be6c6450edcad43bc83db71fb
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875406"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Połączenie wirtualnej sieci do obwodu usługi ExpressRoute, przy użyciu interfejsu wiersza polecenia

Ten artykuł pomaga połączyć sieci wirtualnych (sieci wirtualne) do usługi Azure ExpressRoute obwodów przy użyciu interfejsu wiersza polecenia. Aby połączyć za pomocą interfejsu wiersza polecenia Azure, sieci wirtualnych muszą być tworzone przy użyciu modelu wdrażania Menedżera zasobów. Albo mogą być w tej samej subskrypcji lub częścią innej subskrypcji. Jeśli chcesz użyć innej metody, aby połączyć sieci wirtualnej z obwodem usługi ExpressRoute, możesz wybrać artykułu z poniższej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Należy najnowszą wersję interfejsu wiersza polecenia (CLI). Aby uzyskać więcej informacji, zobacz [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Należy przejrzeć [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwodu usługi ExpressRoute](howto-circuit-cli.md) i mieć obwodu włączane przez dostawcą połączenia. 
  * Upewnij się, że masz prywatnej komunikacji równorzędnej platformy Azure skonfigurowane dla obwodu. Zobacz [Konfigurowanie routingu](howto-routing-cli.md) artykułu instrukcje routingu. 
  * Upewnij się, że skonfigurowano Azure prywatnej komunikacji równorzędnej. BGP komunikacji równorzędnej między siecią a Microsoft muszą być uruchomione, dzięki czemu można włączyć łączność end-to-end.
  * Upewnij się, że masz sieci wirtualnej i Brama sieci wirtualnej utworzone i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [należy skonfigurować bramę sieci wirtualnej dla usługi ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Należy użyć `--gateway-type ExpressRoute`.

* Standardowa obwodu ExpressRoute można połączyć maksymalnie 10 sieciami wirtualnymi. Wszystkie wirtualne sieci musi być w tym samym regionie geograficznymi, gdy przy użyciu standardowych obwodu usługi expressroute. 

* Pojedynczy sieci wirtualnej może odnosić się do maksymalnie cztery obwody usługi ExpressRoute. Umożliwia utworzenie nowego obiektu połączenia dla każdego obwodu ExpressRoute, który jest nawiązywane z procesem opisanym niżej. Obwody usługi ExpressRoute, można w tej samej subskrypcji, różnych subskrypcji lub kombinację obu.

* Należy włączyć dodatek premium usługi ExpressRoute, można połączyć sieć wirtualną poza region geograficznymi obwodu ExpressRoute lub połączyć większą liczbę sieci wirtualnych do obwodu usługi ExpressRoute. Aby uzyskać więcej informacji na temat dodatek premium, zobacz [— często zadawane pytania](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Połączyć się z obwodem sieci wirtualnej w tej samej subskrypcji

Można połączyć z obwodem usługi ExpressRoute bramę sieci wirtualnej przy użyciu w przykładzie. Upewnij się, że bramy sieci wirtualnej jest tworzona i jest gotowy do konsolidacji przed uruchomieniem polecenia.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem

Obwodu usługi ExpressRoute mogą udostępniać między wieloma subskrypcjami. Na poniższym rysunku przedstawiono prostą przedstawienie sposobu udostępniania prac dla obwody usługi ExpressRoute między wieloma subskrypcjami.

Każdy z mniejszym chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji. Każdego z działów w organizacji można użyć własnych subskrypcji do wdrażania ich usług —, ale może udostępniać pojedynczy obwodu ExpressRoute do nawiązywania ponownego połączenia z siecią lokalną. Jednego działu (w tym przykładzie: IT) może być właścicielem obwodu usługi expressroute. Inne subskrypcje w organizacji można używać obwodu usługi expressroute.

> [!NOTE]
> Połączeniami i przepustowością opłat za obwód dedykowany zostaną zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne udostępnianie tego samego przepustowości.
> 
> 

![Łącznością między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administrowanie — obwodu właścicieli i użytkowników obwodu

Właściciela obwodu jest autoryzowanym użytkownikiem zasilania zasobu obwodu usługi ExpressRoute. Właściciel obwodu można utworzyć autoryzacje, które można wykorzystać przez "Obwód użytkowników". Użytkownicy obwodu są właścicielami bram sieci wirtualnej, które nie są w tej samej subskrypcji co obwodu usługi expressroute. Użytkownicy obwodu zrealizować autoryzacje (jeden autoryzacji dla sieci wirtualnej).

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływanie autoryzacje w dowolnym momencie. Po odebraniu autoryzacji wszystkich połączeń są usuwane z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzacji**

Właściciel obwodu tworzy autoryzacji, co powoduje autoryzacji klucza, którego użytkownik obwodu można nawiązać ich bram sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacji dotyczy tylko jedno połączenie.

Poniższy przykład przedstawia sposób tworzenia autoryzacji:

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

**Aby przejrzeć zezwolenia**

Właściciel obwodu można przejrzeć wszystkie autoryzacje, które są wydawane w szczególności obwód, uruchamiając w poniższym przykładzie:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Aby dodać zezwolenia**

Właściciel obwodu można dodać autoryzacje przy użyciu w poniższym przykładzie:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Aby usunąć zezwolenia**

Właściciel obwodu można odwołać/usuwania zezwolenia użytkownikowi, uruchamiając w poniższym przykładzie:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Obwód operacji użytkownika

Użytkownik obwodu wymaga elementu równorzędnego identyfikator i klucz autoryzacji od właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzacji połączenia**

Użytkownik obwodu można uruchomić następującego przykładu, aby zrealizować autoryzacji łącza:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzacji połączenia**

Można zwolnić autoryzacji, usuwając połączenia prowadzący obwodu ExpressRoute do sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).