---
title: Usługa Azure GPU N-series driver Setup for Konfiguracja Windows | Dokumentacja firmy Microsoft
description: Jak skonfigurować sterowniki procesora GPU NVIDIA dla maszyn wirtualnych serii N z systemem Windows Server lub Windows na platformie Azure
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b6f0d79e773ac7b79c79e4be6206fe39928cd0a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127788"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników procesora GPU NVIDIA na maszynach wirtualnych serii N z systemem Windows 

Aby skorzystać z możliwości procesora GPU platformy Azure serii N maszyn wirtualnych z systemem Windows, muszą być zainstalowane sterowniki procesora GPU firmy NVIDIA. [Rozszerzenia sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub siatki na maszynie Wirtualnej serii N. Zainstaluj lub zarządzać rozszerzenia za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub usługi Azure Resource Manager. Zobacz [dokumentację dotyczącą rozszerzeń sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroki związane z wdrażaniem.

Jeśli zdecydujesz się zainstalować sterowniki procesora GPU ręcznie, ten artykuł zawiera obsługiwane systemy operacyjne, sterowników i kroki instalacji i weryfikacji. Informacje o konfiguracji ręcznej sterownik jest również dostępna dla [maszyn wirtualnych systemu Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Specyfikacje podstawowych, pojemności magazynu i szczegóły dysku, zobacz [rozmiarów maszyn wirtualnych Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalacja sterownika

1. Nawiąż połączenie, poszczególnych maszyn wirtualnych serii N usługi pulpitu zdalnego.

2. Pobierz, Wyodrębnij i zainstaluj obsługiwany sterownik w systemie operacyjnym Windows.

Po zakończeniu instalacji sterowników siatki na maszynie Wirtualnej ponowne uruchomienie jest wymagane. Po zakończeniu instalacji sterownik CUDA ponowne uruchomienie komputera nie jest wymagane.

## <a name="verify-driver-installation"></a>Weryfikacja instalacji sterowników

Aby sprawdzić, instalacja sterownika w Menedżerze urządzeń. Poniższy przykład pokazuje pomyślne konfiguracji karta Tesla K80 na Maszynie wirtualnej platformy Azure przez kontroler sieci.

![Właściwości sterowników procesora GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Aby zbadać stan urządzenia procesora GPU, uruchom [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem narzędzie wiersza polecenia.

1. Otwórz wiersz polecenia i zmień **C:\Program Files\NVIDIA Corporation\NVSMI** katalogu.

2. Uruchom polecenie `nvidia-smi`. Jeśli sterownik jest zainstalowany, pojawi się dane wyjściowe podobne do następujących. **GPU Util** pokazuje **0%** , chyba że obciążenie procesora GPU są aktualnie uruchomione na maszynie Wirtualnej. Twoja wersja sterownika i szczegóły procesora GPU mogą różnić się od wyświetlonych.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Połączenie sieciowe RDMA

Połączenie sieciowe RDMA można włączyć dla obsługą dostępu RDMA maszyn wirtualnych serii N, takich jak NC24r wdrożonych w tym samym zestawie dostępności lub w pojedynczej grupy umieszczania w zestawie skalowania maszyn wirtualnych. Aby zainstalować sterowniki urządzeń sieciowych Windows, które umożliwiają łączność RDMA, należy dodać rozszerzenie HpcVmDrivers. Aby dodać rozszerzenie maszyny Wirtualnej do maszyny Wirtualnej serii N z obsługą funkcji RDMA, należy użyć [programu Azure PowerShell](/powershell/azure/overview) poleceń cmdlet usługi Azure Resource Manager.

Aby zainstalować najnowszą wersję 1.1 rozszerzenia HpcVMDrivers obsługą dostępu RDMA istniejącej maszyny wirtualnej o nazwie myVM w regionie zachodnie stany USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Aby uzyskać więcej informacji, zobacz [rozszerzeń maszyn wirtualnych i funkcji dla Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Sieć RDMA obsługuje ruch interfejsu przekazywania komunikatów (MPI) dla aplikacji uruchamianych przy użyciu [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) lub Intel MPI 5.x. 


## <a name="next-steps"></a>Kolejne kroki

* Deweloperzy tworzący aplikacje accelerated procesora GPU dla procesory GPU NVIDIA Tesla można także pobrać i zainstalować najnowszą [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


