---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — instalowanie dysku systemu operacyjnego
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — instalowanie dysku systemu operacyjnego
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037589"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Rozwiązywanie problemów z dyskiem systemu operacyjnego maszyn wirtualnych

Ten skrypt instaluje dysk systemu operacyjnego maszyny wirtualnej z błędami lub problemami jako dysk z danymi na drugiej maszynie wirtualnej. Może to być przydatne podczas rozwiązywania problemów z dyskiem lub odzyskiwania danych.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Wyświetla listę maszyn wirtualnych. W takim przypadku opcja zapytania służy do zwracania dysku systemu operacyjnego maszyny wirtualnej. Ta wartość jest następnie dodawana do nazwy zmiennej „uri”. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Usuwa maszynę wirtualną. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Tworzy maszynę wirtualną.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Dołącza dysk do maszyny wirtualnej. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Zwraca adresy IP maszyny wirtualnej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
