---
title: 'Skrypt programu PowerShell: Set dozwolone rozmiary maszyn wirtualnych w usłudze Azure Lab Services | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell Określa dozwolone rozmiary maszyn wirtualnych w usłudze Azure Lab Services.
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
ms.openlocfilehash: 559e74675a5d113584dca21979c20462c9cdf19c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054710"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Użyj programu PowerShell, aby ustawić dozwolone rozmiary maszyn wirtualnych w usłudze Azure Lab Services

Ten przykładowy skrypt programu PowerShell ustawia rozmiarów dozwolonych maszyn wirtualnych (VM) w usłudze Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) | Wyszukiwanie zasobów, w oparciu o określonych parametrów. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Pobiera zasoby. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modyfikuje zasób. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Utwórz zasób. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell usługi laboratorium Azure można znaleźć w [przykładów programu Azure Lab Services PowerShell](../samples-powershell.md).
