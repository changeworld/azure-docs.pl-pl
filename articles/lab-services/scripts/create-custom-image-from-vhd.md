---
title: 'Skrypt programu PowerShell: Tworzenie niestandardowego obrazu z pliku VHD w usług Azure laboratorium | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell tworzy niestandardowy obraz z pliku VHD w usług Azure laboratorium.
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
ms.openlocfilehash: 19b7c3c6018ec56b056761c336bc56c8b63b47a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636406"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Aby utworzyć niestandardowy obraz z pliku VHD w usługach laboratorium Azure za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy niestandardowy obraz z pliku VHD w usług Azure laboratorium

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Pobiera zasoby. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Pobiera klucze dostępu dla konta usługi Azure Storage. |
| [Nowe AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | Dodaje wdrożenia usługi Azure do grupy zasobów. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów środowiska PowerShell usługi laboratorium Azure można znaleźć w [przykłady Azure PowerShell usługi laboratorium](../samples-powershell.md).