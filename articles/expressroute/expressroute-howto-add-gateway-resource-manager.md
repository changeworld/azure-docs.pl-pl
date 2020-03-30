---
title: 'Usługa Azure ExpressRoute: dodawanie bramy do sieci wirtualnej: program PowerShell'
description: Ten artykuł ułatwia dodawanie bramy sieci wirtualnej do już utworzonej sieci wirtualnej Menedżera zasobów dla usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037428"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ten artykuł ułatwia dodawanie, ponowne rozmiary i usuwanie bramy sieci wirtualnej dla istniejącej sieci wirtualnej. Kroki dla tej konfiguracji dotyczą sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania Menedżera zasobów dla konfiguracji usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [Informacje o bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Przed rozpoczęciem

### <a name="working-with-powershell"></a>Praca z programem PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista odwołań do konfiguracji

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć swoją witrynę wirtualną z obwódem usługi ExpressRoute. Zobacz [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).
