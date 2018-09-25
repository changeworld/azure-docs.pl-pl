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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 64b33fcd25582f6b1d3e7efe12aba85bb17c4cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951206"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Utwórz kopię maszyny Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure i dyski Managed Disks

W tym artykule pokazano, jak utworzyć kopię sieci maszyn wirtualnych (VM) z systemem Linux przy użyciu wiersza polecenia platformy Azure i modelu wdrażania usługi Azure Resource Manager. 

Możesz również [przekazywanie i tworzenie maszyny Wirtualnej na podstawie wirtualnego dysku twardego](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne


-   Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2)

-   Zaloguj się do konta platformy Azure za pomocą [az login](/cli/azure/reference-index#az_login).

-   Masz Maszynę wirtualną platformy Azure do użycia jako źródła kopii.

## <a name="step-1-stop-the-source-vm"></a>Krok 1: Zatrzymaj źródłowej maszyny Wirtualnej


Cofnij Przydział źródłowej maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Krok 2. kopiowanie źródłowej maszyny Wirtualnej


Aby skopiować Maszynę wirtualną, należy utworzyć kopię podstawowego wirtualnego dysku twardego. Ten proces tworzy wyspecjalizowanego wirtualnego dysku twardego jako dysk zarządzany, który zawiera tę samą konfigurację oraz ustawienia co źródłowa maszyna wirtualna.

Aby uzyskać więcej informacji o dyskach Azure Managed Disks, zobacz [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Listy każdej maszyny Wirtualnej i nazwę jej systemu operacyjnego na dysku z [az vm list](/cli/azure/vm#az_vm_list). Poniższy przykład wyświetla listę wszystkich maszyn wirtualnych w grupie zasobów o nazwie **myResourceGroup**:
    
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

1.  Kopiowanie dysku przez utworzenie nowego zarządzanych, dysk przy użyciu [tworzenia dysku az](/cli/azure/disk#az_disk_create). W poniższym przykładzie tworzony jest dysk o nazwie **myCopiedDisk** z dysków zarządzanych, o nazwie **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Sprawdź dysków zarządzanych w grupie zasobów przy użyciu [listy dysków az](/cli/azure/disk#az_disk_list). Poniższy przykład wyświetla listę dysków zarządzanych w grupie zasobów o nazwie **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Krok 3: Konfigurowanie sieci wirtualnej


Następujące opcjonalne czynności tworzenia nowej sieci wirtualnej, podsieci, publiczny adres IP i karty sieci wirtualnej (NIC).

Jeśli kopiujesz maszyny Wirtualnej rozwiązywania problemów z celów lub dodatkowe wdrożenia, możesz nie chcieć za pomocą maszyny Wirtualnej w istniejącej sieci wirtualnej.

Jeśli chcesz utworzyć infrastrukturę sieci wirtualnej dla maszyn wirtualnych skopiowany wykonaj kilka następnych kroków. Jeśli nie chcesz utworzyć sieć wirtualną, przejdź do [krok 4: tworzenie maszyny Wirtualnej](#step-4-create-a-vm).

1.  Utwórz sieć wirtualną przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie **myVnet** i podsieć o nazwie **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Tworzenie publicznego adresu IP za pomocą [tworzenie sieci az public-ip](/cli/azure/network/public-ip#az_network_public_ip_create). Poniższy przykład tworzy publiczny adres IP o nazwie **myPublicIP** nazwą DNS **mypublicdns**. (Nazwa DNS muszą być unikatowe, dlatego Podaj unikatową nazwę.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Utwórz kartę Sieciową, za pomocą [tworzenie az sieciowego](/cli/azure/network/nic#az_network_nic_create).
    Poniższy przykład tworzy kartę Sieciową o nazwie **myNic** dołączona do **mySubnet** podsieci:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Krok 4: Tworzenie maszyny Wirtualnej

Teraz można utworzyć Maszynę wirtualną przy użyciu [tworzenie az vm](/cli/azure/vm#az_vm_create).

Określ skopiowany dysk zarządzany do użycia jako dysk systemu operacyjnego (--dołączyć os-disk), wykonując następujące czynności:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak zarządzać swojej nowej maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure, zobacz [poleceń interfejsu wiersza polecenia platformy Azure Resource Manager](../azure-cli-arm-commands.md).
