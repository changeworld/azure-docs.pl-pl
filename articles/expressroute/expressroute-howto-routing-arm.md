---
title: 'Usługa Azure ExpressRoute: konfigurowanie komunikacji równorzędnej: program PowerShell'
description: Ten artykuł zawiera instrukcje tworzenia i inicjowania obsługi komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 2c28df35eec862afb5b0078ca7693898e9b58533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264845"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute przy użyciu programu PowerShell

Ten artykuł ułatwia tworzenie konfiguracji routingu dla obwodu usługi ExpressRoute w modelu wdrażania Menedżera zasobów przy użyciu programu PowerShell i zarządzanie nią. Można również sprawdzić stan, zaktualizować lub usunąć i anulować komunikację równorzędną dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Komunikacja równorzędna](about-public-peering.md)
> * [Wideo - Prywatne peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 


Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli korzystasz z dostawcy usług, który oferuje zarządzane usługi warstwy 3 (zazwyczaj ipvpn, takie jak MPLS), dostawca łączności skonfiguruje routing i zarządza nim.

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Przed skonfigurowaniem komunikacji równorzędnej protokołu BGP skontaktuj się z usługodawcą.
> 
> 

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczne równorzędne komunikacji równorzędnej platformy Azure jest przestarzałe dla nowych obwodów). Komunikacji równorzędnej można skonfigurować w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji na temat routingu domen i komunikacji równorzędnej, zobacz [Domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat komunikacji równorzędnej publicznej, zobacz [Komunikacja równorzędna usługi ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie aprowidyzowania i włączone, aby można było uruchomić polecenia cmdlet w tym artykule.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, ujmowanie, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana przed 1 sierpnia 2017 r. będzie miała wszystkie prefiksy usługi anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana 1 sierpnia 2017 r. lub później nie będzie miała żadnych prefiksów anonsowanych, dopóki filtr trasy nie zostanie dołączony do obwodu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru marszruty dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Zaloguj się i wybierz subskrypcję.

   Jeśli program PowerShell został zainstalowany lokalnie, zaloguj się. Jeśli używasz usługi Azure Cloud Shell, możesz pominąć ten krok.

   ```azurepowershell
   Connect-AzAccount
   ```

   Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute.

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. f dostawca łączności oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcę łączności o włączenie komunikacji równorzędnej firmy Microsoft. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza routingiem, po utworzeniu obwodu kontynuuj konfigurację, wykonując kolejne kroki. 

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, że jest aprowizować, a także włączone. Skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 lub /126 dla łącza podstawowego. Musi to być prawidłowy publiczny prefiks IPv4 lub IPv6 należący do Ciebie i zarejestrowany w RIR / IRR.
   * Podsieć /30 lub /126 dla łącza pomocniczego. Musi to być prawidłowy publiczny prefiks IPv4 lub IPv6 należący do Ciebie i zarejestrowany w RIR / IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłać zestaw prefiksów, możesz wysłać listę oddzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR. Sesje protokołu IPv4 BGP wymagają prefiksów anonsowanych iPv4, a sesje protokołu IPv6 BGP wymagają anonsowanych prefiksów protokołu IPv6. 
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * Opcjonalnie:
     * Numer ASN klienta: jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane.
     * Skrót MD5, jeśli zdecydujesz się go użyć.

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "Anonsowane prefiksy publiczne" i "Peer ASN" (lub "Asn klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz prefiksy publiczne z innej jednostki i jeśli przypisanie nie jest rejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie zakończone i będzie wymagać ręcznego sprawdzania poprawności. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zobaczysz "AnonsowanePublicPrefixesState" jako "Sprawdzanie poprawności potrzebne" na wyjściu "Get-AzExpressRouteCircuitPeeringConfig" (zobacz "Aby uzyskać szczegóły komunikacji równorzędnej firmy Microsoft" poniżej) polecenia. 
> 
> Jeśli zostanie wyświetlony komunikat "Sprawdzanie poprawności potrzebne", zebrać dokumenty, które pokazują prefiksy publiczne są przypisane do organizacji przez jednostkę, która jest wymieniona jako właściciel prefiksów w rejestrze routingu i przesłać te dokumenty do ręcznego sprawdzania poprawności przez otwierając bilet wsparcia, jak pokazano poniżej. 
> 
>

   Użyj następującego przykładu, aby skonfigurować komunikację równorzędnych firmy Microsoft dla obwodu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Aby pobrać szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Dowolną część konfiguracji można zaktualizować, korzystając z następującego przykładu:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Prywatna komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, ujmowanie, aktualizowanie i usuwanie prywatnej konfiguracji komunikacji równorzędnej platformy Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

   Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](https://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Zaimportuj wszystkie\* moduły Az. w ramach znanego zakresu wersji semantycznych.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Można również po prostu zaimportować wybrany moduł w ramach znanego zakresu wersji semantycznych.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Zaloguj się na swoje konto.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute.

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca łączności oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcę łączności o włączenie prywatnej komunikacji równorzędnej platformy Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza routingiem, po utworzeniu obwodu kontynuuj konfigurację, wykonując kolejne kroki.

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, że jest aprowizować, a także włączone. Skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Pamiętaj, aby nie używać numeru 65515.
   * Opcjonalnie:
     * Skrót MD5, jeśli zdecydujesz się go użyć.

   Użyj następującego przykładu, aby skonfigurować prywatną komunikację równorzędnych platformy Azure dla obwodu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Jeśli zdecydujesz się użyć skrótu MD5, użyj następującego przykładu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Aby uzyskać szczegółowe informacje dotyczące prywatnej komunikacji równorzędnej platformy Azure

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Dowolną część konfiguracji można zaktualizować, korzystając z poniższego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany od 100 do 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając następujący przykład:

> [!WARNING]
> Przed uruchomieniem tego przykładu należy upewnić się, że wszystkie sieci wirtualne i połączenia usługi ExpressRoute Global Reach zostaną usunięte. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


## <a name="next-steps"></a>Następne kroki

Następny krok: [Link a VNet to an ExpressRoute circuit](expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
