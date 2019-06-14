---
title: 'Resetuj, komunikacja równorzędna obwodu — ExpressRoute:  Azure | Microsoft Docs'
description: Jak wyłączyć lub włączyć komunikacja równorzędna obwodu usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8541362a16c7d12a0e3a4cf009ed9cd5faf9f1cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60366304"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetuj komunikacja równorzędna obwodu usługi ExpressRoute

W tym artykule opisano, jak wyłączyć lub włączyć komunikację równorzędną dla obwodu usługi ExpressRoute za pomocą programu PowerShell. Po wyłączeniu komunikację równorzędną, sesji protokołu BGP, zarówno połączenia podstawowego, jak i połączenia pomocniczego obwodu usługi ExpressRoute zostanie zamknięty. Użytkownik utraci łączność za pośrednictwem komunikacji równorzędnej do firmy Microsoft. Po włączeniu komunikacji równorzędnej sesji protokołu BGP, zarówno połączenia podstawowego, jak i połączenia pomocniczego obwodu usługi ExpressRoute zostanie wznowione. Odzyska łączność za pośrednictwem komunikacji równorzędnej do firmy Microsoft. Można włączać i wyłączać Peering firmy Microsoft i Azure prywatnej komunikacji równorzędnej na obwód usługi ExpressRoute w niezależnie. Podczas pierwszej konfiguracji komunikacji równorzędnej na obwód usługi ExpressRoute, komunikację równorzędną są domyślnie włączone.

Istnieje kilka scenariuszy, gdzie może okazać się przydatne, resetowanie komunikacji równorzędnej usługi ExpressRoute.
* Testowanie projektu odzyskiwania po awarii i implementacji. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikacje równorzędne jednym obwodu i wymusić ruchu sieciowego do trybu failover z obwodem usługi.
* Włączanie dwukierunkowego przekazywania wykrywania (BFD) na platformie Azure prywatną komunikację równorzędną dla obwodu usługi ExpressRoute. BFD jest domyślnie włączona, jeśli obwód usługi ExpressRoute jest tworzony po 1 sierpnia 2018 r. Jeśli Twój obwód została utworzona wcześniej, BFD nie została włączona. Można włączyć BFD przez wyłączenie obsługi komunikacji równorzędnej, a następnie ponowne włączanie go. Należy zauważyć, że BFD jest tylko obsługiwana w prywatnej komunikacji równorzędnej Azure.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz pomocy, aby rozwiązać problem z usługi ExpressRoute, zapoznaj się z następującymi artykułami:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
