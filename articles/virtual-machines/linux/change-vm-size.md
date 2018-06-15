---
title: Jak zmienić rozmiar maszyny Wirtualnej systemu Linux, Azure CLI 2.0 | Dokumentacja firmy Microsoft
description: Jak skalowanie w górę i skalowania maszynę wirtualną systemu Linux przez zmianę rozmiaru maszyny Wirtualnej.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5c396ad047567799b7541c1a125de0c1c51c39e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185998"
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Zmień rozmiar maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia 2.0

Po udostępnić maszynę wirtualną (VM), można skalować maszyny Wirtualnej w górę lub w dół, zmieniając [rozmiar maszyny Wirtualnej][vm-sizes]. W niektórych przypadkach należy najpierw cofnąć maszyny Wirtualnej. Musisz cofnąć maszyny Wirtualnej, jeśli wymagany rozmiar jest niedostępny w klastrze sprzętu, który jest hostem maszyny Wirtualnej. W tym artykule szczegółowo sposób zmiany rozmiaru maszyny Wirtualnej systemu Linux, 2.0 interfejsu wiersza polecenia platformy Azure. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej
Aby zmienić rozmiar maszyny Wirtualnej, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

1. Wyświetl listę dostępnych rozmiarów maszyny Wirtualnej w klastrze sprzętu, gdzie jest hostowana maszyna wirtualna z [az maszyny wirtualnej-vm — zmiany rozmiaru — opcje listy](/cli/azure/vm#az_vm_list_vm_resize_options). Poniższy przykład zawiera rozmiarów maszyn wirtualnych dla maszyny Wirtualnej o nazwie `myVM` w grupie zasobów `myResourceGroup` regionu:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Jeśli żądany rozmiar maszyny Wirtualnej jest wyświetlana, Zmień rozmiar maszyny Wirtualnej z [Zmień rozmiar maszyny wirtualnej az](/cli/azure/vm#az_vm_resize). Poniższy przykład powoduje zmianę rozmiaru maszyny Wirtualnej o nazwie `myVM` do `Standard_DS3_v2` rozmiar:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Maszyna wirtualna zostanie uruchomiony ponownie w trakcie tego procesu. Po ponownym uruchomieniu są mapowane ponownie z istniejącego systemu operacyjnego i dysków z danymi. Elementy na dysku tymczasowym zostaną utracone.

3. Jeśli żądany rozmiar maszyny Wirtualnej nie ma na liście, należy najpierw cofnąć maszyny Wirtualnej z [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate). Ten proces umożliwia maszyna wirtualna może następnie być powiększony dostępny rozmiar, że obsługuje region, a następnie uruchomiona. Poniższe kroki cofnięcie przydziału, Zmień rozmiar, a następnie uruchom maszynę Wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Cofanie przydziału maszyny Wirtualnej zwalnia również dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie dotyczy systemu operacyjnego i dysków z danymi.

## <a name="next-steps"></a>Kolejne kroki
Dodatkowe możliwości skalowania uruchamianie wielu wystąpień maszyny Wirtualnej i skalowanie w poziomie. Aby uzyskać więcej informacji, zobacz [automatycznie skalować w zestawie skalowania maszyn wirtualnych systemu Linux maszyny][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
