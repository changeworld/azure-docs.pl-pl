---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 12f8a8edc995a5b3e07244eb5c54835fa6dd832d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039941"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Tworzenie dysku zarządzanego na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji przy użyciu interfejsu wiersza polecenia

Ten skrypt tworzy dysk zarządzany na podstawie pliku VHD na koncie magazynu w ramach tej samej subskrypcji. Użyj tego skryptu, aby importować specjalny (nieuogólniony/przygotowany przez program Sysprep) dysk VHD do zarządzanego dysku systemu operacyjnego w celu utworzenia maszyny wirtualnej. Pozwala on również na importowanie dysku VHD danych do zarządzanego dysku danych.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie dysku VHD. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Tworzy dysk zarządzany przy użyciu identyfikatora URI dysku VHD na koncie magazynu w ramach tej samej subskrypcji |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładów skryptu interfejsu wiersza polecenia maszyny wirtualnej i dysków zarządzanych można znaleźć w [dokumentacji maszyny wirtualnej systemu Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
