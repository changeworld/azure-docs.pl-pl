---
title: 'Skrypt programu PowerShell: dodać użytkownika zewnętrznego w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft'
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
ms.openlocfilehash: bd86e44c21ca3c0b9c061f9a5c24bd19c4b207f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636447"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Aby dodać użytkownika zewnętrznego do laboratorium w usłudze Azure DevTest Labs za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium w usłudze Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Ponowne próby obiektu użytkownika z usługi Azure active directory. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | Przypisuje określoną rolę do podmiotu zabezpieczeń określonej w podanym zakresie. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów środowiska PowerShell usługi laboratorium Azure można znaleźć w [przykłady Azure PowerShell usługi laboratorium](../samples-powershell.md).