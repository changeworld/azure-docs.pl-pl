---
title: Zmodyfikuj prefiksy adresów IP bramy sieci lokalnej i adres IP bramy sieci VPN | Azure | INTERFEJS WIERSZA POLECENIA | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi zmiana prefiksy adresów IP dla bramy sieci lokalnej przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 20e8269f0ee7463dcff9a5f66e46ebd81cb3df84
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60652858"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modyfikowanie ustawień lokalnej bramy sieci przy użyciu wiersza polecenia platformy Azure

Czasami zmienić ustawienia dla bramy sieci lokalnej, prefiks adresu lub adres IP bramy. W tym artykule przedstawiono sposób modyfikowania ustawień bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modyfikowanie prefiksów adresów IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modyfikowanie adresu IP bramy

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Można sprawdzić połączenie bramy. Zobacz [Weryfikowanie połączenia z bramą](vpn-gateway-verify-connection-resource-manager.md).

