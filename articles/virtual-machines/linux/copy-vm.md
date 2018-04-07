---
title: Kopiowanie maszyny Wirtualnej systemu Linux przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię maszyny Wirtualnej systemu Linux platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0 i dysków zarządzanych.
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 66f2789d717816f5be3fd8b298819825f8cd87f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Utworzenie kopii maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0 i dysków zarządzanych


W tym artykule pokazano, jak utworzyć kopię Azure maszyny wirtualnej (VM) systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0 i modelu wdrażania usługi Azure Resource Manager. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Możesz również [przekazywanie i tworzenie maszyny Wirtualnej z dysku VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne


-   Zainstaluj [Azure CLI 2.0](/cli/azure/install-az-cli2)

-   Zaloguj się do konta platformy Azure z [logowania az](/cli/azure/reference-index#az_login).

-   Ma maszyny Wirtualnej platformy Azure do użycia jako źródło dla tej kopii.

## <a name="step-1-stop-the-source-vm"></a>Krok 1: Zatrzymaj źródłowej maszyny Wirtualnej


Cofnięcie przydziału źródłowej maszyny Wirtualnej za pomocą [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate).
Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Krok 2: Kopiowanie źródłowej maszyny Wirtualnej


Aby skopiować Maszynę wirtualną, należy utworzyć kopię źródłowego wirtualnego dysku twardego. Ten proces tworzy specjalne wirtualnego dysku twardego jako zarządzane dysk zawierający taką samą konfigurację i ustawienia źródłowej maszyny Wirtualnej.

Aby uzyskać więcej informacji o dyskach Azure Managed Disks, zobacz [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Każda z maszyn wirtualnych oraz nazwę jego systemu operacyjnego na dysku z listy [listy wirtualna az](/cli/azure/vm#az_vm_list). Poniższy przykład zawiera listę wszystkich maszyn wirtualnych w grupie zasobów o nazwie **myResourceGroup**:
    
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

1.  Kopiowanie dysku przez utworzenie nowej zarządzane przy użyciu dysku [Tworzenie dysku az](/cli/azure/disk#az_disk_create). Poniższy przykład tworzy dysk o nazwie **myCopiedDisk** z zarządzanego dysku o nazwie **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Sprawdź dysków zarządzanych w grupie zasobów za pomocą [Lista dysków az](/cli/azure/disk#az_disk_list). Poniższy przykład znajduje się lista dysków zarządzanych w tej grupie zasobów o nazwie **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Krok 3: Konfigurowanie sieci wirtualnej


Poniższe kroki opcjonalne Utwórz nową sieć wirtualną, podsieci, publiczny adres IP i sieć wirtualna karta sieciowa (NIC).

Jeśli kopiujesz Maszynę wirtualną do rozwiązywania problemów z celów lub dodatkowe wdrożenia, nie można używać maszyny Wirtualnej w ramach istniejącej sieci wirtualnej.

Jeśli chcesz utworzyć infrastrukturę sieci wirtualnej dla skopiowanego maszyn wirtualnych, wykonaj następnych kilku krokach. Jeśli nie chcesz utworzyć sieć wirtualną, przejdź do [krok 4: tworzenie maszyny Wirtualnej](#step-4-create-a-vm).

1.  Utwórz sieć wirtualną przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie **myVnet** i podsieć o nazwie **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Tworzenie publicznego adresu IP za pomocą [utworzyć az sieci publicznej ip](/cli/azure/network/public-ip#az_network_public_ip_create). Poniższy przykład tworzy publicznego adresu IP o nazwie **myPublicIP** o nazwie DNS **mypublicdns**. (Nazwa DNS musi być unikatowa, więc Podaj unikatową nazwę.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Tworzenie przy użyciu kart [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create).
    Poniższy przykład tworzy karty Sieciowej o nazwie **myNic** dołączona do **mySubnet** podsieci:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Krok 4: Tworzenie maszyny Wirtualnej

Można teraz utworzyć Maszynę wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create).

Określ skopiowany dysk zarządzanych do użycia jako dysk systemu operacyjnego (--attach-os-disk) w następujący sposób:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak zarządzać nowej maszyny Wirtualnej za pomocą wiersza polecenia platformy Azure, zobacz [polecenia wiersza polecenia platformy Azure dla Menedżera zasobów Azure](../azure-cli-arm-commands.md).
