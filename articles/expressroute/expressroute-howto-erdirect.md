---
title: Konfigurowanie usługi ExpressRoute bezpośrednio — Azure | Dokumentacja firmy Microsoft
description: Ta strona służy do konfigurowania bezpośrednich usługi ExpressRoute.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 0fec7234d18659051c61fda593b1ba0fb846c220
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964261"
---
# <a name="how-to-configure-expressroute-direct"></a>Jak skonfigurować bezpośrednio z usługi ExpressRoute

Bezpośrednio z usługi ExpressRoute zapewnia możliwość łączenia bezpośrednio do globalnej sieci firmy Microsoft w lokalizacji komunikacji równorzędnej, strategicznie rozproszonych na całym świecie. Aby uzyskać więcej informacji, zobacz [dotyczące usługi ExpressRoute bezpośrednie łączenie](expressroute-erdirect-about.md).

## <a name="resources"></a>Utwórz zasób

1. Logowanie do platformy Azure i wybierz subskrypcję. Zasób bezpośrednio z usługi ExpressRoute i obwodów usługi ExpressRoute musi być w tej samej subskrypcji.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription “<SubscriptionID or SubscriptionName>”
   ```
2. Wyświetl listę wszystkich lokalizacji, w którym są obsługiwane bezpośrednio z usługi ExpressRoute.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Przykładowe dane wyjściowe**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
3. Określa, czy lokalizacja z wymienionych powyżej ma dostępną przepustowość

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Przykładowe dane wyjściowe**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
4. Tworzenie zasobu usługi ExpressRoute bezpośrednio na podstawie lokalizacji wybrano tak powyżej

   Usługa ExpressRoute bezpośrednio obsługuje QinQ i Dot1Q hermetyzacji. Jeśli wybrano QinQ, każdy obwód usługi ExpressRoute zostanie dynamicznie przypisany S Tag i będą unikatowe w obrębie całej zasobów bezpośrednio z usługi ExpressRoute. Każdy Tag języka C, w ramach obwodu muszą być unikatowe w ramach obwodu, ale nie między bezpośrednio usługi ExpressRoute.  

   Jeśli Dot1Q hermetyzacji jest zaznaczone, można zarządzać unikatowości tagu języka C (VLAN), w całej zasobów bezpośrednio z usługi ExpressRoute.  

   > [!IMPORTANT]
   > Bezpośrednie usługi ExpressRoute może być tylko jeden typ hermetyzacji. Hermetyzacja nie można zmienić po utworzeniu bezpośrednio z usługi ExpressRoute.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Można również ustawić atrybut hermetyzacji do Dot1Q. 
   >

   **Przykładowe dane wyjściowe:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="state"></a>Zmień stan administrator łączy

  Ten proces powinien służyć do przeprowadzenia testu warstwy 1, zapewnia, że każdy obejmującej wiele połączeń jest prawidłowo poprawionego do każdego routera dla podstawowego i pomocniczego.
1. Szczegółowe informacje bezpośrednio z usługi ExpressRoute.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Włącz łącze. Powtórz ten krok, aby ustawić każdy link włączony.

   Łącza [0] jest podstawowy port i łącza [1] jest dodatkowych portów.

   ```powershell
   $ERDirect.Links[0].AdminState = “Enabled”
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = “Enabled”
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Przykładowe dane wyjściowe:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   Użyj tej samej procedury z `AdminState = “Disabled”` włączyć porty w dół.

## <a name="circuit"></a>Tworzenie obwodu

Domyślnie można utworzyć 10 obwodów w ramach subskrypcji, gdzie jest zasobów bezpośrednio z usługi ExpressRoute. Może to być zwiększana przez dział pomocy technicznej. Odpowiedzialność za śledzenia Aprowizowana wykorzystania przepustowości. Elastycznie przepustowość jest suma przepustowości wszystkich obwodów zasobu bezpośrednio z usługi ExpressRoute i wykorzystywanych przepustowość jest użycie fizycznego bazowego interfejsy fizyczne.

Brak przepustowości obwodu dodatkowe, które mogą zostać użyte na bezpośrednio ExpressRoute tylko do obsługi scenariuszy opisanych powyżej. Są to: 40Gbps i 100Gbps.

**SkuTier** może być lokalny, standardowa lub Premium.

**SkuFamily** musi być MeteredData tylko jako nieograniczona liczba nie jest obsługiwana na bezpośrednio z usługi ExpressRoute.

Utwórz obwód zasobu bezpośrednio z usługi ExpressRoute.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Inne przepustowości obejmują: 5.0, 10.0 i 40.0

  **Przykładowe dane wyjściowe:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute bezpośrednio zobacz [Przegląd](expressroute-erdirect-about.md).
