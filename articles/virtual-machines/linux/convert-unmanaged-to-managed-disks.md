---
title: Konwertowanie maszyny Wirtualnej z systemu Linux z dysków niezarządzanych na dyski zarządzane
description: Jak przekonwertować maszynę wirtualną systemu Linux z dysków niezarządzanych na dyski zarządzane przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969695"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertowanie maszyny wirtualnej systemu Linux z dysków niezarządzanych na dyski zarządzane

Jeśli masz istniejące maszyny wirtualne systemu Linux (VM), które używają dysków niezarządzanych, możesz przekonwertować maszyny wirtualne na [dyski zarządzane platformy Azure](../linux/managed-disks-overview.md). Ten proces konwertuje zarówno dysk systemu operacyjnego, jak i wszystkie dołączone dyski danych.

W tym artykule pokazano, jak konwertować maszyny wirtualne przy użyciu interfejsu wiersza polecenia platformy Azure. Jeśli chcesz go zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [często zadawane pytania dotyczące migracji do dysków zarządzanych](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Oryginalne wirtualne dyski twarde i konto magazynu używane przez maszynę wirtualną przed konwersją nie są usuwane. Nadal będą za nie naliczane opłaty. Aby uniknąć naliczania opłat za te artefakty, usuń oryginalne obiekty blob dysków VHD po upewnieniu się, że konwersja została zakończona. Jeśli chcesz znaleźć te dyski nieprzyłączone, aby je usunąć, zobacz nasz artykuł [Znajdowanie i usuwanie nieprzyłączonych dysków zarządzanych i niezarządzanych platformy Azure.](find-unattached-disks.md)

## <a name="convert-single-instance-vms"></a>Konwertowanie maszyn wirtualnych z pojedynczym wystąpieniem
W tej sekcji opisano sposób konwertowania maszyn wirtualnych platformy Azure z dysków niezarządzanych z dysków niezarządzanych na dyski zarządzane. (Jeśli maszyny wirtualne znajdują się w zestawie dostępności, zobacz następną sekcję). Za pomocą tego procesu można przekonwertować maszyny wirtualne z dysków niezarządzanych w ramach premium (SSD) na dyski zarządzane w wersji premium lub z dysków niezarządzanych (HDD) na standardowe dyski zarządzane.

1. Zdeokuj maszynę wirtualną za pomocą [az vm deallocate](/cli/azure/vm). Poniższy przykład przydziela maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie: `myResourceGroup`

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Konwertuj maszynę wirtualną na dyski zarządzane za pomocą [konwersji maszyn wirtualnych az](/cli/azure/vm). Następujący proces konwertuje maszynę wirtualną o nazwie `myVM`, w tym dysk systemu operacyjnego i dyski z danymi:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Uruchom maszynę wirtualną po konwersji na dyski zarządzane przy użyciu [az vm start](/cli/azure/vm). Poniższy przykład rozpoczyna maszynę `myVM` wirtualną o `myResourceGroup`nazwie w grupie zasobów o nazwie .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konwertowanie maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które chcesz przekonwertować na dyski zarządzane, są ustawione w zestawie dostępności, należy najpierw przekonwertować zestaw dostępności na zestaw dostępności zarządzanej.

Wszystkie maszyny wirtualne w zestawie dostępności muszą zostać cofnięte przed przekonwertowanie zestawu dostępności. Planowanie konwersji wszystkich maszyn wirtualnych na dyski zarządzane po przekonwertowaniu samego zestawu dostępności na zestaw dostępności zarządzanej. Następnie uruchom wszystkie maszyny wirtualne i kontynuuj normalną pracę.

1. Lista wszystkich maszyn wirtualnych w zestawie dostępności przy użyciu [listy az vm availability-set](/cli/azure/vm/availability-set). Poniższy przykład zawiera listę wszystkich maszyn wirtualnych w zestawie dostępności o nazwie `myAvailabilitySet` w grupie zasobów o nazwie: `myResourceGroup`

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Zdeokuj wszystkie maszyny wirtualne za pomocą [az vm deallocate](/cli/azure/vm). Poniższy przykład przydziela maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie: `myResourceGroup`

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konwertuj zestaw dostępności przy użyciu [az vm dostępności zestaw konwertu](/cli/azure/vm/availability-set). Poniższy przykład konwertuje zestaw `myAvailabilitySet` dostępności nazwany `myResourceGroup`w grupie zasobów o nazwie:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konwertuj wszystkie maszyny wirtualne na dyski zarządzane za pomocą [konwersji maszyn wirtualnych az](/cli/azure/vm). Następujący proces konwertuje maszynę wirtualną o nazwie `myVM`, w tym dysk systemu operacyjnego i dyski z danymi:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Uruchom wszystkie maszyny wirtualne po konwersji na dyski zarządzane przy użyciu [az vm start](/cli/azure/vm). Poniższy przykład rozpoczyna maszynę `myVM` wirtualną o `myResourceGroup`nazwie w grupie zasobów o nazwie:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konwertowanie przy użyciu portalu Azure

Można również przekonwertować dyski niezarządzane na dyski zarządzane za pomocą witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz maszynę wirtualną z listy maszyn wirtualnych w portalu.
3. W bloku maszyny Wirtualnej wybierz opcję **Dyski** z menu.
4. U góry bloku **Dyski** wybierz pozycję **Migruj do dysków zarządzanych**.
5. Jeśli maszyna wirtualna jest w zestawie dostępności, będzie ostrzeżenie na **migracji do dysków zarządzanych** bloku, które należy najpierw przekonwertować zestaw dostępności. Ostrzeżenie powinno zawierać łącze, które można kliknąć, aby przekonwertować zestaw dostępności. Po przekonwertowaniu zestawu dostępności lub jeśli maszyna wirtualna nie jest w zestawie dostępności, kliknij przycisk **Migracja,** aby rozpocząć proces migracji dysków na dyski zarządzane.

Maszyna wirtualna zostanie zatrzymana i ponownie uruchomiona po zakończeniu migracji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji magazynowania, zobacz [Omówienie omówienie dysków zarządzanych platformy Azure](../windows/managed-disks-overview.md).
