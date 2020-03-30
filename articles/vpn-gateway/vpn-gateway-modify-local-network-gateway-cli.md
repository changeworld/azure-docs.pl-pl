---
title: 'Brama sieci VPN: modyfikowanie ustawień adresu IP bramy: narzędzie interfejsu wiersza polecenia platformy Azure'
description: W tym artykule oszukuje cię zmieniające się prefiksy adresów IP bramy sieci lokalnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864047"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modyfikowanie ustawień bramy sieci lokalnej przy użyciu interfejsu wiersza polecenia platformy Azure

Czasami zmieniają się ustawienia prefiksu adresu bramy sieciowej lub adresu IP bramy. W tym artykule pokazano, jak zmodyfikować ustawienia bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z poniższej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2.0 lub nowszych). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikowanie prefiksów adresów IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modyfikowanie adresu IP bramy

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie bramy. Zobacz [Zweryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).

