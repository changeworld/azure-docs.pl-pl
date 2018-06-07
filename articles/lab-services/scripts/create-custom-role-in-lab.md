---
title: 'Skrypt programu PowerShell: Tworzenie niestandardowej roli zabezpieczeń w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium w usłudze Azure DevTest Labs.
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
ms.openlocfilehash: 295f742342fba7d77b556724c8005f3ac4816482
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636719"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Użyj programu PowerShell, aby utworzyć niestandardową rolę w laboratorium w usłudze Azure DevTest Labs

Ten przykładowy skrypt programu PowerShell tworzy niestandardowej roli zabezpieczeń do użycia w laboratorium w usłudze Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Pobiera operacje dla dostawcy zasobów platformy Azure, które są możliwe do zabezpieczenia przy użyciu funkcji RBAC Azure. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Wyświetla wszystkie role Azure RBAC, które są dostępne do przypisania. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Tworzy niestandardowej roli zabezpieczeń. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów środowiska PowerShell usługi laboratorium Azure można znaleźć w [przykłady Azure PowerShell usługi laboratorium](../samples-powershell.md).