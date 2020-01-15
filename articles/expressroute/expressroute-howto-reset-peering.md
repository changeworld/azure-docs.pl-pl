---
title: 'Azure ExpressRoute: Resetowanie komunikacji równorzędnej obwodu'
description: Jak wyłączyć lub włączyć komunikacja równorzędna obwodu usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941733"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetuj komunikacja równorzędna obwodu usługi ExpressRoute

W tym artykule opisano, jak wyłączyć lub włączyć komunikację równorzędną dla obwodu usługi ExpressRoute za pomocą programu PowerShell. Po wyłączeniu komunikację równorzędną, sesji protokołu BGP, zarówno połączenia podstawowego, jak i połączenia pomocniczego obwodu usługi ExpressRoute zostanie zamknięty. Użytkownik utraci łączność za pośrednictwem komunikacji równorzędnej do firmy Microsoft. Po włączeniu komunikacji równorzędnej sesji protokołu BGP, zarówno połączenia podstawowego, jak i połączenia pomocniczego obwodu usługi ExpressRoute zostanie wznowione. Odzyska łączność za pośrednictwem komunikacji równorzędnej do firmy Microsoft. Można włączać i wyłączać Peering firmy Microsoft i Azure prywatnej komunikacji równorzędnej na obwód usługi ExpressRoute w niezależnie. Podczas pierwszej konfiguracji komunikacji równorzędnej na obwód usługi ExpressRoute, komunikację równorzędną są domyślnie włączone.

Istnieje kilka scenariuszy, gdzie może okazać się przydatne, resetowanie komunikacji równorzędnej usługi ExpressRoute.
* Testowanie projektu odzyskiwania po awarii i implementacji. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikacje równorzędne jednym obwodu i wymusić ruchu sieciowego do trybu failover z obwodem usługi.
* Włącz wykrywanie dwukierunkowego przekazywania (BFD) w prywatnej komunikacji równorzędnej Azure lub komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute. BFD jest domyślnie włączona w prywatnej komunikacji równorzędnej Azure, jeśli obwód usługi ExpressRoute został utworzony po 1 2018 sierpnia i w przypadku komunikacji równorzędnej firmy Microsoft, jeśli obwód ExpressRoute został utworzony po 10 2020 stycznia. Jeśli Twój obwód została utworzona wcześniej, BFD nie została włączona. Można włączyć BFD przez wyłączenie obsługi komunikacji równorzędnej, a następnie ponowne włączanie go. 

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Resetuj komunikację równorzędną

1. Jeśli korzystasz z programu PowerShell lokalnie, otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i nawiąż połączenie z kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

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
5. Zidentyfikuj, komunikację równorzędną, którą chcesz włączyć lub wyłączyć. *Komunikacja równorzędna* jest tablicą. W poniższym przykładzie komunikacji równorzędnej [0] jest prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej [1] Peering firmy Microsoft.

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
   Komunikacja równorzędna powinna być w stanie, gdy ustawiasz. 

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz pomocy, aby rozwiązać problem z usługi ExpressRoute, zapoznaj się z następującymi artykułami:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
