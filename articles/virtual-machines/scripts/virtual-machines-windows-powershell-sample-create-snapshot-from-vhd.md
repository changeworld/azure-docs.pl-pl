---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie migawki z wirtualnego dysku twardego w celu utworzenia wielu identycznych dysków zarządzanych w krótkim czasie | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie migawki z wirtualnego dysku twardego w celu utworzenia wielu identycznych dysków zarządzanych w krótkim czasie
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 7148d28038986d3ac7f88dd57f937942b0d29bfc
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "31600244"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Tworzenie migawki z wirtualnego dysku twardego w celu utworzenia wielu takich samych dysków zarządzanych w krótkim czasie przy użyciu programu PowerShell

Ten skrypt tworzy migawkę z pliku VHD na koncie magazynu w ramach tej samej lub innej subskrypcji. Ten skrypt umożliwia zaimportowanie wyspecjalizowanego (nieuogólnionego/nieprzetworzonego przez program Sysprep) wirtualnego dysku twardego do migawki, a następnie utworzenie wielu identycznych dysków zarządzanych w krótkim czasie za pomocą migawki. Ponadto za jego pomocą można zaimportować dane wirtualnego dysku twardego do migawki, a następnie przy użyciu migawki utworzyć wiele identycznych dysków zarządzanych w krótkim czasie. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 4.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie wirtualnego dysku twardego w innej subskrypcji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Tworzy konfigurację dysku, która jest używana do tworzenia dysku. Zawiera ona typ magazynu, lokalizację, identyfikator zasobu konta magazynu, w którym przechowywany jest nadrzędny wirtualny dysk twardy, oraz identyfikator URI nadrzędnego wirtualnego dysku twardego. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Tworzy dysk przy użyciu konfiguracji dysku, nazwy dysku i nazwy grupy zasobów, przekazanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

[Tworzenie dysku zarządzanego na podstawie migawki](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysku systemu operacyjnego](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
