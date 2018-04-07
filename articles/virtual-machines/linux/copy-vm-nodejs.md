---
title: Utworzenie kopii maszyny Wirtualnej systemu Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię maszyny wirtualnej systemu Linux platformy Azure z 1.0 interfejsu wiersza polecenia platformy Azure w modelu wdrażania usługi Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: bb350f8d14ad451ad3ff7cd617ca3f90967aaa4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Utworzenie kopii maszyny wirtualnej systemu Linux działających na platformie Azure z interfejsu wiersza polecenia platformy Azure w wersji 1.0
W tym artykule pokazano, jak utworzyć kopię Azure maszyny wirtualnej (VM) systemem Linux przy użyciu modelu wdrażania Menedżera zasobów. Najpierw skopiowaniu za pośrednictwem systemu operacyjnego i dysków z danymi do nowego kontenera, a następnie skonfigurować zasoby sieciowe i tworzenie nowej maszyny wirtualnej.

Możesz również [przekazywanie i tworzenie maszyny Wirtualnej z obrazu niestandardowego dysku](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- Interfejs wiersza polecenia platformy Azure w wersji 1.0 — nasz interfejs wiersza polecenia dla klasycznego modelu wdrażania i modelu wdrażania na potrzeby zarządzania zasobami (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami

## <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, spełnia następujące wymagania wstępne, przed rozpoczęciem kroków:

* Masz [interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) pobrane i zainstalowane na tym komputerze. 
* Należy również pewne informacje o istniejącej maszyny Wirtualnej systemu Linux platformy Azure:

| Informacje o źródle maszyny Wirtualnej | Skąd uzyskać |
| --- | --- |
| Nazwa maszyny wirtualnej |`azure vm list` |
| Nazwa grupy zasobów |`azure vm list` |
| Lokalizacja |`azure vm list` |
| Nazwa konta magazynu |`azure storage account list -g <resourceGroup>` |
| Nazwa kontenera |`azure storage container list -a <sourcestorageaccountname>` |
| Nazwa pliku wirtualnego dysku twardego maszyny Wirtualnej źródłowego |`azure storage blob list --container <containerName>` |

* Należy zmienić kilka opcji dotyczących nowej maszyny Wirtualnej:    <br> -Nazwa kontenera    <br> -Nazwa maszyny Wirtualnej    <br> Rozmiar maszyny Wirtualnej —    <br> Nazwa sieci wirtualnej-    <br> -Nazwa podsieci    <br> Nazwa - IP    <br> Nazwa - NIC

## <a name="login-and-set-your-subscription"></a>Logowania i ustaw swoją subskrypcję
1. Zaloguj się do interfejsu wiersza polecenia.

    ```azurecli
    azure login
    ```
2. Upewnij się, że jesteś w trybie Menedżera zasobów.

    ```azurecli
    azure config mode arm
    ```
3. Ustaw poprawną subskrypcję. "Lista konto platformy azure" można użyć do wyświetlenia wszystkich subskrypcji.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej
Zatrzymaj i cofnięcia przydzielenia źródłowej maszyny Wirtualnej. "Lista maszyna wirtualna platformy azure" można użyć w celu uzyskania listy wszystkich maszyn wirtualnych w Twojej subskrypcji i zasobu ich nazwy grup.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Skopiuj wirtualny dysk twardy
Możesz skopiować wirtualny dysk twardy z magazynu źródłowego do docelowego przy użyciu `azure storage blob copy start`. W tym przykładzie zamierzamy Skopiuj wirtualny dysk twardy do tego samego konta magazynu, ale różnych kontenerów.

Aby skopiować dysk VHD do innego kontenera, w tym samym koncie magazynu, wpisz:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Konfigurowanie sieci wirtualnej dla nowej maszyny Wirtualnej
Konfigurowanie sieci wirtualnej i karty Sieciowej dla nowej maszyny Wirtualnej. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Tworzenie nowej maszyny Wirtualnej
Teraz można utworzyć maszyny Wirtualnej z dysku wirtualnego przekazane [przy użyciu szablonu usługi resource manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) lub za pomocą interfejsu wiersza polecenia, określając identyfikator URI do skopiowanego dysku, wpisując:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak zarządzać nowej maszyny wirtualnej za pomocą wiersza polecenia platformy Azure, zobacz [polecenia wiersza polecenia platformy Azure dla Menedżera zasobów Azure](../azure-cli-arm-commands.md).

