---
title: Dysk wymiany systemu operacyjnego na Maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: b17647a09c88491e2486046b1ca99ee277f0cc28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473896"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Zmień dysk systemu operacyjnego używany przez Maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia


Jeśli masz istniejącą maszynę Wirtualną, ale chcesz zamienić na dysku dla dysku kopii zapasowej lub inny dysk systemu operacyjnego, można użyć wiersza polecenia platformy Azure o zamianę dysków systemu operacyjnego. Nie masz usunięcie i ponowne utworzenie maszyny Wirtualnej. Nawet służy dysku zarządzanego w innej grupie zasobów, tak długo, jak go nie jest już używana.

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu dysku zarządzanego można zastąpić o identyfikatorze zasobu z innego dysku zarządzanego. 

Upewnij się, że typ rozmiaru i magazynu maszyny Wirtualnej są zgodne z dysku, który chcesz dołączyć. Na przykład w przypadku dysku, którego chcesz użyć w usłudze Premium Storage, maszyny Wirtualnej musi być w stanie magazynu w warstwie Premium (na przykład rozmiar serii DS).

Ten artykuł wymaga interfejsu wiersza polecenia Azure w wersji 2.0.25 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


Użyj [listy dysków az](/cli/azure/disk) w celu uzyskania listy dysków w grupie zasobów.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Użyj [az vm stop](/cli/azure/vm) do stop\deallocate maszyny Wirtualnej przed zamianą na dyskach.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Użyj [az vm update](/cli/azure/vm#az-vm-update) z pełny identyfikator zasobu nowego dysku dla `--osdisk` parametru 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Uruchom ponownie maszynę Wirtualną przy użyciu [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Następne kroki**

Aby utworzyć kopię dysku, zobacz [Tworzenie migawki dysku](snapshot-copy-managed-disk.md).
