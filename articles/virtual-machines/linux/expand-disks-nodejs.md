---
title: Zwiększ rozmiar dysku systemu operacyjnego na maszynie Wirtualnej systemu Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć wirtualny dysk systemu operacyjnego (OS) na maszynie Wirtualnej systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i modelu wdrażania usługi Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: f81054727bb1f0e8ffa752783e866a72d573589d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30905337"
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Zwiększ rozmiar dysku systemu operacyjnego na maszynie Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure z interfejsu wiersza polecenia platformy Azure w wersji 1.0
Domyślny rozmiar wirtualnego dysku twardego systemu operacyjnego (OS) jest zwykle 30 GB na maszynie wirtualnej systemu Linux (VM) na platformie Azure. Możesz [Dodaj dyski danych](add-disk.md) zapewnienie dodatkowego miejsca, ale mogą też chcieć Zwiększ rozmiar dysku systemu operacyjnego. Ten artykuł zawiera szczegóły dotyczące sposobu rozszerzania dysku systemu operacyjnego dla maszyny Wirtualnej systemu Linux przy użyciu niezarządzanych dysków z interfejsu wiersza polecenia platformy Azure w wersji 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#prerequisites) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](expand-disks.md) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami

## <a name="prerequisites"></a>Wymagania wstępne
Należy [najnowsze Azure CLI 1.0](../../cli-install-nodejs.md) zainstalowane i zarejestrowane celu [konta Azure](https://azure.microsoft.com/pricing/free-trial/) przy użyciu tryb usługi Resource Manager w następujący sposób:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają *myResourceGroup* i *myVM*.

## <a name="expand-os-disk"></a>Rozszerzanie dysku systemu operacyjnego

1. Nie można wykonać operacji na wirtualnych dyskach twardych z uruchomionych maszyn wirtualnych. W poniższym przykładzie zatrzymuje i cofa alokację maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` zwalnia zasoby obliczeniowe. Aby zwolnić zasoby obliczeniowe, użyj `azure vm deallocate`. Aby zwiększyć rozmiaru wirtualnego dysku twardego, można cofnąć przydziału maszyny Wirtualnej.

2. Aktualizacja rozmiaru niezarządzane użycie dysku systemu operacyjnego `azure vm set` polecenia. Poniższy przykład aktualizuje maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup* jako *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Uruchom maszyny Wirtualnej w następujący sposób:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH do maszyny Wirtualnej przy użyciu odpowiednich poświadczeń. Aby sprawdzić, zmiany rozmiaru dysku systemu operacyjnego, użyj `df -h`. Partycja podstawowa zawiera następujące przykładowe dane wyjściowe (*/dev/sda1*) jest teraz 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz dodatkowego magazynu, możesz również [Dodaj dyski danych do maszyny Wirtualnej systemu Linux](add-disk.md). Aby uzyskać więcej informacji o szyfrowaniu dysków, zobacz [szyfrowania dysków na Maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia Azure](encrypt-disks.md).
