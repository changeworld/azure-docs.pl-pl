---
title: Przykłady programu PowerShell maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Przykłady programu PowerShell maszyny wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 53784c3d74f9e6af5f1e84cc098194113e81333b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771264"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Przykłady programu Azure PowerShell maszyny wirtualnej

Poniższa tabela zawiera linki do przykładowych skryptów programu PowerShell, które umożliwiają tworzenie i zarządzanie maszynami wirtualnymi systemu Linux.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy grupę zasobów, maszyna wirtualna i wszystkie powiązane zasoby.|
| [Utwórz Maszynę wirtualną z włączoną platformą Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, a następnie konfiguruje tę maszynę Wirtualną jako hosta Docker i uruchamia kontener NGINX. |
| [Tworzenie maszyny Wirtualnej, a następnie uruchom skrypt konfiguracji](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia skryptu niestandardowego usługi Azure w celu zainstalowania aparatu NGINX. |
| [Tworzenie maszyny Wirtualnej z platformą WordPress zainstalowany](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia Azure niestandardowego skryptu do zainstalowania systemu WordPress. |
| [Utwórz Maszynę wirtualną z zarządzanego dysku systemu operacyjnego](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysk systemu operacyjnego. |
| [Tworzenie maszyny Wirtualnej na podstawie migawki](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną z migawki, najpierw tworząc dysku zarządzanego z migawki, a następnie Dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD w tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany z wyspecjalizowanego wirtualnego dysku twardego jako dysk systemu operacyjnego lub dysku VHD jako dysk danych, w tym samym danych lub innej subskrypcji.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Eksportowanie migawki jako dysku VHD do konta magazynu](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysku VHD do konta magazynu w innym regionie. |
| [Eksportuj wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje bazowego wirtualnego dysku twardego dysku zarządzanego na konto magazynu w innym regionie. |
| [Tworzenie migawki z wirtualnego dysku twardego](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy migawkę z wirtualnego dysku twardego, a następnie używa tej migawki do szybkiego utworzenia wielu identycznych dysków zarządzanych.  |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie jako migawki nadrzędnej. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny Wirtualnej przy użyciu dzienników usługi Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, a następnie instaluje agenta usługi Log Analytics i rejestruje maszyny Wirtualnej w obszarze roboczym usługi Log Analytics.  |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie, co nadrzędnego dysku zarządzanego.
| | |
