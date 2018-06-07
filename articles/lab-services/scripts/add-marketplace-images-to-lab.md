---
title: 'Skrypt programu PowerShell: Dodaj obrazu z witryny marketplace do laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell dodaje laboratorium w usłudze Azure DevTest Labs obrazu z witryny marketplace.
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
ms.openlocfilehash: 9a65237b3eba6c9878d73148f0143f20dd60dd79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636552"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Dodawanie obrazu z witryny marketplace do laboratorium w usłudze Azure DevTest Labs przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell dodaje obrazu z witryny marketplace do laboratorium w usłudze Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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