---
title: 'Dodaj bramę sieci wirtualnej do sieci wirtualnej dla usługi ExpressRoute: Program PowerShell: Azure | Microsoft Docs'
description: W tym artykule ułatwiają dodawanie bramy sieci wirtualnej do sieci wirtualnej usługi Resource Manager zostały już utworzone dla usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6ab701adcc2122270c607604f222ac5edbceeb37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730507"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ten artykuł ułatwia dodawanie, zmienianie rozmiaru i usuwanie bramy sieci wirtualnej (VNet) wstępnie istniejącej sieci wirtualnej. W krokach dla tej konfiguracji dotyczy sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania usługi Resource Manager dla konfiguracji usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Przed rozpoczęciem

### <a name="working-with-powershell"></a>Praca z programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista odwołań konfiguracji

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bramy sieci wirtualnej, możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute. Zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).
