---
title: Kopiowanie maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię maszyny Wirtualnej systemu Linux platformy Azure przy użyciu wiersza polecenia platformy Azure i dyski Managed Disks.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: abc8c09a51104c81b827afb7055531df98691714
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731348"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Utwórz kopię maszyny Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure i dyski Managed Disks

W tym artykule pokazano, jak utworzyć kopię sieci maszyn wirtualnych (VM) z systemem Linux przy użyciu wiersza polecenia platformy Azure i modelu wdrażania usługi Azure Resource Manager. 

Możesz również [przekazywanie i tworzenie maszyny Wirtualnej na podstawie wirtualnego dysku twardego](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne

-   Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2).

-   Zaloguj się do konta platformy Azure za pomocą [az login](/cli/azure/reference-index#az-login).

-   Masz Maszynę wirtualną platformy Azure do użycia jako źródła kopii.

## <a name="stop-the-source-vm"></a>Zatrzymaj źródłowej maszyny Wirtualnej

Cofnij Przydział źródłowej maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Skopiuj źródłowej maszyny Wirtualnej

Aby skopiować Maszynę wirtualną, należy utworzyć kopię podstawowego wirtualnego dysku twardego. Ten proces tworzy wyspecjalizowanego wirtualnego dysku twardego (VHD) jako dysk zarządzany, który zawiera tę samą konfigurację oraz ustawienia co źródłowa maszyna wirtualna.

Aby uzyskać więcej informacji o dyskach funkcji Dyski zarządzane platformy Azure, zobacz [Omówienie funkcji Dyski zarządzane platformy Azure](../windows/managed-disks-overview.md). 

1.  Listy każdej maszyny Wirtualnej i nazwę jej systemu operacyjnego na dysku z [az vm list](/cli/azure/vm#az-vm-list). Poniższy przykład wyświetla listę wszystkich maszyn wirtualnych w grupie zasobów o nazwie *myResourceGroup*:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Skopiuj dysk przez utworzenie nowego dysku zarządzanego i za pomocą [tworzenia dysku az](/cli/azure/disk#az-disk-create). W poniższym przykładzie tworzony jest dysk o nazwie *myCopiedDisk* z dysków zarządzanych, o nazwie *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Sprawdź dysków zarządzanych w grupie zasobów przy użyciu [listy dysków az](/cli/azure/disk#az-disk-list). Poniższy przykład wyświetla listę dysków zarządzanych w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Następujące opcjonalne czynności tworzenia nowej sieci wirtualnej, podsieci, publiczny adres IP i karty sieci wirtualnej (NIC).

Jeśli kopiujesz maszyny Wirtualnej rozwiązywania problemów z celów lub dodatkowe wdrożenia, możesz nie chcieć za pomocą maszyny Wirtualnej w istniejącej sieci wirtualnej.

Jeśli chcesz utworzyć infrastrukturę sieci wirtualnej dla maszyn wirtualnych skopiowany wykonaj kilka następnych kroków. Jeśli nie chcesz utworzyć sieć wirtualną, przejdź do [Utwórz Maszynę wirtualną](#create-a-vm).

1.  Utwórz sieć wirtualną przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Tworzenie publicznego adresu IP za pomocą [tworzenie sieci az public-ip](/cli/azure/network/public-ip#az-network-public-ip-create). Poniższy przykład tworzy publiczny adres IP o nazwie *myPublicIP* nazwą DNS *mypublicdns*. (Ponieważ nazwa DNS musi być unikatowa, Podaj unikatową nazwę).

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Tworzenie karty Sieciowej przy użyciu [tworzenie az sieciowego](/cli/azure/network/nic#az-network-nic-create).
    Poniższy przykład tworzy kartę Sieciową o nazwie *myNic* dołączona do *mySubnet* podsieci:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny Wirtualnej przy użyciu [tworzenie az vm](/cli/azure/vm#az-vm-create).

Określ skopiowany dysk zarządzany do użycia jako dysk systemu operacyjnego (`--attach-os-disk`), wykonując następujące czynności:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak zarządzać swojej nowej maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure, zobacz [poleceń interfejsu wiersza polecenia platformy Azure Resource Manager](../azure-cli-arm-commands.md).
