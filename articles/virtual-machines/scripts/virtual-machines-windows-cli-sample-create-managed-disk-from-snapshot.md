---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie migawki | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie migawki
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
ms.openlocfilehash: 8178f05b83b6128717790cc348865c32ea77d59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318108"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Tworzenie dysku zarządzanego na podstawie migawki przy użyciu interfejsu wiersza polecenia

Ten skrypt tworzy dysk zarządzany na podstawie migawki. Umożliwia on przywrócenie maszyny wirtualnej z migawek systemu operacyjnego i dysków danych. Twórz dyski zarządzane systemu operacyjnego i danych na podstawie odpowiednich migawek, a następnie utwórz nową maszynę wirtualną przez dołączenie dysków zarządzanych. Możesz również przywracać dyski z danymi istniejącej maszyny wirtualnej, dołączając dyski z danymi utworzone na podstawie migawek.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie migawki. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Pobiera wszystkie właściwości migawki przy użyciu nazwy i właściwości grupy zasobów migawki. Właściwość Id służy do tworzenia dysku zarządzanego.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Tworzy dysk zarządzany przy użyciu identyfikatora migawki zarządzanej |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe maszyny wirtualnej i dyski zarządzane przykładowych skryptów interfejsu wiersza polecenia można znaleźć w [dokumentacji dotyczącej maszyny Wirtualnej Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
