---
title: Zamień dysk systemu operacyjnego na maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia "
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: bfbe47fb68ffe7cee1ee2f9f7b94b418d8da2a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035329"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Zmienianie dysku systemu operacyjnego używanego przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia


Jeśli masz istniejącą maszynę wirtualną, ale chcesz zamienić dysk na dysk kopii zapasowej lub inny dysk systemu operacyjnego, możesz użyć interfejsu wiersza polecenia platformy Azure do wymiany dysków systemu operacyjnego. Nie trzeba usuwać i ponownie utworzyć maszynę wirtualną. Można nawet użyć dysku zarządzanego w innej grupie zasobów, o ile nie jest on jeszcze używany.

Maszyna wirtualna musi zostać zatrzymana\cofnięto alokację, a następnie identyfikator zasobu dysku zarządzanego można zastąpić identyfikatorem zasobu innego dysku zarządzanego. 

Upewnij się, że rozmiar maszyny Wirtualnej i typ magazynu są zgodne z dyskiem, który chcesz dołączyć. Na przykład jeśli dysk, którego chcesz użyć, znajduje się w magazynie w wersji Premium, maszyna wirtualna musi być zdolna do magazynu w wersji Premium (na przykład rozmiaru serii DS).

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.25 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


Użyj [listy dysków AZ,](/cli/azure/disk) aby uzyskać listę dysków w grupie zasobów.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Użyj [az vm stop,](/cli/azure/vm) aby zatrzymać\deallocate maszyny Wirtualnej przed zamianą dysków.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Użyj [aktualizacji az vm](/cli/azure/vm#az-vm-update) z pełnym identyfikatorem zasobu nowego dysku dla parametru `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Uruchom ponownie maszynę wirtualną przy użyciu [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Następne kroki**

Aby utworzyć kopię dysku, zobacz [Migawka dysku](snapshot-copy-managed-disk.md).
