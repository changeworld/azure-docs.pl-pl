---
title: Jak zmienić rozmiar maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Jak skalować w górę lub w dół maszynę wirtualną systemu Linux, zmieniając rozmiar maszyny Wirtualnej.
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
ms.openlocfilehash: 8bd8136c61a277e730c07c8789afd1bf3214bd7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127076"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Zmień rozmiar maszyny wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure 

Po zainicjowaniu maszynę wirtualną (VM) możesz skalować maszynę Wirtualną w górę lub w dół, zmieniając [rozmiar maszyny Wirtualnej][vm-sizes]. W niektórych przypadkach możesz najpierw cofnąć maszyny Wirtualnej. Należy cofnąć przydział maszyny Wirtualnej, jeśli żądany rozmiar jest niedostępny w klastrze sprzętu, który jest hostem maszyny Wirtualnej. W tym artykule opisano, jak zmienić rozmiar maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure. 

## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej
Aby zmienić rozmiar maszyny Wirtualnej, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

1. Wyświetl listę dostępnych rozmiarów maszyn wirtualnych w klastrze sprzętu, w którym maszyna wirtualna jest hostowana przy użyciu [listy maszyny wirtualnej az-vm-resize-options](/cli/azure/vm). Poniższy przykład wyświetla listę rozmiarów maszyn wirtualnych dla maszyny Wirtualnej o nazwie `myVM` w grupie zasobów `myResourceGroup` regionu:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Jeśli żądany rozmiar maszyny Wirtualnej jest wymieniony, zmienić rozmiar maszyny Wirtualnej przy użyciu [zmienić rozmiar maszyny wirtualnej az](/cli/azure/vm). Poniższy przykład zmienia rozmiar maszyny Wirtualnej o nazwie `myVM` do `Standard_DS3_v2` rozmiar:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Maszyna wirtualna zostanie uruchomiony ponownie w trakcie tego procesu. Po ponownym uruchomieniu są mapowane ponownie z istniejącego systemu operacyjnego i dysków z danymi. Elementy na dysku tymczasowym zostaną utracone.

3. Jeśli żądany rozmiar maszyny Wirtualnej nie ma na liście, musisz najpierw Cofnij Przydział maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm). Ten proces umożliwia maszynę Wirtualną, aby następnie można zmienić rozmiar do dowolnego rozmiaru, które są dostępne, że obsługuje regionu, a następnie jego uruchomienie. Poniższe kroki cofnięcie przydziału, rozmiar, a następnie uruchom maszynę Wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Cofanie przydziału maszyny Wirtualnej również zwalnia dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie dotyczy systemu operacyjnego i dysków z danymi.

## <a name="next-steps"></a>Kolejne kroki
Dla uzyskania dodatkowej skalowalności uruchomić wiele wystąpień maszyny Wirtualnej, a następnie skalować w poziomie. Aby uzyskać więcej informacji, zobacz [automatyczne skalowanie maszyn z systemem Linux w zestawie skalowania maszyn wirtualnych][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
