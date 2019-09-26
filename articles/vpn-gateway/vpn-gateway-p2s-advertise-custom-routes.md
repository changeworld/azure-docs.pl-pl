---
title: Anonsowanie tras niestandardowych dla klientów sieci VPN typu punkt-lokacja | Azure | Microsoft Docs
description: Procedura anonsowania tras niestandardowych na klientach punkt-lokacja
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2019
ms.author: cherylmc
ms.openlocfilehash: 18a0effcf5157ec2797707db78f9614ef12a4669
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310245"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anonsowanie tras niestandardowych dla klientów sieci VPN P2S

Możesz chcieć anonsować trasy niestandardowe wszystkim klientom sieci VPN typu punkt-lokacja. Na przykład jeśli włączono punkty końcowe magazynu w sieci wirtualnej i chcesz, aby użytkownicy zdalni mieli dostęp do tych kont magazynu za pośrednictwem połączenia sieci VPN. Adres IP punktu końcowego magazynu można anonsować wszystkim użytkownikom zdalnym w taki sposób, aby ruch do konta magazynu przeszedł przez tunel VPN, a nie publiczny Internet.

## <a name="to-advertise-custom-routes"></a>Aby anonsować trasy niestandardowe

Aby anonsować trasy niestandardowe, użyj `Set-AzVirtualNetworkGateway cmdlet`. Poniższy przykład przedstawia sposób anonsowania adresu IP dla [tabel kont magazynu Conotoso](https://contoso.table.core.windows.net).

1. Wyślij polecenie ping do *contoso.Table.Core.Windows.NET* i Zanotuj adres IP. Na przykład:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Uruchom następujące polecenia programu PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Aby dodać wiele tras niestandardowych, użyj Coma i spacji, aby rozdzielić adresy. Na przykład:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Aby wyświetlić trasy niestandardowe

Użyj poniższego przykładu, aby wyświetlić trasy niestandardowe:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    $gw.CustomRoutes | Format-List
    ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje dotyczące routingu P2S, zobacz [Informacje o routingu punkt-lokacja](vpn-gateway-about-point-to-site-routing.md).
