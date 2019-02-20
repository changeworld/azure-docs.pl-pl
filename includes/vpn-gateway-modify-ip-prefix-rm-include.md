---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da351942ae4e53fa48ae20616b5ea480d4fe03e4
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418301"
---
### <a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

Aby dodać dodatkowe prefiksy adresów:

1. Ustaw wartość zmiennej dla klasy LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. Zmodyfikuj prefiksy.

  ```azurepowershell-interactive
  Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
  ```

Aby usunąć prefiksy adresów:

  Opuść prefiksy, które nie są już potrzebne. W tym przykładzie firma Microsoft nie są już potrzebne prefiksy 10.101.2.0/24 (z poprzedniego przykładu), dlatego zaktualizujemy bramę sieci lokalnej, wykluczając ten prefiks.

1. Ustaw wartość zmiennej dla klasy LocalNetworkGateway.

  ```azurepowershell-interactive
  $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
  ```
2. Ustaw bramy z prefiksami zaktualizowane.

  ```azurepowershell-interactive
  Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

### <a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli istnieje już połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP zawarte w bramie Twojej sieci lokalnej, wykonaj kolejno następujące kroki. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.

1. Usuń połączenie.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Ustaw bramy sieci lokalnej z prefiksami adresów zmodyfikowane.
   
   Ustaw wartość zmiennej dla klasy LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
   
   Zmodyfikuj prefiksy.
   
   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
3. Utwórz połączenie. W tym przykładzie skonfigurujemy połączenie typu IPsec. Podczas ponownego tworzenia połączenia należy użyć typu połączenia, który został określony dla danej konfiguracji. O dodatkowych typach połączeń można przeczytać na stronie [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) (Polecenia cmdlet programu PowerShell).
   
   Ustaw wartość zmiennej dla klasy VirtualNetworkGateway.

   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
   ```
   
   Utwórz połączenie. W tym przykładzie użyto zmiennej $local ustawionej w kroku 2.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1 -Location 'East US' `
   -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec `
   -RoutingWeight 10 -SharedKey 'abc123'
   ```