---
title: 'Skrypt programu PowerShell: Utworzyć rolę niestandardową, w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium Azure DevTest Labs.
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
ms.openlocfilehash: 4d0c795dffb40ab7efec9005660439f9baef9f3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160576"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Aby utworzyć rolę niestandardową, w laboratorium Azure DevTest Labs przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy niestandardową rolę do użycia w laboratorium Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Pobiera operacje dla dostawcy zasobów platformy Azure, które są zabezpieczane przy użyciu RBAC platformy Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Wyświetla listę wszystkich ról RBAC platformy Azure, które są dostępne do przypisania. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Tworzy rolę niestandardową. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell usługi laboratorium Azure można znaleźć w [przykładów programu Azure Lab Services PowerShell](../samples-powershell.md).
