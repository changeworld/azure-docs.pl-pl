---
title: Program PowerShell — tworzenie roli niestandardowej w laboratorium w laboratorium usługi Azure DevTest Labs
description: Ten artykuł zawiera skrypt programu Azure PowerShell, który dodaje użytkownika zewnętrznego do laboratorium w laboratorium usługi Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760468"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Tworzenie roli niestandardowej w laboratorium w laboratorium devtest labs za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy rolę niestandardową do użycia w laboratorium w laboratorium usługi Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Pobiera operacje dla dostawcy zasobów platformy Azure, które są zabezpieczalne przy użyciu usługi Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Wyświetla listę wszystkich ról RBAC platformy Azure, które są dostępne dla przypisania. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Tworzy rolę niestandardową. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach programu Azure Lab Services PowerShell.](../samples-powershell.md)
