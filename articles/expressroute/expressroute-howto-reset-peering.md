---
title: 'Azure ExpressRoute: Resetowanie komunikacji równorzędnej obwodów'
description: Jak wyłączyć i włączyć komunikację równorzędności obwodów usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941733"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetowanie komunikacji równorzędnej obwodów usługi ExpressRoute

W tym artykule opisano sposób wyłączania i włączania komunikacji równorzędnej obwodu usługi ExpressRoute przy użyciu programu PowerShell. Po wyłączeniu komunikacji równorzędnej sesja BGP zarówno na połączeniu podstawowym, jak i pomocniczym obwodu usługi ExpressRoute zostanie zamknięta. Utracisz łączność za pośrednictwem tej komunikacji równorzędnej z firmą Microsoft. Po włączeniu komunikacji równorzędnej sesja BGP zarówno na połączeniu podstawowym, jak i pomocniczym obwodu usługi ExpressRoute zostanie doprowadzona. Odzyskasz łączność dzięki tej komunikacji równorzędnej z firmą Microsoft. Można włączyć i wyłączyć komunikację równorzędną firmy Microsoft i prywatną komunikację równorzędną platformy Azure niezależnie w obwodzie usługi ExpressRoute. Podczas pierwszej konfiguracji komunikacji równorzędnej w obwodzie usługi ExpressRoute, komunikacji równorzędnej są domyślnie włączone.

Istnieje kilka scenariuszy, w których może okazać się pomocne resetowanie komunikacji równorzędnej usługi ExpressRoute.
* Przetestuj projekt i implementację odzyskiwania po awarii. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikacji równorzędnej jednego obwodu i wymusić ruch sieciowy do pracy awaryjnej do drugiego obwodu.
* Włącz dwukierunkowe wykrywanie przekazywania dalej (BFD) w prywatnej komunikacji równorzędnej platformy Azure lub komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute. BFD jest domyślnie włączona w prywatnej komunikacji równorzędnej platformy Azure, jeśli obwód usługi ExpressRoute zostanie utworzony po 1 sierpnia 2018 r. i w programie Microsoft Peering, jeśli obwód usługi ExpressRoute zostanie utworzony po 10 stycznia 2020 r. Jeśli obwód został utworzony wcześniej, BFD nie został włączony. BfD można włączyć, wyłączając komunikację równorzędną i ponownie ją tworząc. 

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Resetowanie komunikacji równorzędnej

1. Jeśli używasz programu PowerShell lokalnie, otwórz konsolę programu PowerShell z podwyższonymi uprawnieniami i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Wskaż subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Uruchom następujące polecenia, aby pobrać obwód usługi ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Zidentyfikuj komunikację równorzędnej, którą chcesz wyłączyć lub włączyć. *Komunikacja równorzędna* jest tablicą. W poniższym przykładzie komunikacja równorzędna[0] to prywatna komunikacja równorzędna i komunikacja równorzędna platformy Azure[1] microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Uruchom następujące polecenia, aby zmienić stan komunikacji równorzędnej.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Komunikacja równorzędna powinna być ustawiona w stanie. 

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz pomocy w rozwiązaniu problemu z usługi ExpressRoute, zapoznaj się z następującymi artykułami:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
