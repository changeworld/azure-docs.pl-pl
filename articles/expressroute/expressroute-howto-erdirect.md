---
title: 'Azure ExpressRoute: Konfigurowanie direct usługi ExpressRoute'
description: Ta strona ułatwia konfigurowanie usługi ExpressRoute Direct.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jaredro
ms.openlocfilehash: 2722a852b1119ef619bc414bce5cb3a8ff6f8f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031616"
---
# <a name="how-to-configure-expressroute-direct"></a>Jak skonfigurować program ExpressRoute Direct

Usługa ExpressRoute Direct umożliwia bezpośrednie łączenie się bezpośrednio z globalną siecią firmy Microsoft w lokalizacjach komunikacji równorzędnej strategicznie rozproszonych na całym świecie. Aby uzyskać więcej informacji, zobacz [About ExpressRoute Direct (Usługa ExpressRoute Direct)](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Tworzenie zasobu

1. Zaloguj się na platformie Azure i wybierz subskrypcję. Zasób Direct usługi ExpressRoute i obwody usługi ExpressRoute muszą być w tej samej subskrypcji.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Ponownie zarejestruj subskrypcję w witrynie Microsoft.Network, aby uzyskać dostęp do interfejsów API expressrouteportslocation i expressrouteport.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Wyświetl listę wszystkich lokalizacji, w których jest obsługiwana usługa Direct usługi ExpressRoute Direct.
  
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
4. Określ, czy lokalizacja wymieniona powyżej ma dostępną przepustowość

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
5. Tworzenie zasobu Direct usługi ExpressRoute na podstawie lokalizacji wybranej powyżej

   Program ExpressRoute Direct obsługuje hermetyzację QinQ i Dot1Q. Jeśli wybrano qinQ, każdy obwód usługi ExpressRoute będzie dynamicznie przypisywany znacznikiem S i będzie unikatowy w całym zasobie Direct usługi ExpressRoute. Każdy znacznik C w obwodzie musi być unikatowy w obwodzie, ale nie w poprzek expressroute direct.  

   Jeśli wybrano hermetyzację Dot1Q, należy zarządzać unikatowością znacznika C (VLAN) w całym zasobie Direct usługi ExpressRoute.  

   > [!IMPORTANT]
   > Program ExpressRoute Direct może być tylko jednym typem hermetyzacji. Hermetyzacji nie można zmienić po utworzeniu direct usługi ExpressRoute.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Atrybut Encapsulation można również ustawić na Dot1Q. 
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

## <a name="change-admin-state-of-links"></a><a name="state"></a>Zmienianie stanu administracyjnego łączy

  Ten proces powinien być używany do przeprowadzenia testu warstwy 1, upewniając się, że każde połączenie krzyżowe jest prawidłowo załatane do każdego routera dla podstawowego i pomocniczego.
1. Pobierz szczegóły usługi ExpressRoute Direct.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Ustaw łącze na Włączone. Powtórz ten krok, aby ustawić każde łącze na włączone.

   Links[0] jest portem podstawowym, a Links[1] jest portem pomocniczym.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
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

   Użyj tej samej procedury, `AdminState = "Disabled"` aby wyłączyć porty.

## <a name="create-a-circuit"></a><a name="circuit"></a>Tworzenie obwodu

Domyślnie można utworzyć 10 obwodów w ramach subskrypcji, w której znajduje się zasób Direct usługi ExpressRoute. Można to zwiększyć dzięki wsparciu. Użytkownik jest odpowiedzialny za śledzenie zarówno aprowizowana, jak i wykorzystana przepustowość. Przepustowość aprowizacji jest sumą przepustowości wszystkich obwodów w zasobie Direct usługi ExpressRoute, a wykorzystana przepustowość jest fizycznym wykorzystaniem podstawowych interfejsów fizycznych.

Istnieją dodatkowe przepustowości obwodów, które mogą być używane w uzywu usługi ExpressRoute Direct tylko do obsługi scenariuszy opisanych powyżej. Są to: 40Gbps i 100Gbps.

**SkuTier** może być lokalny, standardowy lub premium.

**SkuFamily** musi być MeteredData tylko jako nieograniczony nie jest obsługiwany w użyczarka expressroute direct.

Tworzenie obwodu w zasobie Direct usługi ExpressRoute.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Inne przepustowości to: 5.0, 10.0 i 40.0

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute Direct, zobacz [Omówienie](expressroute-erdirect-about.md).
