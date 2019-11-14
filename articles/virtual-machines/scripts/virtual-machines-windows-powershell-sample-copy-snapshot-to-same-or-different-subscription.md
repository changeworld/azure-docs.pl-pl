---
title: Przykładowy skrypt programu Azure PowerShell — kopiowanie (przenoszenie) migawki dysku zarządzanego do tej samej lub innej subskrypcji
description: Przykładowy skrypt programu Azure PowerShell — kopiowanie (przenoszenie) migawki dysku zarządzanego do tej samej lub innej subskrypcji
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 13add6614af8d3ad76e820228b748c0084a29876
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039801"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiowanie migawki dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu programu PowerShell

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Użyj tego skryptu w następujących scenariuszach:

1. Przeprowadź migrację migawki w usłudze Premium Storage (Premium_LRS) do magazynu w warstwie Standardowa (Standard_LRS lub Standard_ZRS), aby zmniejszyć koszty.
1. Przeprowadź migrację migawki z magazynu lokalnie nadmiarowego (Premium_LRS, Standard_LRS) do magazynu Strefowo nadmiarowego (Standard_ZRS), aby korzystać z wyższej niezawodności magazynu ZRS.
1. Przenieś migawkę do innej subskrypcji w tym samym regionie w celu dłuższego przechowywania.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Tworzy konfigurację migawki, która służy do tworzenia migawki. Zawiera identyfikator zasobu migawki nadrzędnej oraz lokalizację, która jest taka sama jak lokalizacja migawki nadrzędnej.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy migawkę przy użyciu konfiguracji migawki, nazwy migawki i nazwy grupy zasobów przekazywanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
