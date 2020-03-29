---
title: Jak zmienić rozmiar maszyny Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure
description: Jak skalować w górę lub skalować w dół maszyny wirtualnej systemu Linux, zmieniając rozmiar maszyny wirtualnej.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969275"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 

Po aprowizowania maszyny wirtualnej (VM) można skalować maszynę wirtualną w górę lub w dół, zmieniając [rozmiar maszyny wirtualnej][vm-sizes]. W niektórych przypadkach należy najpierw zdelocytować alokację maszyny Wirtualnej. Należy zdelocytować maszynę wirtualną, jeśli żądany rozmiar nie jest dostępny w klastrze sprzętowym, który hostuje maszynę wirtualną. W tym artykule opisano, jak zmienić rozmiar maszyny Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure. 

## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej
Aby zmienić rozmiar maszyny Wirtualnej, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

1. Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych w klastrze sprzętowym, w którym maszyna wirtualna jest hostowana przy za pomocą [opcji az vm list-vm-resize.View](/cli/azure/vm)the list of available VM sizes on the hardware cluster where the VM is hosted with az vm list-vm-resize-options . Poniższy przykład zawiera listę rozmiarów maszyn `myVM` wirtualnych `myResourceGroup` dla maszyny Wirtualnej o nazwie w regionie grupy zasobów:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Jeśli na liście znajduje się żądany rozmiar maszyny Wirtualnej, zmienić rozmiar maszyny Wirtualnej z [az vm resize](/cli/azure/vm). Poniższy przykład ma rozmiar maszyny `myVM` Wirtualnej `Standard_DS3_v2` o nazwie do rozmiaru:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Maszyna wirtualna uruchamia się ponownie podczas tego procesu. Po ponownym uruchomieniu istniejący system operacyjny i dyski z danymi zostaną ponownie zamapowane. Wszystko na dysku tymczasowym zostanie utracone.

3. Jeśli żądany rozmiar maszyny Wirtualnej nie jest wymieniony, należy najpierw zdeterminować maszynę wirtualną za pomocą [az vm deallocate](/cli/azure/vm). Ten proces umożliwia maszynę wirtualną, aby następnie zmieścić rozmiar do dowolnego rozmiaru dostępnego regionu, a następnie uruchomione. Następujące kroki obejmują alokację, ponowne rozmiary, a `myVM` następnie uruchomienie maszyny `myResourceGroup`wirtualnej o nazwie w grupie zasobów o nazwie:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Rozdzielanie maszyny Wirtualnej zwalnia również wszystkie dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie ma to wpływu na system operacyjny i dyski danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkową skalowalność, uruchom wiele wystąpień maszyn wirtualnych i skaluj w poziomie. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie maszyn systemu Linux w zestawie skalowania maszyny wirtualnej][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
