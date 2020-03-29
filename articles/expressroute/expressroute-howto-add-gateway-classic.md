---
title: 'Azure ExpressRoute: Dodawanie bramy do sieci wirtualnej: klasyczny'
description: Konfigurowanie bramy sieci wirtualnej dla klasycznej sieci wirtualnej modelu wdrażania przy użyciu programu PowerShell dla konfiguracji usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928059"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu programu PowerShell (klasyczny)
> [!div class="op_single_selector"]
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Klip wideo — witryna Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule przejdziesz przez kroki, aby dodać, zmienić rozmiar i usunąć bramę sieci wirtualnej dla istniejącej sieci wirtualnej. Kroki dla tej konfiguracji są specjalnie dla sieci wirtualnych, które zostały utworzone przy użyciu **klasycznego modelu wdrażania** i które będą używane w konfiguracji usługi ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Przed rozpoczęciem
Sprawdź, czy zostały zainstalowane polecenia cmdlet programu Azure PowerShell potrzebne dla tej konfiguracji.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć swoją witrynę wirtualną z obwódem usługi ExpressRoute. Zobacz [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-classic.md).

