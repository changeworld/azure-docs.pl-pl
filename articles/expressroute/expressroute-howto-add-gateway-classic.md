---
title: 'Azure ExpressRoute: Dodawanie bramy do sieci wirtualnej: klasyczny'
description: Konfigurowanie bramy sieci wirtualnej dla wdrożenia klasycznego modelu sieci wirtualnej przy użyciu programu PowerShell do konfiguracji usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928059"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — witryna Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule opisano kroki umożliwiające dodawanie, zmienianie rozmiaru i usuwanie bramy sieci wirtualnej (VNet) wstępnie istniejącej sieci wirtualnej. W krokach dla tej konfiguracji są przeznaczone dla sieci wirtualnych, które zostały utworzone przy użyciu **klasycznego modelu wdrażania** i który będzie używany w konfiguracji usługi ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Przed rozpoczęciem
Sprawdź, czy zainstalowano polecenia cmdlet Azure PowerShell potrzebne dla tej konfiguracji.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej, możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute. Zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

