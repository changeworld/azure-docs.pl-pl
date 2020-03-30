---
title: 'Brama sieci VPN platformy Azure: weryfikowanie połączenia bramy'
description: W tym artykule pokazano, jak zweryfikować połączenie bramy sieci wirtualnej sieci VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780151"
---
# <a name="verify-a-vpn-gateway-connection"></a>Weryfikowanie połączenia bramy sieci VPN

W tym artykule pokazano, jak zweryfikować połączenie bramy sieci VPN dla modeli wdrażania klasycznego i Menedżera zasobów.

## <a name="azure-portal"></a>Portal Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Aby zweryfikować połączenie bramy sieci VPN dla modelu wdrażania Menedżera zasobów przy użyciu programu PowerShell, zainstaluj najnowszą wersję [poleceń cmdlet programu Azure Resource Manager programu PowerShell](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby zweryfikować połączenie bramy sieci VPN dla modelu wdrażania usługi Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2.0 lub [nowszych).](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portal Azure (klasyczny)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasyczny)

Aby zweryfikować połączenie bramy sieci VPN dla klasycznego modelu wdrażania przy użyciu programu PowerShell, zainstaluj najnowsze wersje poleceń cmdlet programu Azure PowerShell. Pamiętaj, aby pobrać i zainstalować moduł [zarządzania usługami.](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Użyj "Add-AzureAccount", aby zalogować się do klasycznego modelu wdrażania.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Następne kroki

* Do sieci wirtualnych można dodać maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
