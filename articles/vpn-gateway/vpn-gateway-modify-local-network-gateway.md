---
title: Zmodyfikuj prefiksy adresów IP bramy sieci lokalnej i adres IP bramy sieci VPN | Azure | PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono zmiana prefiksów adresów IP dla bramy sieci lokalnej za pomocą programu PowerShell
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 2a095b96a8c352abeca72640d37c0d629b447763
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23885105"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modyfikowanie ustawień lokalnej bramy sieci przy użyciu programu PowerShell

Czasami zmienić ustawienia dla bramy sieci lokalnej prefiks adresu lub GatewayIPAddress. W tym artykule przedstawiono sposób zmodyfikować ustawienia bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z poniższej listy:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Instalowanie i konfigurowanie programu Azure PowerShell).

## <a name="ipaddprefix"></a>Zmodyfikuj prefiksy adresów IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Zmodyfikuj adres IP bramy

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Można sprawdzić połączenie bramy. Zobacz [sprawdzić połączenie bramy](vpn-gateway-verify-connection-resource-manager.md).