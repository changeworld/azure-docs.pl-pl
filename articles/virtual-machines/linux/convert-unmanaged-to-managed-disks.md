---
title: Konwertowanie maszyny wirtualnej z systemem Linux na platformie Azure z dysków niezarządzanych do usługi managed disks - usługi Azure Managed Disks | Dokumentacja firmy Microsoft
description: Sposób konwertowania maszyny Wirtualnej z systemem Linux z dysków niezarządzanych do dysków zarządzanych przy użyciu wiersza polecenia platformy Azure w modelu wdrażania usługi Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
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
ms.subservice: disks
ms.openlocfilehash: d1783890ba1b304ab658e827bfd24091e208fda4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699254"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertuj maszynę wirtualną z systemem Linux z dysków niezarządzanych do usługi managed disks

W przypadku istniejących maszyn wirtualnych systemu Linux (VM) korzystające z niezarządzanych dysków można konwertować maszyn wirtualnych do użycia [usługi Azure Managed Disks](../linux/managed-disks-overview.md). Ten proces konwertuje dysk systemu operacyjnego i wszelkich dołączonych dysków danych.

W tym artykule przedstawiono sposób konwertowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure. Jeśli musisz zainstalować lub uaktualnić go, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przegląd [— często zadawane pytania dotyczące migracji do usługi Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Konwertowanie maszyn wirtualnych z jednego wystąpienia
W tej sekcji opisano sposób konwertowania maszyn wirtualnych platformy Azure z jednego wystąpienia z dysków niezarządzanych do dysków zarządzanych. (W przypadku maszyn wirtualnych w zestawie dostępności, zobacz następną sekcję). Ten proces można użyć do konwersji maszyny wirtualne z dysków w warstwie premium (SSD), niezarządzanych dysków na dyski zarządzane w warstwie premium lub standardowa (HDD), niezarządzanych do dysków zarządzanych w warstwie standardowa.

1. Cofnij Przydział maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Konwertowanie maszyny Wirtualnej do usługi managed disks przy użyciu [przekonwertować az vm](/cli/azure/vm). Następujący proces konwersji maszyny Wirtualnej o nazwie `myVM`, w tym dysk systemu operacyjnego i dysków z danymi:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Uruchom maszynę Wirtualną po konwersji do dysków zarządzanych przy użyciu [az vm start](/cli/azure/vm). Poniższy przykład uruchamia maszynę Wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konwertowanie maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które chcesz przekonwertować zarządzane dyski znajdują się w zestawie dostępności, należy najpierw przekonwertować zestawie dostępności do zarządzanego zestawu dostępności.

Przed dokonaniem konwersji zestawu dostępności, należy cofnąć przydział wszystkich maszyn wirtualnych w zestawie dostępności. Należy zaplanować i konwersję wszystkich maszyn wirtualnych do usługi managed disks, po samego zestawu dostępności został przekonwertowany na zarządzany zestaw dostępności. Następnie uruchom wszystkie maszyny wirtualne i kontynuowania działania w zwykły sposób.

1. Lista wszystkich maszyn wirtualnych w zestawie przy użyciu dostępności [listy zestaw dostępności maszyn wirtualnych az](/cli/azure/vm/availability-set). Poniższy przykład wyświetla listę wszystkich maszyn wirtualnych w zestawie o nazwie dostępności `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Cofnij Przydział wszystkich maszyn wirtualnych przy użyciu [az vm deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konwertowanie zestawu za pomocą dostępności [konwersji zestawu dostępności maszyny wirtualnej az](/cli/azure/vm/availability-set). Poniższy przykład konwertuje zestawu dostępności o nazwie `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konwertuj wszystkich maszyn wirtualnych do usługi managed disks przy użyciu [przekonwertować az vm](/cli/azure/vm). Następujący proces konwersji maszyny Wirtualnej o nazwie `myVM`, w tym dysk systemu operacyjnego i dysków z danymi:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Uruchom wszystkie maszyny wirtualne po konwersji do dysków zarządzanych przy użyciu [az vm start](/cli/azure/vm). Poniższy przykład uruchamia maszynę Wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat opcji magazynu, zobacz [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md).
