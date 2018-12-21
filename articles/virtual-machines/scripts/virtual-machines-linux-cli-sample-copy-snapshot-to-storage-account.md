---
title: Przykład dla interfejsu wiersza polecenia platformy Azure — kopiowanie migawki na konto magazynu w innym regionie | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — eksportowanie/kopiowanie migawki jako dysku VHD na konto magazynu w tym samym lub innym regionie.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: bdc9da206e938c0c7fd3d027ee02b11d01a1b4d0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106723"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Eksportowanie/kopiowanie migawki na konto magazynu w innym regionie przy użyciu interfejsu wiersza polecenia

Ten skrypt eksportuje zarządzaną migawkę do konta magazynu w innym regionie. Najpierw generuje on identyfikator URI sygnatury dostępu współdzielonego migawki, a następnie używa go w celu skopiowania go do konta magazynu w innym regionie. Ten skrypt umożliwia obsługę kopii zapasowej dysków zarządzanych w innym regionie na potrzeby odzyskiwania po awarii. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń do generowania identyfikatora URI sygnatury dostępu współdzielonego dla migawki zarządzanej i kopiuje migawkę do konta magazynu przy użyciu identyfikatora URI sygnatury dostępu współdzielonego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Generuje sygnaturę dostępu współdzielonego tylko do odczytu, która jest używana do kopiowania odpowiedniego pliku VHD do konta magazynu lub pobierania go do środowiska lokalnego  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Asynchronicznie kopiuje obiekt blob z jednego konta magazynu do innego |

## <a name="next-steps"></a>Następne kroki

[Tworzenie dysku zarządzanego na podstawie dysku VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
