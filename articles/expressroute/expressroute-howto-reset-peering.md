---
title: Resetuj komunikacje równorzędne usługi Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Jak wyłączyć lub włączyć peeerings obwodu usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b8c9bc1944e9ed0281616062a84958c953d08694
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180816"
---
# <a name="reset-expressroute-peerings"></a>Resetuj komunikacje równorzędne usługi ExpressRoute

W tym artykule opisano, jak wyłączyć lub włączyć komunikację równorzędną dla obwodu usługi ExpressRoute za pomocą programu PowerShell. Po wyłączeniu komunikację równorzędną, sesji protokołu BGP, zarówno połączenia podstawowego, jak i połączenia pomocniczego obwodu usługi ExpressRoute zostanie zamknięty. Użytkownik utraci łączność za pośrednictwem komunikacji równorzędnej do firmy Microsoft. Po włączeniu komunikacji równorzędnej sesji protokołu BGP, zarówno połączenia podstawowego, jak i połączenia pomocniczego obwodu usługi ExpressRoute zostanie wznowione. Odzyska łączność za pośrednictwem komunikacji równorzędnej do firmy Microsoft. Można włączać i wyłączać Peering firmy Microsoft i Azure prywatnej komunikacji równorzędnej na obwód usługi ExpressRoute w niezależnie. Podczas pierwszej konfiguracji komunikacji równorzędnej na obwód usługi ExpressRoute, komunikację równorzędną są domyślnie włączone. 

Istnieje kilka scenariuszy, gdzie może okazać się przydatne, resetowanie komunikacji równorzędnej usługi ExpressRoute.
* Testowanie projektu odzyskiwania po awarii i implementacji. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikacje równorzędne jednym obwodu i wymusić ruchu sieciowego do trybu failover z obwodem usługi.
* Włączanie dwukierunkowego przekazywania wykrywania (BFD) na platformie Azure prywatną komunikację równorzędną dla obwodu usługi ExpressRoute. BFD jest domyślnie włączona, jeśli obwód usługi ExpressRoute jest tworzony po 1 sierpnia 2018 r. Jeśli Twój obwód została utworzona wcześniej, BFD nie została włączona. Można włączyć BFD przez wyłączenie obsługi komunikacji równorzędnej, a następnie ponowne włączanie go. Należy zauważyć, że BFD jest tylko obsługiwana w prywatnej komunikacji równorzędnej Azure.


## <a name="reset-a-peering"></a>Resetuj komunikację równorzędną

1. Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

  ```powershell
  Connect-AzureRmAccount
  ```
3. Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Wskaż subskrypcję, której chcesz użyć.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Uruchom następujące polecenia, aby pobrać obwód usługi ExpressRoute.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```
6. Zidentyfikuj, komunikację równorzędną, którą chcesz włączyć lub wyłączyć. *Komunikacja równorzędna* jest tablicą. W poniższym przykładzie komunikacji równorzędnej [0] jest prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej [1] Peering firmy Microsoft.

  ```powershell
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
7. Uruchom następujące polecenia, aby zmienić stan komunikacji równorzędnej.

  ```powershell
  $ckt.Peerings[0].State = "Disabled"
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```
Komunikacja równorzędna powinna być w stanie, gdy ustawiasz. 

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz pomocy, aby rozwiązać problem z usługi ExpressRoute, zapoznaj się z następującymi artykułami:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
