---
title: 'Skrypt programu PowerShell: Zestaw dozwolone rozmiary maszyn wirtualnych w usłudze Azure Lab Services | Dokumentacja firmy Microsoft'
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
ms.openlocfilehash: 0c82e304d3e3d8df1206c7c05883399b74229af7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160548"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Użyj programu PowerShell, aby ustawić dozwolone rozmiary maszyn wirtualnych w usłudze Azure Lab Services

Ten przykładowy skrypt programu PowerShell ustawia rozmiarów dozwolonych maszyn wirtualnych (VM) w usłudze Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| Find-AzResource | Wyszukiwanie zasobów, w oparciu o określonych parametrów. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasoby. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modyfikuje zasób. |
| [Nowe AzResource](/powershell/module/az.resources/new-azresource) | Utwórz zasób. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell usługi laboratorium Azure można znaleźć w [przykładów programu Azure Lab Services PowerShell](../samples-powershell.md).
