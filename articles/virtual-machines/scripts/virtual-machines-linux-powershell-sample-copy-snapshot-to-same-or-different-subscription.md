---
title: Przykładowy skrypt programu Azure PowerShell — kopiowanie (przenoszenie) migawki dysku zarządzanego do tej samej lub innej subskrypcji | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — kopiowanie (przenoszenie) migawki dysku zarządzanego do tej samej lub innej subskrypcji
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: aadcdc679d8d0df82c7ddc41b4dcdd6766bb7426
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249683"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiowanie migawki dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu programu PowerShell

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Użyj tego skryptu w następujących scenariuszach:

1. Przeprowadź migrację migawki w usłudze Premium storage (Premium_LRS) do magazynu w warstwie standardowa (Standard_LRS lub Standard_ZRS), aby zmniejszyć koszt.
1. Migrować migawki z magazyn lokalnie nadmiarowy (Premium_LRS, Standard_LRS), aby Magazyn strefowo nadmiarowy (Standard_ZRS), aby korzystać z wyższej niezawodności magazynu ZRS.
1. Przenoszenie migawki do innej subskrypcji w tym samym regionie dłuższy okres przechowywania.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Tworzy konfigurację migawki, która służy do tworzenia migawki. Zawiera identyfikator zasobu migawki nadrzędnej oraz lokalizację, która jest taka sama jak lokalizacja migawki nadrzędnej.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy migawkę przy użyciu konfiguracji migawki, nazwy migawki i nazwy grupy zasobów przekazywanych jako parametry. |

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).