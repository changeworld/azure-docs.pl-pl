---
title: 'Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell: klasyczny: Azure | Dokumentacja firmy Microsoft'
description: Konfigurowanie bramy sieci wirtualnej dla wdrożenia klasycznego modelu sieci wirtualnej przy użyciu programu PowerShell do konfiguracji usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.openlocfilehash: 82deca87f2410afab7b63f03b222db71db8d9b5c
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218227"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — witryna Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule opisano kroki umożliwiające dodawanie, zmienianie rozmiaru i usuwanie bramy sieci wirtualnej (VNet) wstępnie istniejącej sieci wirtualnej. W krokach dla tej konfiguracji są przeznaczone dla sieci wirtualnych, które zostały utworzone przy użyciu **klasycznego modelu wdrażania** i który będzie używany w konfiguracji usługi ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Przed rozpoczęciem
Sprawdź, czy zostały zainstalowane polecenia cmdlet programu Azure PowerShell potrzebne dla tej konfiguracji (1.0.2 lub nowsza). Jeśli nie zainstalowano poleceń cmdlet, należy to zrobić przed rozpoczęciem procedury konfiguracji. Aby uzyskać więcej informacji na temat instalowania programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bramy sieci wirtualnej, możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute. Zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

