---
title: Niestandardowe selektory ruchu dla połączeń VPN Gateway platformy Azure | Microsoft Docs
description: Dowiedz się, jak używać niestandardowych selektorów ruchu na VPN Gateway połączeniach
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512160"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Niestandardowe selektory ruchu dla połączeń VPN Gateway

Można ustawić niestandardowe selektory ruchu dla połączeń bramy sieci VPN. Możliwość ustawiania selektorów ruchu umożliwia zawężenie przestrzeni adresowej między obiema tunelami sieci VPN, na których ma być wysyłany ruch. Niestandardowe selektory ruchu są szczególnie przydatne w przypadku dużej przestrzeni adresów sieci wirtualnej, ale chcą korzystać z jednej z podsieci na potrzeby negocjacji protokołu IPsec/IKE.

Niestandardowe selektory ruchu można dodać podczas tworzenia nowego połączenia lub można je zaktualizować dla istniejącego połączenia. Parametr `TrafficSelectorPolicies` składa się z tablicy selektorów ruchu. Każdy selektor ruchu zawiera zbiór lokalnych i zdalnych zakresów adresów w formacie CIDR.

## <a name="add-custom-traffic-selectors"></a>Dodawanie niestandardowych selektorów ruchu

W poniższych przykładach pokazano, jak można tworzyć niestandardowe selektory ruchu dla połączenia bramy sieci wirtualnej przy użyciu poleceń programu PowerShell. Aby uzyskać pomoc dotyczącą tworzenia połączenia z bramą sieci wirtualnej, zobacz [Konfigurowanie połączenia lokacja-lokacja](vpn-gateway-create-site-to-site-rm-powershell.md).

1. Ustaw wartości parametru *trafficselectorpolicies* w $trafficselectorpolicy. Podczas ustawiania tego ustawienia należy zauważyć, że wartość *New-AzTrafficSelectorPolicy* przyjmuje zakresy adresów lokalnych i zdalnych w tablicy.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Utwórz nowe połączenie bramy sieci wirtualnej. Dostosuj parametry wartości dla swoich wymagań.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

Można także aktualizować niestandardowe selektory ruchu dla istniejącego połączenia bramy sieci wirtualnej za pomocą polecenia "Set-AzVirtualNetworkGatewayConnection". W przypadku wartości programu PowerShell zobacz [połączenie bramy sieci wirtualnej](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat bram sieci VPN, zobacz [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md).