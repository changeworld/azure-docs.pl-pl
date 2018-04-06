---
title: Jak zmienić rozmiar maszyny Wirtualnej systemu Linux, z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Jak skalowanie w górę i skalowania maszynę wirtualną systemu Linux przez zmianę rozmiaru maszyny Wirtualnej.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bb5ad082336c92a397f2590d9b06e94552c8744
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Zmień rozmiar maszyny Wirtualnej systemu Linux z interfejsu wiersza polecenia platformy Azure 1.0

## <a name="overview"></a>Przegląd

Po udostępnić maszynę wirtualną (VM), można skalować maszyny Wirtualnej w górę lub w dół, zmieniając [rozmiar maszyny Wirtualnej][vm-sizes]. W niektórych przypadkach należy najpierw cofnąć maszyny Wirtualnej. Może to nastąpić, jeśli nowy rozmiar jest niedostępny w klastrze sprzętu, który jest hostem maszyny Wirtualnej.

W tym artykule przedstawiono sposób zmiany rozmiaru maszyny Wirtualnej systemu Linux przy użyciu [interfejsu wiersza polecenia Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#resize-a-linux-vm) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="resize-a-linux-vm"></a>Zmień rozmiar maszyny Wirtualnej systemu Linux
Aby zmienić rozmiar maszyny Wirtualnej, wykonaj następujące kroki.

1. Uruchom następujące polecenie, interfejsu wiersza polecenia. To polecenie wyświetla listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętu, gdzie jest hostowana maszyny Wirtualnej.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Jeśli na liście jest wymagany rozmiar, uruchom następujące polecenie, aby zmienić rozmiar maszyny Wirtualnej.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    Maszyna wirtualna zostanie ponownie uruchomiona w trakcie tego procesu. Po uruchomieniu z istniejącego systemu operacyjnego i dysków z danymi będzie można ponownie zamapować. Elementy na dysku tymczasowym zostaną utracone.
   
    Użyj `--enable-boot-diagnostics` powoduje włączenie [diagnostyki rozruchu][boot-diagnostics], aby rejestrować błędy dotyczące uruchamiania.
3. W przeciwnym razie Jeśli żądany rozmiar nie jest wyświetlana, uruchom następujące polecenia można cofnąć alokacji maszyny Wirtualnej, rozmiar i ponownie uruchom maszynę Wirtualną.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Cofanie przydziału maszyny Wirtualnej zwalnia również dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie dotyczy systemu operacyjnego i dysków z danymi.
   > 
   > 

## <a name="next-steps"></a>Kolejne kroki
Dodatkowe możliwości skalowania uruchamianie wielu wystąpień maszyny Wirtualnej i skalowanie w poziomie. Aby uzyskać więcej informacji, zobacz [automatycznie skalować w zestawie skalowania maszyn wirtualnych systemu Linux maszyny][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
