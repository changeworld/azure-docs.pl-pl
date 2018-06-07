---
title: 'Skrypt programu PowerShell: zestaw dozwolone rozmiary maszyn wirtualnych w usługach laboratorium Azure | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell ustawia dozwolone rozmiary maszyn wirtualnych w usług Azure laboratorium.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 159f175e7bb27b2d89001e1eba737c67adb89e50
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638147"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Użyj programu PowerShell, aby ustawić dozwolone rozmiary maszyn wirtualnych w usług Azure laboratorium

Ten przykładowy skrypt programu PowerShell ustawia dozwolone rozmiary maszyn wirtualnych (VM) w usługach laboratorium Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Wyszukiwanie zasobów na podstawie określonych parametrów. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Pobiera zasoby. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modyfikuje zasobu. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Utwórz zasób. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów środowiska PowerShell usługi laboratorium Azure można znaleźć w [przykłady Azure PowerShell usługi laboratorium](../samples-powershell.md).