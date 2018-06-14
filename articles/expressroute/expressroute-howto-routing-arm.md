---
title: 'Jak skonfigurować obwód (równorzędna) dla usługi routingu: Menedżer zasobów: środowiska PowerShell: Azure | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera instrukcje tworzenia i inicjowania obsługi komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
documentationcenter: na
services: expressroute
author: osamazia
manager: jonor
editor: ''
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/3/2018
ms.author: osamaz, jaredr80
ms.openlocfilehash: d4f8f0e6c8fab5dfb4efcc4f1659ba90336c8bf0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593886"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute, przy użyciu programu PowerShell

W tym artykule opisano tworzenie i zarządzanie nimi konfiguracji routingu dla obwodu usługi ExpressRoute w modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell. Można również sprawdzić stan, update lub delete i anulowanie zastrzeżenia komunikacji równorzędnych dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodu, wybierz artykułu z poniższej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Video - prywatnej komunikacji równorzędnej](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - publicznej komunikacji równorzędnej](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - komunikacji równorzędnej firmy Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Konieczne będzie najnowszą wersję poleceń cmdlet programu PowerShell Menedżera zasobów Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). 
* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi expressroute musi być w stanie zainicjowane i włączone dla możesz mieć możliwość uruchamiania poleceń cmdlet w tym artykule.

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz usługodawcy, który oferuje zarządzanych warstwy 3 usługi (zazwyczaj IPVPN, takich jak MPLS), dostawca połączenia będzie Konfigurowanie i zarządzanie nimi routingu dla Ciebie.

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Skontaktuj się z dostawcą usług przed rozpoczęciem konfigurowania komunikacji równorzędnych protokołu BGP.
> 
> 

Można skonfigurować jedną komunikację równorzędną, dwie lub trzy (prywatną Azure, publiczną Azure i Microsoft) dla obwodu usługi ExpressRoute. Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o routingu domeny i komunikacji równorzędnych, zobacz [domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Komunikacji równorzędnej firmy Microsoft

Ta sekcja pomoże Ci tworzenie, get, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Z obwody usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft ma wszystkie prefiksy usługi anonsowane przez firmę Microsoft, zaglądanie, nawet jeśli nie zdefiniowano filtrów trasy. Z obwody usługi ExpressRoute, które są skonfigurowane na lub po 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft nie będzie miał wszystkie prefiksy anonsowane do momentu filtr tras jest dołączony do obwodu. Aby uzyskać więcej informacji, zobacz [skonfigurować filtr trasy dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

  Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](http://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Importuj wszystkie moduły AzureRM.* zakresu znanych wersją semantyczną.

  ```powershell
  Import-AzureRM
  ```

  Można także po prostu zaimportować moduł wybierz zakresu znanych wersją semantyczną.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Zaloguj się do swojego konta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Wybierz subskrypcję, aby utworzyć obwodu usługi expressroute.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Utwórz obwód usługi ExpressRoute.

  Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. f zarządzanych usług warstwy 3 oferuje dostawcą połączenia, skontaktuj się dostawcą połączenia, aby włączyć Microsoft komunikacji równorzędnej dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych krokach. 

3. Sprawdź obwodu ExpressRoute, aby upewnić się, jest udostępniane i włączona. Skorzystaj z następującego przykładu:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

  * / 30 lub /126 podsieci dla linku podstawowego. Musi to być prawidłowy publiczny IPv4 lub IPv6 prefiks należące do Ciebie i zarejestrowane w rejestrze RIR / IRR.
  * / 30 lub /126 podsieci dla linku dodatkowego. Musi to być prawidłowy publiczny IPv4 lub IPv6 prefiks należące do Ciebie i zarejestrowane w rejestrze RIR / IRR.
  * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
  * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
  * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłać zestaw prefiksy, możesz wysłać rozdzielana przecinkami lista. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR. Sesje IPv4 BGP wymagają IPv4 anonsowany prefiksy i sesje IPv6 BGP wymagają IPv6 anonsowane prefiksy. 
  * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
  * Opcjonalnie:
    * Numer ASN klienta: jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane.
    * Skrót MD5, jeśli zdecydujesz się go użyć.

  Skorzystaj z następującego przykładu, aby skonfigurować Microsoft komunikacji równorzędnej dla obwodu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>Aby uzyskać szczegółowe informacje komunikacji równorzędnej firmy Microsoft

Można pobrać szczegółów konfiguracji, jak w poniższym przykładzie:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Można aktualizować dowolną część konfiguracji, jak w poniższym przykładzie:

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Aby usunąć komunikacji równorzędnej firmy Microsoft

Można usunąć konfiguracji komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Azure prywatnej komunikacji równorzędnej

Ta sekcja pomoże Ci tworzenie, get, aktualizowanie i usuwanie konfiguracji platformy Azure prywatnej komunikacji równorzędnej dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

  Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](http://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importuj wszystkie moduły AzureRM.* zakresu znanych wersją semantyczną.

  ```powershell
  Import-AzureRM
  ```

  Można także po prostu zaimportować moduł wybierz zakresu znanych wersją semantyczną.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Zaloguj się do swojego konta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Wybierz subskrypcję, aby utworzyć obwodu usługi expressroute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Utwórz obwód usługi ExpressRoute.

  Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia udostępnia usługi warstwy 3 zarządzane, możesz poprosić dostawcą połączenia, aby włączyć prywatnej komunikacji równorzędnej platformy Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych krokach.

3. Sprawdź obwodu ExpressRoute, aby upewnić się, jest udostępniane i włączona. Skorzystaj z następującego przykładu:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

  * Podsieć /30 dla połączenia podstawowego. Podsieci nie może być częścią żadnych przestrzeni adresowej zarezerwowane dla sieci wirtualnych.
  * Podsieć /30 dla połączenia dodatkowego. Podsieci nie może być częścią żadnych przestrzeni adresowej zarezerwowane dla sieci wirtualnych.
  * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
  * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Pamiętaj, aby nie używać numeru 65515.
  * Opcjonalnie:
    * Skrót MD5, jeśli zdecydujesz się go użyć.

  Skorzystaj z następującego przykładu, aby skonfigurować prywatnej komunikacji równorzędnej platformy Azure dla obwodu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Jeśli chcesz użyć skrótu MD5, skorzystaj z następującego przykładu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
  > 
  >

### <a name="getprivate"></a>Aby dowiedzieć się Azure prywatnej komunikacji równorzędnej

Szczegóły konfiguracji można uzyskać za pomocą w poniższym przykładzie:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Aby zaktualizować konfiguracji komunikacji równorzędnej prywatnej platformy Azure

Można aktualizować dowolną część konfiguracji, jak w poniższym przykładzie. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowana od 100 do 500.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Aby usunąć Azure prywatnej komunikacji równorzędnej

Można usunąć konfiguracji komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

> [!WARNING]
> Należy się upewnić, że wszystkie sieci wirtualne są odłączone od obwodu ExpressRoute przed uruchomieniem w tym przykładzie. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Azure publicznej komunikacji równorzędnej

Ta sekcja pomoże Ci tworzenie, get, aktualizowanie i usuwanie konfiguracji platformy Azure publicznej komunikacji równorzędnej dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Aby utworzyć publiczną komunikację równorzędną Azure

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

  Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](http://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Importuj wszystkie moduły AzureRM.* zakresu znanych wersją semantyczną.

  ```powershell
  Import-AzureRM
  ```

  Można także po prostu zaimportować moduł wybierz zakresu znanych wersją semantyczną.

  ```powershell
  Import-Module AzureRM.Network
```

  Zaloguj się do swojego konta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Wybierz subskrypcję, aby utworzyć obwodu usługi expressroute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Utwórz obwód usługi ExpressRoute.

  Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia udostępnia usługi warstwy 3 zarządzane, możesz poprosić dostawcą połączenia, aby włączyć publicznej komunikacji równorzędnej platformy Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych krokach.

3. Sprawdź obwodu ExpressRoute, aby upewnić się, jest udostępniane i włączona. Skorzystaj z następującego przykładu:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
4. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Upewnij się, że masz następujące informacje przed przejściem dalej.

  * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
  * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
  * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
  * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
  * Opcjonalnie:
    * Skrót MD5, jeśli zdecydujesz się go użyć.

  Uruchom poniższy przykład, aby skonfigurować publicznej komunikacji równorzędnej platformy Azure dla obwodu

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Jeśli chcesz użyć skrótu MD5, skorzystaj z następującego przykładu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
  > 
  >

### <a name="getpublic"></a>Aby dowiedzieć się Azure publicznej komunikacji równorzędnej

Można pobrać szczegółów konfiguracji przy użyciu następującego polecenia cmdlet:

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Aby zaktualizować konfiguracji komunikacji równorzędnej publicznej platformy Azure

Można aktualizować dowolną część konfiguracji, jak w poniższym przykładzie. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowana od 200 do 600.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Aby usunąć Azure publicznej komunikacji równorzędnej

Można usunąć konfiguracji komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Kolejne kroki

Następny krok: [Link a VNet to an ExpressRoute circuit](expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
