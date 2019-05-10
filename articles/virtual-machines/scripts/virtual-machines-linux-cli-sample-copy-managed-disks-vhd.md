---
title: Przykład dla interfejsu wiersza polecenia platformy Azure — kopiowanie dysków zarządzanych na konto magazynu
description: Interfejs wiersza polecenia Azure przykładowy — Eksport lub kopiowanie dysków zarządzanych do konta magazynu.
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
ms.date: 05/09/2019
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: c5055860df007d0f009bd08c2b211ffeeaad711f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510871"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Eksportowanie/kopiowanie dysków zarządzanych na konto magazynu przy użyciu wiersza polecenia platformy Azure

Ten skrypt umożliwia wyeksportowanie odpowiedniego wirtualnego dysku twardego dysku zarządzanego na konto magazynu w tym samym lub innym regionie. Najpierw generuje on identyfikator URI sygnatury dostępu współdzielonego dysku zarządzanego, a następnie używa go do skopiowania wirtualnego dysku twardego na konto magazynu. Ten skrypt umożliwia kopiowanie dysków zarządzanych do innego regionu w celu zwiększenia liczby regionów. Jeśli chcesz opublikować plik VHD dysku zarządzanego w witrynie Azure Marketplace służy ten skrypt można skopiować pliku VHD na koncie magazynu, a następnie wygenerować identyfikator URI sygnatury dostępu Współdzielonego skopiowanego wirtualnego dysku twardego opublikować ją w portalu Marketplace.   


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt generuje identyfikator URI sygnatury dostępu współdzielonego dysku zarządzanego przy użyciu poniższych poleceń oraz kopiuje odpowiedni wirtualny dysk twardy na konto magazynu przy użyciu identyfikatora URI sygnatury dostępu współdzielonego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Generuje sygnaturę dostępu współdzielonego tylko do odczytu, która umożliwia skopiowanie odpowiedniego pliku wirtualnego dysku twardego na konto magazynu lub pobranie go do środowiska lokalnego  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Asynchronicznie kopiuje obiekt blob z jednego konta magazynu do innego |

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie dysku zarządzanego na podstawie dysku VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
