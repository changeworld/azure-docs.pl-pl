---
title: 'Brama sieci VPN platformy Azure: anonsowanie tras niestandardowych dla klientów sieci VPN p2S'
description: Kroki reklamowania tras niestandardowych do klientów typu punkt-lokacja
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3588755e2aab1c84d443e917eca8c7fca280b49a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756884"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anonsowanie tras niestandardowych dla klientów sieci VPN p2S

Możesz anonsować niestandardowe trasy do wszystkich klientów sieci VPN typu punkt-lokacja. Na przykład po włączeniu punktów końcowych magazynu w sieci wirtualnej i chcesz, aby użytkownicy zdalni mogli uzyskiwać dostęp do tych kont magazynu za pośrednictwem połączenia sieci VPN. Adres IP punktu końcowego magazynu można anonsować wszystkim użytkownikom zdalnym, aby ruch na koncie magazynu przechodził przez tunel sieci VPN, a nie publiczny Internet.

![Przykład połączenia obejmującego wiele lokacji w usłudze Azure VPN Gateway](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Aby reklamować trasy niestandardowe

Aby reklamować trasy `Set-AzVirtualNetworkGateway cmdlet`niestandardowe, użyj pliku . W poniższym przykładzie pokazano, jak anonsować adres IP [dla tabel kont magazynu Contoso](https://contoso.table.core.windows.net).

1. Ping *contoso.table.core.windows.net* i zanotuj adres IP. Przykład:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Uruchom następujące polecenia programu PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Aby dodać wiele tras niestandardowych, użyj przecinka i spacji, aby oddzielić adresy. Przykład:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Aby wyświetlić trasy niestandardowe

Aby wyświetlić trasy niestandardowe, użyj następującego przykładu:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Aby usunąć trasy niestandardowe

Użyj następującego przykładu, aby usunąć trasy niestandardowe:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje o routingu P2S, zobacz [Informacje o routingu typu "punkt-lokacja".](vpn-gateway-about-point-to-site-routing.md)
