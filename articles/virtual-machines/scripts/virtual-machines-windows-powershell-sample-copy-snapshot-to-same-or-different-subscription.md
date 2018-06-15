---
title: Przykładowy skrypt programu PowerShell Azure - migawki kopiowania (przenoszenia) dysków zarządzanych do tych samych lub różnych subskrypcji | Dokumentacja firmy Microsoft
description: Przykładowy skrypt programu PowerShell Azure - migawki kopiowania (przenoszenia) dysków zarządzanych do tych samych lub różnych subskrypcji
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879659"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiowanie migawek dysków zarządzanych w tej samej subskrypcji lub różnych subskrypcji przy użyciu programu PowerShell

Ten skrypt tworzy kopię migawkę w tej samej subskrypcji tej samej lub innej subskrypcji. Użyj tego skryptu można przenieść do innej subskrypcji dla przechowywania danych migawki. Zapisywanie migawek w innej subskrypcji Chroń przed przypadkowym usunięciem migawek w ramach subskrypcji głównego. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń do tworzenia migawek w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Nowe AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Tworzy konfigurację migawki, która służy do tworzenia migawek. Zawiera ona identyfikator nadrzędnego migawki i lokalizacji, która jest taka sama jak nadrzędnego migawki zasobu.  |
| [Nowe AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Tworzy migawkę przy użyciu konfiguracji migawki, nazwę migawki i przekazywane jako parametry nazwę grupy zasobów. |


## <a name="next-steps"></a>Następne kroki

[Utwórz maszynę wirtualną z migawki](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).

Przykłady skryptów PowerShell dodatkowe maszyny wirtualnej znajdują się w [dokumentacji maszyny Wirtualnej systemu Windows Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).