---
title: Instalacja sterownika N-series usługi Azure dla systemu Windows | Dokumentacja firmy Microsoft
description: Jak skonfigurować wersji sterowników procesora GPU NVIDIA dla N-series uruchomionych maszyn wirtualnych systemu Windows Server lub Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efa8c2603d6ff4493656cda41306a5dad46bc5f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Konfigurowanie wersji sterowników procesora GPU N serii maszyn wirtualnych systemu Windows 
Aby skorzystać z możliwości procesora GPU N-series maszyny wirtualne platformy Azure uruchomiona obsługiwana wersja systemu Windows Server lub systemu Windows, muszą zostać zainstalowane sterowniki grafiki NVIDIA. Ten artykuł zawiera kroki konfiguracji sterownika po wdrożeniu maszyny Wirtualnej N serii. Informacje o instalacji sterowników jest również dostępny do [maszyn wirtualnych systemu Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dla podstawowych specyfikacji, pojemności magazynu i dysku szczegółów, zobacz [rozmiarów maszyn wirtualnych systemu Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalacja sterownika

1. Połączenia pulpitu zdalnego na każdej maszynie Wirtualnej N serii.

2. Pobierz, Wyodrębnij i zainstaluj obsługiwany sterownik dla systemu operacyjnego Windows.

Na maszynach wirtualnych z wirtualizacją sieci Azure po instalacji sterowników jest wymagane ponowne uruchomienie komputera. Na maszynach wirtualnych NC ponowne uruchomienie nie jest wymagane.

## <a name="verify-driver-installation"></a>Sprawdzić, czy instalacja sterownika

Instalacja sterownika w Menedżerze urządzeń można sprawdzić. W poniższym przykładzie przedstawiono Konfiguracja zakończyła się pomyślnie karty K80 tesla — na maszynie Wirtualnej platformy Azure NC.

![Właściwości sterownika procesora GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Aby sprawdzić stan urządzenia procesora GPU, uruchom [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem narzędzie wiersza polecenia.

1. Otwórz wiersz polecenia i zmień **C:\Program Files\NVIDIA Corporation\NVSMI** katalogu.

2. Uruchom polecenie `nvidia-smi`. Jeśli jest zainstalowany sterownik zostaną wyświetlone dane wyjściowe podobne do następującego. Należy pamiętać, że **GPU Util** pokazuje **0%** chyba, że obciążenie procesora GPU są aktualnie uruchomione na maszynie Wirtualnej. Wersja sterownika, a szczegóły GPU mogą być inne niż te wyświetlane.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Połączenie sieciowe RDMA

Połączenie sieciowe RDMA można włączyć na maszynach wirtualnych z funkcją RDMA N-series, takie jak NC24r wdrażane w tym samym zestawie dostępności lub zestaw skali maszyny Wirtualnej. Aby zainstalować sterowniki urządzeń sieciowych systemu Windows, umożliwiających łączności RDMA należy dodać rozszerzenie HpcVmDrivers. Aby dodać rozszerzenie maszyny Wirtualnej do maszyny Wirtualnej z obsługą RDMA N-series, użyj [programu Azure PowerShell](/powershell/azure/overview) poleceń cmdlet dla usługi Azure Resource Manager.

Aby zainstalować najnowszą wersję 1.1 rozszerzenia HpcVMDrivers na istniejącej maszyny Wirtualnej z funkcją RDMA o nazwie myVM regionu zachodnie stany USA:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Aby uzyskać więcej informacji, zobacz [rozszerzenia maszyny wirtualnej i funkcje systemu Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Sieć RDMA obsługuje ruch interfejsu Message (Passing) dla aplikacji działających z [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) lub Intel MPI 5.x. 


## <a name="next-steps"></a>Kolejne kroki

* Deweloperzy tworzący aplikacje przyspieszony GPU dla jednostki GPU tesla — NVIDIA można również pobrać i zainstalować [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


