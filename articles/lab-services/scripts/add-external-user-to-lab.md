---
title: PowerShell — dodawanie użytkownika zewnętrznego do laboratorium w laboratorium usługi Azure DevTest Labs
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
ms.openlocfilehash: e9efe5476da051d905feff3d6803cb60ea0c4beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760491"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Dodawanie użytkownika zewnętrznego do laboratorium w laboratorium usługi Azure DevTest Labs za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium w laboratorium usługi Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzADUżycieli](/powershell/module/az.resources/get-azaduser) | Ponawia ponowny prób obiektu użytkownika z usługi Azure active directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Przypisuje określoną rolę do określonego podmiotu zabezpieczeń w określonym zakresie. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach programu Azure Lab Services PowerShell.](../samples-powershell.md)
