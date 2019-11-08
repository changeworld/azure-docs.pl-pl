---
title: 'Dodawanie bramy sieci wirtualnej do sieci wirtualnej dla usługi ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Ten artykuł ułatwia dodanie bramy sieci wirtualnej do już utworzonej Menedżer zasobów sieci wirtualnej dla usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bbb091addd078a08a075d2754256ed016d8dd086
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748341"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ten artykuł ułatwia dodawanie, zmienianie rozmiaru i usuwanie bramy sieci wirtualnej (VNet) dla istniejącej sieci wirtualnej. Kroki tej konfiguracji dotyczą sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania Menedżer zasobów na potrzeby konfiguracji ExpressRoute. Aby uzyskać więcej informacji, zobacz [Informacje o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Przed rozpoczęciem

### <a name="working-with-powershell"></a>Praca z programem PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista odwołań do konfiguracji

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć sieć wirtualną z obwodem ExpressRoute. Zobacz [Link Virtual Network do obwodu ExpressRoute](expressroute-howto-linkvnet-arm.md).
