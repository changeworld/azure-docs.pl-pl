---
title: 'Konfigurowanie routingu (komunikacji równorzędnej) dla usługi ExpressRoute circuit: Resource Manager: programu PowerShell: Azure | Dokumentacja firmy Microsoft'
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
ms.date: 10/23/2018
ms.author: osamaz, jaredr80
ms.openlocfilehash: dc67f4a4e2189a63cfd4adbb5c1b7eace23acad5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957534"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Utworzyć i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute za pomocą programu PowerShell

Ten artykuł ułatwia tworzenie i zarządzanie nimi konfiguracji routingu dla obwodu usługi ExpressRoute w modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell. Możesz również sprawdzić stan, update lub delete i anulowanie aprowizacji komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z Twojego obwodu, wybierz artykułu z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Wideo — prywatna komunikacja równorzędna](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — publicznej komunikacji równorzędnej](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Wideo — komunikacja równorzędna firmy Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Konieczne będzie najnowszą wersję poleceń cmdlet programu Azure PowerShell Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). 
* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony na tym, aby można było uruchamiać polecenia cmdlet w tym artykule.

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje zarządzane warstwy 3 usługi (zwykle IPVPN, np. MPLS), dostawca połączenia będzie Konfigurowanie routingu oraz zarządzanie nim za Ciebie.

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Skontaktuj się z dostawcą usług, przed skonfigurowaniem komunikacji równorzędnej BGP.
> 
> 

Można skonfigurować jedną komunikację równorzędną, dwie lub trzy (prywatną Azure, publiczną Azure i Microsoft) dla obwodu usługi ExpressRoute. Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji na temat routingu domen i komunikacji równorzędnej, zobacz [domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizacji i usuwania konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu. Aby uzyskać więcej informacji, zobacz [skonfigurować filtr tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

  Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](http://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Zaimportuj wszystkie moduły AzureRM.* w ramach znanego semantycznego zakresu wersji.

  ```powershell
  Import-AzureRM
  ```

  Możesz też po prostu zaimportować wybrany moduł w ramach znanego semantycznego zakresu wersji.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Zaloguj się do swojego konta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Utwórz obwód usługi ExpressRoute.

  Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. f dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić firmie Microsoft komunikacji równorzędnej. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków. 

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, jest aprowizowana i włączona. Skorzystaj z następującego przykładu:

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

  * / 30 lub/126 podsieć dla linku podstawowego. Musi to być prawidłowy publiczny IPv4 lub IPv6 prefiks właścicielem, zarejestrowany w RIR / IRR.
  * / 30 lub/126 podsieć dla linku dodatkowego. Musi to być prawidłowy publiczny IPv4 lub IPv6 prefiks właścicielem, zarejestrowany w RIR / IRR.
  * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
  * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
  * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę rozdzielonych przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR. Sesje protokołu BGP IPv4 wymagają IPv4 anonsowane prefiksy i sesje protokołu BGP IPv6 wymaga, że IPv6 anonsowane prefiksy. 
  * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
  * Opcjonalnie:
    * Numer ASN klienta: jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane.
    * Skrót MD5, jeśli zdecydujesz się go użyć.

  Aby skonfigurować komunikację równorzędną Microsoft dla obwodu, skorzystaj z następującego przykładu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>Aby uzyskać szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Można uzyskać szczegółów konfiguracji, korzystając z następującego przykładu:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu:

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Prywatną komunikację równorzędną Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej konfiguracji Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

  Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](http://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Zaimportuj wszystkie moduły AzureRM.* w ramach znanego semantycznego zakresu wersji.

  ```powershell
  Import-AzureRM
  ```

  Możesz też po prostu zaimportować wybrany moduł w ramach znanego semantycznego zakresu wersji.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Zaloguj się do swojego konta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Utwórz obwód usługi ExpressRoute.

  Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić prywatną komunikację równorzędną Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków.

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, jest aprowizowana i włączona. Skorzystaj z następującego przykładu:

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

  * Podsieć /30 dla połączenia podstawowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
  * Podsieć /30 dla połączenia dodatkowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
  * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
  * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Pamiętaj, aby nie używać numeru 65515.
  * Opcjonalnie:
    * Skrót MD5, jeśli zdecydujesz się go użyć.

  Aby skonfigurować prywatną komunikację równorzędną dla obwodu, skorzystaj z następującego przykładu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Jeśli zdecydujesz się używać skrótu MD5, skorzystaj z następującego przykładu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
  > 
  >

### <a name="getprivate"></a>Aby uzyskać Azure szczegóły dotyczące komunikacji równorzędnej prywatnej

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Aby zaktualizować konfigurację komunikacji równorzędnej prywatnej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany ze 100 do 500.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

> [!WARNING]
> Należy się upewnić, czy wszystkie połączenia zasięgu globalnym usługi ExpressRoute i sieci wirtualne zostały usunięte przed uruchomieniem tego przykładu. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Publicznej komunikacji równorzędnej Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie publicznej komunikacji równorzędnej konfiguracji Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Aby utworzyć publiczną komunikację równorzędną Azure

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

  Aby zacząć używać poleceń cmdlet usługi ExpressRoute, musisz zainstalować najnowszą wersję Instalatora programu PowerShell z [galerii programu PowerShell](http://www.powershellgallery.com/) i zaimportować moduły usługi Azure Resource Manager do sesji programu PowerShell. Musisz uruchomić program PowerShell jako administrator.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Zaimportuj wszystkie moduły AzureRM.* w ramach znanego semantycznego zakresu wersji.

  ```powershell
  Import-AzureRM
  ```

  Możesz też po prostu zaimportować wybrany moduł w ramach znanego semantycznego zakresu wersji.

  ```powershell
  Import-Module AzureRM.Network
```

  Zaloguj się do swojego konta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Utwórz obwód usługi ExpressRoute.

  Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić publicznej komunikacji równorzędnej Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków.

3. Sprawdź obwód usługi ExpressRoute, aby upewnić się, jest aprowizowana i włączona. Skorzystaj z następującego przykładu:

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
4. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Upewnij się, że masz następujące informacje, zanim przejdziesz dalej.

  * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
  * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
  * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
  * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
  * Opcjonalnie:
    * Skrót MD5, jeśli zdecydujesz się go użyć.

  Z poniższego przykładu, aby skonfigurować publiczną komunikację równorzędną Azure dla obwodu

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Jeśli zdecydujesz się używać skrótu MD5, skorzystaj z następującego przykładu:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
  > 
  >

### <a name="getpublic"></a>Aby dowiedzieć się Azure publicznej komunikacji równorzędnej

Można uzyskać szczegółów konfiguracji, za pomocą następującego polecenia cmdlet:

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Aby zaktualizować konfigurację komunikacji równorzędnej publicznej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Kolejne kroki

Następny krok: [Link a VNet to an ExpressRoute circuit](expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
