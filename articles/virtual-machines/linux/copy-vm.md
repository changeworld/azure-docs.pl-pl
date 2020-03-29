---
title: Kopiowanie maszyny Wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć kopię maszyny wirtualnej systemu Azure z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i dysków zarządzanych.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: ed8574133eafe751699e90ea8cae832ee649fb00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969599"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Tworzenie kopii maszyny Wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i dysków zarządzanych

W tym artykule pokazano, jak utworzyć kopię maszyny wirtualnej platformy Azure (VM) z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i modelu wdrażania usługi Azure Resource Manager. 

Maszynę wirtualną można również [przesłać i utworzyć z dysku VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne

-   Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2).

-   Zaloguj się do konta platformy Azure [przy logowaniu az](/cli/azure/reference-index#az-login).

-   Mieć maszynę wirtualną platformy Azure do użycia jako źródło kopii.

## <a name="stop-the-source-vm"></a>Zatrzymywać źródłowej maszynę wirtualną

Zdeokuj źródło maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
W poniższym przykładzie wiele osób przydziela maszynę wirtualną o nazwie *myVM* w grupie zasobów *myResourceGroup:*

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopiowanie źródłowej maszyny Wirtualnej

Aby skopiować maszynę wirtualną, należy utworzyć kopię podstawowego wirtualnego dysku twardego. Ten proces tworzy wyspecjalizowany wirtualny dysk twardy (VHD) jako dysk zarządzany, który zawiera taką samą konfigurację i ustawienia jak źródłowa maszyna wirtualna.

Aby uzyskać więcej informacji o dyskach funkcji Dyski zarządzane platformy Azure, zobacz [Omówienie funkcji Dyski zarządzane platformy Azure](../windows/managed-disks-overview.md). 

1.  Wyświetl listę każdej maszyny wirtualnej i nazwę dysku systemu operacyjnego z [listą az vm](/cli/azure/vm#az-vm-list). Poniższy przykład zawiera listę wszystkich maszyn wirtualnych w grupie zasobów o nazwie *myResourceGroup:*
    
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

1.  Skopiuj dysk, tworząc nowy dysk zarządzany i używając [programu az disk create](/cli/azure/disk#az-disk-create). Poniższy przykład tworzy dysk o nazwie *myCopiedDisk* z dysku zarządzanego o nazwie *myDisk:*

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Sprawdź dyski zarządzane teraz w grupie zasobów za pomocą [listy dysków az](/cli/azure/disk#az-disk-list). Poniższy przykład zawiera listę dysków zarządzanych w grupie zasobów o nazwie *myResourceGroup:*

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Poniższe kroki opcjonalne utworzyć nową sieć wirtualną, podsieci, publiczny adres IP i wirtualną kartę interfejsu sieciowego (NIC).

Jeśli kopiujesz maszynę wirtualną do celów rozwiązywania problemów lub dodatkowych wdrożeń, możesz nie chcieć używać maszyny Wirtualnej w istniejącej sieci wirtualnej.

Jeśli chcesz utworzyć infrastrukturę sieci wirtualnej dla skopiowanych maszyn wirtualnych, wykonaj kilka następnych kroków. Jeśli nie chcesz tworzyć sieci wirtualnej, przejdź do [programu Utwórz maszynę wirtualną](#create-a-vm).

1.  Utwórz sieć wirtualną przy użyciu [sieci wirtualnej az create](/cli/azure/network/vnet#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnet:*

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Tworzenie publicznego adresu IP przy użyciu [sieci az public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). Poniższy przykład tworzy publiczny adres IP o nazwie *myPublicIP* z nazwą DNS *mypublicdns*. (Ponieważ nazwa DNS musi być unikatowa, podaj unikatową nazwę).

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Utwórz kartę sieciową przy użyciu [sieciowej karty sieciowej az .](/cli/azure/network/nic#az-network-nic-create)
    Poniższy przykład tworzy kartę sieciową o nazwie *myNic,* która jest dołączona do podsieci *mySubnet:*

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu [az vm create](/cli/azure/vm#az-vm-create).

Określ skopiowany dysk zarządzany, który ma`--attach-os-disk`być używany jako dysk systemu operacyjnego ( ), w następujący sposób:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać obrazami maszyn wirtualnych za pomocą [galerii obrazów udostępnionych.](shared-images.md)
