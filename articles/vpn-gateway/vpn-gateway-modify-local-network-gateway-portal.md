---
title: Zmodyfikuj prefiksy adresów IP bramy sieci lokalnej i adres IP bramy sieci VPN | Azure | Portal | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi zmiana prefiksy adresów IP dla bramy sieci lokalnej za pomocą witryny Azure portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651940"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modyfikowanie ustawień lokalnej bramy sieci przy użyciu witryny Azure portal

Czasami zmienić ustawienia dla bramy sieci lokalnej, prefiks adresu lub klasy GatewayIPAddress. W tym artykule przedstawiono sposób modyfikowania ustawień bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Modyfikowanie prefiksów adresów IP

Podczas modyfikowania prefiksów adresów IP, kroki, które należy wykonać są zależne od tego, czy brama sieci lokalnej z połączeniem.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Modyfikowanie adresu IP bramy

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Zmiana publicznego adresu IP, kroki, które należy wykonać są zależne od tego, czy brama sieci lokalnej z połączeniem.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Można sprawdzić połączenie bramy. Zobacz [Weryfikowanie połączenia z bramą](vpn-gateway-verify-connection-resource-manager.md).