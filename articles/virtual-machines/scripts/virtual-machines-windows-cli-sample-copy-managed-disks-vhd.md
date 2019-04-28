---
title: Przykład dla interfejsu wiersza polecenia platformy Azure — kopiowanie dysków zarządzanych na konto magazynu
description: Przykład dla interfejsu wiersza polecenia platformy Azure — eksportowanie lub kopiowanie dysków zarządzanych na konto magazynu
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: d6009a723297d03dc854d06529315b22b2f4de16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789525"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Eksportowanie/kopiowanie dysków zarządzanych na konto magazynu przy użyciu wiersza polecenia platformy Azure

Ten skrypt umożliwia wyeksportowanie odpowiedniego wirtualnego dysku twardego dysku zarządzanego na konto magazynu w tym samym lub innym regionie. Najpierw generuje on identyfikator URI sygnatury dostępu współdzielonego dysku zarządzanego, a następnie używa go do skopiowania wirtualnego dysku twardego na konto magazynu. Ten skrypt umożliwia kopiowanie dysków zarządzanych do innego regionu.

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

[Tworzenie dysku zarządzanego na podstawie dysku VHD](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe maszyny wirtualnej i dyski zarządzane przykładowych skryptów interfejsu wiersza polecenia można znaleźć w [dokumentacji dotyczącej maszyny Wirtualnej Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
