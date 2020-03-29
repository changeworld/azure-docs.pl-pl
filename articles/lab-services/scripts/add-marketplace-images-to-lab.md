---
title: PowerShell — dodawanie obrazu portalu marketplace do laboratorium w laboratorium usługi Azure DevTest Labs
description: Ten skrypt programu PowerShell dodaje obraz portalu marketplace do laboratorium w laboratorium usługi Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166447"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Dodawanie obrazu portalu Marketplace do laboratorium w laboratorium usługi Azure DevTest Labs za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell dodaje obraz portalu Marketplace do laboratorium w laboratorium usługi Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| Znajdź-AzResource | Wyszukuje zasoby na podstawie określonych parametrów. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasoby. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modyfikuje zasób. |
| [Nowe źródło AzResource](/powershell/module/az.resources/new-azresource) | Utworzyć zasób |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach programu Azure Lab Services PowerShell.](../samples-powershell.md)
