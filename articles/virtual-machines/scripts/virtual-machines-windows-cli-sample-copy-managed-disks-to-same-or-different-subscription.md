---
title: Kopiowanie dysków zarządzanych do subskrypcji — przykładowy wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji
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
ms.openlocfilehash: e31712e9010fa23fb2af2d9b0a3253e226971c51
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463632"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopiowanie dysków zarządzanych do tej samej lub innej subskrypcji za pomocą interfejsu wiersza polecenia

Ten skrypt kopiuje dysk zarządzany do tej samej lub innej subskrypcji w obrębie jednego regionu. Kopiowanie działa tylko wtedy, gdy subskrypcje należą do tej samej dzierżawy usługi AAD.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego dysku zarządzanego w subskrypcji docelowej przy użyciu identyfikatora źródłowego dysku zarządzanego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Pobiera wszystkie właściwości dysku zarządzanego przy użyciu nazwy i właściwości grupy zasobów dysku zarządzanego. Do kopiowania dysku zarządzanego do innej subskrypcji jest używana właściwość Id.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Kopiuje dysk zarządzany przez utworzenie nowego dysku zarządzanego w innej subskrypcji przy użyciu identyfikatora i nazwy nadrzędnego dysku zarządzanego.  |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia maszyny wirtualnej i dysków zarządzanych można znaleźć w [dokumentacji maszyny wirtualnej systemu Azure systemu Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
