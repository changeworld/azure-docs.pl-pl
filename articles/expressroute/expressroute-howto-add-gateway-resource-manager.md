---
title: 'Dodawanie bramy sieci wirtualnej do sieci wirtualnej dla usługi ExpressRoute: programu PowerShell: Azure | Dokumentacja firmy Microsoft'
description: W tym artykule ułatwiają dodawanie bramy sieci wirtualnej do sieci wirtualnej usługi Resource Manager zostały już utworzone dla usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097917"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ten artykuł ułatwia dodawanie, zmienianie rozmiaru i usuwanie bramy sieci wirtualnej (VNet) wstępnie istniejącej sieci wirtualnej. W krokach dla tej konfiguracji są przeznaczone na potrzeby sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania usługi Resource Manager, który będzie używany w konfiguracji usługi ExpressRoute. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i ustawienia konfiguracji bramy usługi ExpressRoute, zobacz [o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Przed rozpoczęciem
Sprawdź, czy zostały zainstalowane najnowsze polecenia cmdlet programu Azure PowerShell. Jeśli nie zainstalowano najnowsze polecenia cmdlet, należy to zrobić przed rozpoczęciem procedury konfiguracji. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bramy sieci wirtualnej, możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute. Zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).

