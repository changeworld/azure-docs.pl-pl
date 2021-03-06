---
title: Przykłady interfejsu wiersza polecenia platformy Azure systemu Windows
description: Przykłady interfejsu wiersza polecenia platformy Azure systemu Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8d57220f79f1349937a279e57a17d51a76bcccb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033641"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows

Poniższa tabela zawiera łącza do skryptów bash utworzonych przy użyciu interfejsu wiersza polecenia platformy Azure, które wdrażają maszyny wirtualne systemu Windows.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną systemu Windows przy minimalnej konfiguracji. |
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i z równoważenia obciążenia. |
| [Tworzenie maszyny Wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia skryptu niestandardowego platformy Azure do zainstalowania usług IIS. |
| [Tworzenie maszyny wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia konfiguracji żądanego stanu platformy Azure (DSC) do zainstalowania usług IIS. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany ze specjalistycznego dysku VHD jako dysk systemu operacyjnego lub z dysku wirtualnego z danymi jako dysk danych.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany z migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, ale w tym samym regionie co nadrzędny dysk zarządzany. 
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje migawkę zarządzaną jako dysk VHD do konta magazynu w różnych regionach. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje podstawowy dysk wirtualny dysku zarządzanego do konta magazynu w różnych regionach. |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, ale w tym samym regionie co migawka nadrzędna. |
|**Sieciowe maszyny wirtualne**||
| [Bezpieczny ruch sieciowy między maszynami wirtualnymi](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dwie maszyny wirtualne, wszystkie powiązane zasoby oraz wewnętrzne i zewnętrzne grupy zabezpieczeń sieci (NSG). |
|**Bezpieczne maszyn wirtualnych**||
| [Szyfrowanie maszyny wirtualnej i dysków danych](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy usługę Azure Key Vault, klucz szyfrowania i jednostkę usługi, a następnie szyfruje maszynę wirtualną. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą usługi Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta usługi Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym usługi Log Analytics.  |
| | |
