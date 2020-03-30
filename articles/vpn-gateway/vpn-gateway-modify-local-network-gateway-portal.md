---
title: 'Brama sieci VPN: modyfikowanie ustawień adresu IP bramy: witryna Azure portal'
description: W tym artykule oszukuje cię zmieniające się prefiksy adresów IP bramy sieci lokalnej przy użyciu witryny Azure portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864030"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modyfikowanie ustawień bramy sieci lokalnej przy użyciu witryny Azure portal

Czasami zmieniają się ustawienia adresu bramy sieci lokalnejPrefix lub GatewayIPAddress. W tym artykule pokazano, jak zmodyfikować ustawienia bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z poniższej listy:

Przed usunięciem połączenia można pobrać konfigurację urządzeń łączących, aby uzyskać zdefiniowany plik PSK. W ten sposób nie trzeba na nowo definiować go po drugiej stronie.

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikowanie prefiksów adresów IP

Podczas modyfikowania prefiksów adresów IP kroki, które należy wykonać, zależą od tego, czy brama sieci lokalnej ma połączenie.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modyfikowanie adresu IP bramy

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Zmiana publicznego adresu IP zależy od tego, czy brama sieci lokalnej ma połączenie.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie bramy. Zobacz [Zweryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).
