---
title: Przykładowy skrypt programu PowerShell Azure - migawki eksportu/kopiowania jako dysk VHD do konta magazynu w innym regionie | Dokumentacja firmy Microsoft
description: Przykładowy skrypt programu PowerShell Azure - migawki eksportu/kopiowania jako dysk VHD do konta magazynu w tym samym regionie innym
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
ms.openlocfilehash: be21a891121df1d645b430d87b572cde6c945d61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879645"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Eksport/kopiowania zarządzane migawki jako dysk VHD do konta magazynu w innym regionie przy użyciu programu PowerShell

Ten skrypt eksportuje zarządzanych migawki na konto magazynu w innym regionie. Najpierw generuje identyfikator URI sygnatury dostępu Współdzielonego migawki, a następnie używa, aby skopiować go do konta magazynu w innym regionie. Użyj tego skryptu do obsługi kopii zapasowych dysków zarządzanych w innym regionie odzyskiwania po awarii.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń, można wygenerować identyfikatora URI sygnatury dostępu Współdzielonego dla zarządzanych migawki i kopiuje migawki na konto magazynu przy użyciu identyfikatora URI połączenia SAS. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Udziel AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | Generuje identyfikator URI sygnatury dostępu Współdzielonego dla migawki, która jest używana, aby skopiować go do konta magazynu. |
| [Nowe AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Tworzy kontekst konta magazynu przy użyciu nazwy konta i klucz. Tego kontekstu może służyć do wykonywania operacji odczytu/zapisu na koncie magazynu. |
| [Start AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Kopiuje podstawowy dysk VHD migawki na konto magazynu |

## <a name="next-steps"></a>Następne kroki

[Tworzenie dysku zarządzanego z wirtualnego dysku twardego](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Utwórz maszynę wirtualną z dyskiem zarządzanym](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).

Przykłady skryptów PowerShell dodatkowe maszyny wirtualnej znajdują się w [dokumentacji maszyny Wirtualnej systemu Windows Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).