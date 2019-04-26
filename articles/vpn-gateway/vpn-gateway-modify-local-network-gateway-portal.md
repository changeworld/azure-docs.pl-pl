---
title: Zmodyfikuj prefiksy adresów IP bramy sieci lokalnej i adres IP bramy sieci VPN | Azure | Portal | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi zmiana prefiksy adresów IP dla bramy sieci lokalnej za pomocą witryny Azure portal.
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 06/19/2017
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: 12f1f8bbcb103d0882059cadc12bc1a8b9d40bdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419604"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modyfikowanie ustawień lokalnej bramy sieci przy użyciu witryny Azure portal

Czasami zmienić ustawienia dla bramy sieci lokalnej, prefiks adresu lub klasy GatewayIPAddress. W tym artykule przedstawiono sposób modyfikowania ustawień bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

Zanim usuniesz połączenie, można pobrać konfiguracji dla urządzeń łączących Aby uzyskać zdefiniowanego klucza Wstępnego. Dzięki temu nie trzeba przedefiniować go po drugiej stronie.

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