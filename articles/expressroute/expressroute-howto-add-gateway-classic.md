---
title: 'Azure ExpressRoute: Dodawanie bramy do sieci wirtualnej: klasyczny'
description: Konfigurowanie bramy sieci wirtualnej dla wdrożenia klasycznego modelu sieci wirtualnej przy użyciu programu PowerShell do konfiguracji usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 75c2b400a2caf4e5f2e14589fc007bb506b46338
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031875"
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
Sprawdź, czy zostały zainstalowane polecenia cmdlet programu Azure PowerShell potrzebne dla tej konfiguracji (1.0.2 lub nowsza). Jeśli nie zainstalowano poleceń cmdlet, należy to zrobić przed rozpoczęciem procedury konfiguracji. Aby uzyskać więcej informacji na temat instalowania programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej, możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute. Zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

