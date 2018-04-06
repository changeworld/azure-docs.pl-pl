---
title: Tworzenie klasycznej maszyny Wirtualnej systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny wirtualnej systemu Linux z 1.0 interfejsu wiersza polecenia platformy Azure przy użyciu klasycznego modelu wdrożenia
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 6d3f0dd0c82ad32df4d6e17058d9b1bea57c301f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Tworzenie klasycznej maszyny Wirtualnej systemu Linux z Azure CLI 1.0
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Wersja Menedżera zasobów dla [tutaj](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym temacie opisano sposób tworzenia maszyny wirtualnej systemu Linux (VM) z 1.0 interfejsu wiersza polecenia platformy Azure przy użyciu klasycznego modelu wdrożenia. Używamy obrazu systemu Linux, spośród dostępnych **obrazów** na platformie Azure. Polecenia interfejsu wiersza polecenia platformy Azure w wersji 1.0 zapewniają następujące opcje konfiguracji, między innymi:

* Połączenie z maszyną Wirtualną do sieci wirtualnej
* Dodawanie maszyny Wirtualnej do istniejącej usługi w chmurze
* Dodawanie maszyny Wirtualnej do istniejącego konta magazynu
* Dodawanie maszyny Wirtualnej do zestawu dostępności lub lokalizacji

> [!IMPORTANT]
> Jeśli chcesz użyć sieci wirtualnej do połączenia się do niego bezpośrednio przez hosta lub konfigurowanie połączeń między różnymi lokalizacjami maszyny Wirtualnej upewnij się, że sieci wirtualnej można określić podczas tworzenia maszyny Wirtualnej. Maszyny Wirtualnej można skonfigurować do przyłączenia sieci wirtualnej wyłącznie w przypadku utworzenia maszyny Wirtualnej. Aby uzyskać szczegółowe informacje w sieciach wirtualnych, zobacz [omówienie sieci wirtualnych Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Tworzenie maszyny Wirtualnej systemu Linux przy użyciu klasycznego modelu wdrożenia
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

