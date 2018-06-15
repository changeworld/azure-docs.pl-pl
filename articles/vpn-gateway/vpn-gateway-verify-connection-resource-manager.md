---
title: Sprawdź połączenie bramy sieci VPN | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób sprawdzania, połączenie sieci VPN bramy sieci wirtualnej.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: b2d702ecdd5e1fca342e7c84c6e75339097f0bcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884132"
---
# <a name="verify-a-vpn-gateway-connection"></a>Sprawdź połączenie bramy sieci VPN

W tym artykule przedstawiono sposób sprawdzić połączenie bramy sieci VPN dla modeli wdrażania usługi Resource Manager i klasycznym.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Aby sprawdzić połączenie bramy sieci VPN dla modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell, należy zainstalować najnowszą wersję [poleceń cmdlet programu PowerShell usługi Azure Resource Manager](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby sprawdzić połączenie bramy sieci VPN dla modelu wdrażania usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję [polecenia interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 lub nowszej).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portalu Azure (klasyczne)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasyczny)

Aby sprawdzić połączenie bramy sieci VPN dla klasycznym modelu wdrażania przy użyciu programu PowerShell, należy zainstalować najnowsze wersje poleceń cmdlet programu Azure PowerShell. Pamiętaj pobrać i zainstalować [zarządzania usługami](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0) modułu. Użyj "Add-AzureAccount", aby zalogować się w klasycznym modelu wdrażania.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Następne kroki

* Do sieci wirtualnych można dodać maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).