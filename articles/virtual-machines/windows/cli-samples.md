---
title: Interfejs wiersza polecenia Azure przykłady Windows | Dokumentacja firmy Microsoft
description: Windows — przykłady wiersza polecenia platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: abc38d315bc879a06ecd9a9bf7188c15533c018a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535012"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Przykłady interfejsu wiersza polecenia dla Windows maszyn wirtualnych

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą wiersza polecenia platformy Azure, które wdrażanie maszyn wirtualnych Windows.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną Windows z minimalną konfiguracją. |
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszyna wirtualna i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem. |
| [Tworzenie maszyny Wirtualnej, a następnie uruchom skrypt konfiguracji](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia skryptu niestandardowego usługi Azure, aby zainstalować usługi IIS. |
| [Tworzenie maszyny Wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia Azure Desired State Configuration (DSC), aby zainstalować usługi IIS. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany na podstawie specjalnego dysku VHD jako dysk systemu operacyjnego lub na podstawie dysku VHD z danymi jako dysk danych.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje zarządzanych dysków do tych samych lub różnych subskrypcji, ale w tym samym regionie co element nadrzędny dysk zarządzany. 
| [Eksportowanie migawki jako dysku VHD do konta magazynu](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysku VHD do konta magazynu w innym regionie. |
| [Eksportuj wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje bazowego wirtualnego dysku twardego dysku zarządzanego na konto magazynu w innym regionie. |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje migawkę do tej samej lub różnych subskrypcji, ale w tym samym regionie jako migawki nadrzędnej. |
|**Sieci wirtualne**||
| [Zabezpieczanie ruchu sieciowego między maszynami wirtualnymi](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dwie maszyny wirtualne i wszystkie pokrewne zasoby, wewnętrznej i zewnętrznej sieciowej grupy zabezpieczeń (NSG). |
|**Bezpieczne maszyn wirtualnych**||
| [Szyfrowanie dysków maszyn wirtualnych i danych](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy usługi Azure Key Vault, klucz szyfrowania i jednostki usługi, a następnie szyfruje maszyny Wirtualnej. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny Wirtualnej z usługą Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, a następnie instaluje agenta usługi Log Analytics i rejestruje maszyny Wirtualnej w obszarze roboczym usługi Log Analytics.  |
| | |
