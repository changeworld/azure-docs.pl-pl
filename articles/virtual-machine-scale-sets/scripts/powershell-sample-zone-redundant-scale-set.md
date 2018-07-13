---
title: Przykłady dla programu Azure PowerShell — strefowo nadmiarowy zestaw skalowania | Microsoft Docs
description: Przykłady dla programu Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: de0e4e68033559a450d16fe531deff063923f5e0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652249"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Tworzenie strefowo nadmiarowego zestawu skalowania maszyn wirtualnych przy użyciu programu PowerShell
Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Windows Server 2016 w wielu strefach dostępności. Po uruchomieniu skryptu dostęp do maszyny wirtualnej można uzyskać za pomocą protokołu RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Tworzy zestaw skalowania maszyn wirtualnych i wszystkie pomocnicze zasoby, w tym sieć wirtualną, moduł równoważenia obciążenia i reguły NAT. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Pobiera informacje o zestawie skalowania maszyn wirtualnych. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Dodaje rozszerzenie maszyny wirtualnej dla niestandardowego skryptu, aby zainstalować podstawową aplikację internetową. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aktualizuje model zestawu skalowania maszyn wirtualnych w celu zastosowania rozszerzenia maszyny wirtualnej. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Pobiera informacje o przypisanym publicznym adresie IP używanym przez moduł równoważenia obciążenia. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../powershell-samples.md).