---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (przenoszenie) migawki dysków zarządzanych do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (przenoszenie) migawki dysków zarządzanych do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia
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
ms.custom: mvc
ms.openlocfilehash: c8c9f2ab2bed2e05f540422e47fdc634cb011187
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583276"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopiowanie migawki dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Umożliwia on przenoszenie migawki do innej subskrypcji w tym samym regionie jako migawki nadrzędnej.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Pobiera wszystkie właściwości migawki przy użyciu nazwy i właściwości grupy zasobów migawki. Do kopiowania migawki do innej subskrypcji jest używana właściwość Id.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | Kopiuje migawkę przez utworzenie migawki w innej subskrypcji przy użyciu identyfikatora i nazwy migawki nadrzędnej.  |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
