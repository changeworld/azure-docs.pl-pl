---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej przy użyciu dysku VHD | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej przy użyciu wirtualnego dysku twardego.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 414ef43063cc48b7b9ae7be5fbccbb7906ae8c03
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29849345"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Tworzenie maszyny wirtualnej przy użyciu wirtualnego dysku twardego

W tym przykładzie maszyna wirtualna jest tworzona przy użyciu dysku VHD.
Tworzona jest grupa zasobów, konto magazynu i kontener, a następnie maszyna wirtualna przez przekazanie dysku VHD do kontenera.
Zastępuje klucz publiczny ssh Twoim kluczem publicznym, aby udzielić Ci dostępu do maszyny wirtualnej.

Będziesz potrzebować rozruchowego dysku VHD.
Użyty dysk VHD możesz pobrać z lokalizacji https://azclisamples.blob.core.windows.net/vhds/sample.vhd lub możesz użyć własnego dysku VHD. Skrypt szuka elementu `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Wyświetla listę kont magazynu |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Sprawdza, czy nazwa konta magazynu jest prawidłowa i czy już nie istnieje |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | Wyświetla listę kluczy kont magazynu |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Sprawdza, czy obiekt blob istnieje |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Tworzy kontener na koncie magazynu. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | Tworzy obiekt blob w kontenerze, przekazując dysk VHD. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Używane z opcją `--query` w celu sprawdzenia, czy nazwa maszyny wirtualnej jest już w użyciu. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Tworzy maszyny wirtualne. |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#az_vm_access_set_linux_user) | Resetuje klucz SSH, aby przyznać bieżącemu użytkownikom prawa dostępu do maszyny wirtualnej. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Pobiera adres IP utworzonej maszyny wirtualnej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
