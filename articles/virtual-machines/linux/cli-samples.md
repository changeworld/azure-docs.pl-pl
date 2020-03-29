---
title: Przykłady interfejsu wiersza polecenia platformy Azure
description: Przykłady interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 76803f8c3a703071eb733c5cfde65482ffd07f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970079"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie maszyny wirtualnej](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną systemu Linux przy minimalnej konfiguracji. |
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i z równoważenia obciążenia. |
| [Tworzenie maszyny Wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia skryptu niestandardowego platformy Azure do zainstalowania NGINX. |
| [Tworzenie maszyny wirtualnej z zainstalowanym wordpressem](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia skryptu niestandardowego platformy Azure do zainstalowania wordpressa. |
| [Tworzenie maszyny wirtualnej na dysku zarządzanego systemu operacyjnego](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, dołączając istniejący dysk zarządzany jako dysk systemu operacyjnego. |
| [Tworzenie maszyny Wirtualnej na podstawie migawki](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną z migawki, najpierw tworząc dysk zarządzany z migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany ze specjalistycznego dysku VHD jako dysk systemu operacyjnego lub z dysku wirtualnego z danymi jako dysk danych.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany z migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, ale w tym samym regionie co nadrzędny dysk zarządzany. 
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje migawkę zarządzaną jako dysk VHD do konta magazynu w różnych regionach. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje podstawowy dysk wirtualny dysku zarządzanego do konta magazynu w różnych regionach. |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, ale w tym samym regionie co migawka nadrzędna. |
|**Sieciowe maszyny wirtualne**||
| [Bezpieczny ruch sieciowy między maszynami wirtualnymi](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dwie maszyny wirtualne, wszystkie powiązane zasoby oraz wewnętrzne i zewnętrzne grupy zabezpieczeń sieci (NSG). |
|**Bezpieczne maszyn wirtualnych**||
| [Szyfrowanie maszyny wirtualnej i dysków danych](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy usługę Azure Key Vault, klucz szyfrowania i jednostkę usługi, a następnie szyfruje maszynę wirtualną. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą dzienników usługi Azure Monitor](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta usługi Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym usługi Log Analytics.  |
|**Rozwiązywanie problemów z maszynami wirtualnymi**||
| [Rozwiązywanie problemów z dyskiem systemu operacyjnego maszyn wirtualnych](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Instaluje dysk systemu operacyjnego z jednej maszyny Wirtualnej jako dysk danych na drugiej maszynie wirtualnej. |
| | |
