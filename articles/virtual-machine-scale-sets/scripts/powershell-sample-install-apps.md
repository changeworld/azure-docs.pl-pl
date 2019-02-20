---
title: Przykłady dla programu Azure PowerShell — instalacja aplikacji | Microsoft Docs
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
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a2887eea0e4a43e21c776f7d25a1ee55971d80a1
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981608"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>Instalowanie aplikacji w zestawie skalowania maszyn wirtualnych przy użyciu programu PowerShell
Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Windows Server 2016 i używa rozszerzenia niestandardowego skryptu w celu zainstalowania podstawowej aplikacji internetowej. Po uruchomieniu skryptu możesz uzyskać dostęp do aplikacji internetowej za pośrednictwem przeglądarki internetowej.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Tworzy zestaw skalowania maszyn wirtualnych i wszystkie pomocnicze zasoby, w tym sieć wirtualną, moduł równoważenia obciążenia i reguły NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Pobiera informacje o zestawie skalowania maszyn wirtualnych. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Dodaje rozszerzenie maszyny wirtualnej dla niestandardowego skryptu, aby zainstalować podstawową aplikację internetową. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Aktualizuje model zestawu skalowania maszyn wirtualnych w celu zastosowania rozszerzenia maszyny wirtualnej. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Pobiera informacje o przypisanym publicznym adresie IP używanym przez moduł równoważenia obciążenia. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../powershell-samples.md).
