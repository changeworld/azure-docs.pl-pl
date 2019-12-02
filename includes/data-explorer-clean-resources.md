---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667845"
---
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Czyszczenie zasobów przy użyciu Azure Portal

Usuń zasoby z Azure Portal, wykonując czynności opisane w sekcji [Oczyszczanie zasobów](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Oczyszczanie zasobów przy użyciu programu PowerShell

Jeśli Cloud Shell nadal jest otwarty, nie musisz kopiować/uruchamiać pierwszego wiersza (odczyt-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```