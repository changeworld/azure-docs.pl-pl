---
title: Przykłady programu PowerShell maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Przykłady programu PowerShell maszyny wirtualnej platformy Azure
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
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 564e8e2968f31b9e2aef8f7eadbf848885037ccb
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040766"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Przykłady programu Azure PowerShell maszyny wirtualnej

Poniższa tabela zawiera linki do przykładowych skryptów programu PowerShell, które umożliwiają tworzenie i zarządzanie maszynami wirtualnymi Windows.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Szybkie tworzenie maszyny wirtualnej](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy grupę zasobów, maszyna wirtualna i wszystkie pokrewne zasoby za pomocą co najmniej monity.|
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy grupę zasobów, maszyna wirtualna i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem.|
| [Tworzenie maszyny Wirtualnej, a następnie uruchom skrypt konfiguracji](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia skryptu niestandardowego usługi Azure, aby zainstalować usługi IIS. |
| [Tworzenie maszyny Wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia Azure Desired State Configuration (DSC), aby zainstalować usługi IIS. |
| [Przekazywanie wirtualnego dysku twardego i Tworzenie maszyn wirtualnych](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Przekazuje plik lokalny wirtualnego dysku twardego na platformie Azure, tworzy i obrazów z wirtualnego dysku twardego i następnie tworzy Maszynę wirtualną na podstawie tego obrazu. |
| [Utwórz Maszynę wirtualną z zarządzanego dysku systemu operacyjnego](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysk systemu operacyjnego. |
| [Tworzenie maszyny Wirtualnej na podstawie migawki](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy maszynę wirtualną z migawki, najpierw Tworzenie dysku zarządzanego na podstawie migawki, a następnie Dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego w tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy dysk zarządzany na podstawie wyspecjalizowanego wirtualnego dysku twardego jako dysk systemu operacyjnego lub dane wirtualnego dysku twardego dysku danych w tej samej lub różnych subskrypcji.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopiuje zarządzanych dysków do tych samych lub różnych subskrypcji, ale w tym samym regionie co element nadrzędny dysk zarządzany. 
| [Eksportowanie migawki jako dysku VHD do konta magazynu](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysku VHD do konta magazynu w innym regionie. |
| [Eksportuj wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Eksportuje bazowego wirtualnego dysku twardego dysku zarządzanego na konto magazynu w innym regionie. |
| [Tworzenie migawki z wirtualnego dysku twardego](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy migawki z wirtualnego dysku twardego do utworzenia wielu identycznych dysków zarządzanych z migawki w krótkim czasie.  |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Kopiuje migawkę do tej samej lub różnych subskrypcji, ale w tym samym regionie jako migawki nadrzędnej. |
|**Bezpieczne maszyn wirtualnych**||
| [Szyfrowanie dysków maszyn wirtualnych i danych](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Tworzy usługi Azure Key Vault, klucz szyfrowania i jednostki usługi, a następnie szyfruje maszyny Wirtualnej. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny Wirtualnej za pomocą pakietu Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzy maszynę wirtualną, a następnie instaluje agenta pakietu Operations Management Suite i rejestruje maszyny Wirtualnej w obszarze roboczym pakietu OMS.  |
| | |

