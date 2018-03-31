---
title: Konwertuj maszyny wirtualnej systemu Linux na platformie Azure z dysków niezarządzanych do zarządzanych dysków - Azure zarządzanych dysków | Dokumentacja firmy Microsoft
description: Jak przekonwertować maszyny Wirtualnej systemu Linux z dysków niezarządzanych do zarządzanych dysków przy użyciu interfejsu wiersza polecenia Azure w wersji 2.0 w modelu wdrażania usługi Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.openlocfilehash: a3a2bbc15dd94ef09755d34a20e69c97854416b3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertuj maszyny wirtualnej systemu Linux z dysków niezarządzanych do zarządzanych dysków

Jeśli masz istniejące maszyn wirtualnych systemu Linux (VM) korzystające z niezarządzanego dysków, można przekonwertować maszyny wirtualne do użycia [dysków zarządzanych Azure](../linux/managed-disks-overview.md). Ten proces konwersji zarówno dysk systemu operacyjnego i wszystkich dysków dołączonych danych.

W tym artykule przedstawiono sposób konwertowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure. Jeśli musisz zainstalować lub uaktualnić go, zobacz [zainstalować Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przegląd [— często zadawane pytania dotyczące migracji do zarządzanych dysków](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Konwertuj maszyn wirtualnych z jednego wystąpienia
W tej sekcji omówiono sposób konwertowania maszyn wirtualnych Azure jednym wystąpieniu z dysków niezarządzanych do zarządzanych dysków. (W przypadku maszyn wirtualnych w zestawie dostępności, zobacz następną sekcję). Ten proces można użyć do konwersji maszyn wirtualnych z dyski dysków do dysków zarządzanych w warstwie premium lub standard (HDD) niezarządzanych premium (SSD) niezarządzanych do zarządzanych dyski standardowe.

1. Cofnięcie przydziału maszyny Wirtualnej za pomocą [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate). Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Konwertuj maszynę Wirtualną do zarządzanych dysków za pomocą [konwersji maszyny wirtualnej az](/cli/azure/vm#az_vm_convert). Następujący proces konwersji maszyny Wirtualnej o nazwie `myVM`, w tym dysku systemu operacyjnego i dysków z danymi:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Uruchom maszynę Wirtualną po konwersji do dysków zarządzanych za pomocą [uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_start). W następującym przykładzie uruchomiono maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konwertuj maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które ma zostać przekonwertowany na zarządzany dyski znajdują się w zestawie dostępności, należy najpierw przekonwertować zestaw z zestawem dostępności zarządzanego dostępności.

Wszystkie maszyny wirtualne w zestawie dostępności muszą przydzielenia przed dokonaniem konwersji zestawu dostępności. Planowanie można skonwertować wszystkich maszyn wirtualnych do zarządzanych dysków po sam zestaw dostępności został przekonwertowany na zestaw dostępności zarządzanego. Następnie uruchom wszystkie maszyny wirtualne i kontynuowania działania normalnego.

1. Wyświetl listę wszystkich maszyn wirtualnych w zestawie przy użyciu dostępności [listy zestawu dostępności maszyny wirtualnej az](/cli/azure/vm/availability-set#az_vm_availability_set_list). Poniższy przykład zawiera listę wszystkich maszyn wirtualnych w zestawie o nazwie dostępności `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Cofnięcie przydziału wszystkich maszyn wirtualnych za pomocą [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate). Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konwertuj zestawu za pomocą dostępności [przekonwertować zestawu dostępności maszyny wirtualnej az](/cli/azure/vm/availability-set#az_vm_availability_set_convert). Poniższy przykład konwertuje zestaw o nazwie dostępności `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konwertuj wszystkich maszyn wirtualnych do zarządzanych dysków za pomocą [konwersji maszyny wirtualnej az](/cli/azure/vm#az_vm_convert). Następujący proces konwersji maszyny Wirtualnej o nazwie `myVM`, w tym dysku systemu operacyjnego i dysków z danymi:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Uruchom wszystkie maszyny wirtualne po konwersji do dysków zarządzanych przy użyciu [uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_start). W następującym przykładzie uruchomiono maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat opcji magazynu, zobacz [omówienie dysków zarządzanych Azure](../windows/managed-disks-overview.md).
