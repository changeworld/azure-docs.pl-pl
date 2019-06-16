---
title: Weryfikowanie połączenia bramy sieci VPN | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak zweryfikować połączenie sieci VPN bramy sieci wirtualnej.
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
ms.openlocfilehash: bcece64a85a69c705b0f7f915dafe29123a2cd4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128368"
---
# <a name="verify-a-vpn-gateway-connection"></a>Weryfikowanie połączenia bramy sieci VPN

W tym artykule pokazano, jak zweryfikować połączenie bramy sieci VPN dla wdrożeń klasycznych, jak i modelem wdrażania usługi Resource Manager.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Aby sprawdzić połączenie bramy sieci VPN w przypadku modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell, zainstaluj najnowszą wersję [poleceń cmdlet programu PowerShell usługi Resource Manager platformy Azure](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby sprawdzić połączenie bramy sieci VPN w przypadku modelu wdrażania usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, zainstalować najnowszą wersję [poleceń interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 lub nowszą).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Witryna Azure portal (klasyczny)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasyczny)

Aby sprawdzić połączenie bramy sieci VPN dla klasycznego modelu wdrażania przy użyciu programu PowerShell, należy zainstalować najnowsze wersje poleceń cmdlet programu Azure PowerShell. Pamiętaj pobrać i zainstalować [zarządzania usługami](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) modułu. Użyj "Add-AzureAccount", aby zalogować się do klasycznego modelu wdrażania.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Kolejne kroki

* Do sieci wirtualnych można dodać maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
