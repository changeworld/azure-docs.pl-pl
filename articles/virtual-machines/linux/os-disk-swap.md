---
title: Dysk wymiany systemu operacyjnego dla maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 1732b60ee135b765cdeea43f981bcef9575ff32c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196210"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Zmień dysk systemu operacyjnego używany przez maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia


Jeśli masz istniejącą maszynę Wirtualną, ale chcesz wymienić dysk kopii zapasowej dysku lub innego dysku systemu operacyjnego, można użyć interfejsu wiersza polecenia Azure, można zamienić na dyskach systemu operacyjnego. Nie masz usunięcie i ponowne utworzenie maszyny Wirtualnej. Nawet służy dysków zarządzanych w innej grupie zasobów, jak długo nie znajduje się już w użyciu.

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu zarządzanego dysku można zastąpić identyfikator zasobu zarządzanego inny dysk. 

Upewnij się, że typu rozmiaru i magazynu maszyny Wirtualnej są zgodne z dysku, który chcesz dołączyć. Na przykład jeśli dysk, który ma być używany jest magazyn w warstwie Premium, następnie maszyny Wirtualnej musi być w stanie magazyn w warstwie Premium (takich jak rozmiar z serii DS).

W tym artykule wymaga wiersza polecenia platformy Azure w wersji 2.0.25 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 


Użyj [Lista dysków az](/cli/azure/disk#list) spowoduje wyświetlenie listy dysków w grupie zasobów.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Użyj [zatrzymania maszyny wirtualnej az](/cli/azure/vm#stop) do stop\deallocate przed wymiany dyski maszyny Wirtualnej.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Użyj [aktualizację maszyny wirtualnej az](/cli/azure/vm#az-vm-update) z pełny identyfikator zasobu dla nowego dysku `--osdisk` parametru 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Uruchom ponownie maszynę Wirtualną przy użyciu [uruchomienia maszyny wirtualnej az](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Następne kroki**

Aby utworzyć kopię dysku, zobacz [migawki dysku](snapshot-copy-managed-disk.md).