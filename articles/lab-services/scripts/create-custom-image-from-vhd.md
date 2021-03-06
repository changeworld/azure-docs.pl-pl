---
title: PowerShell — tworzenie obrazu niestandardowego z pliku VHD w usłudze Azure Lab Services
description: Ten skrypt programu PowerShell tworzy obraz niestandardowy z pliku VHD w usłudze Azure Lab Services.
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
ms.openlocfilehash: 38383462a665ced1ccb6c6a2f062fab0492eee9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169985"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Tworzenie niestandardowego obrazu z pliku VHD w usługach Azure Lab Services za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy obraz niestandardowy z pliku VHD w usłudze Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasoby. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Pobiera klucze dostępu dla konta usługi Azure Storage. |
| [Nowy-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Dodaje wdrożenie platformy Azure do grupy zasobów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach programu Azure Lab Services PowerShell.](../samples-powershell.md)
