---
title: Tworzenie klasycznej maszyny Wirtualnej systemu Linux przy użyciu platformy Azure klasyczny interfejs wiersza polecenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu klasycznego wiersza polecenia platformy Azure przy użyciu klasycznego modelu wdrażania
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 507d9e12a37d7bf187a3e56b04cb47ac0104773d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982045"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Jak utworzyć klasycznej maszyny Wirtualnej systemu Linux przy użyciu platformy Azure klasyczny interfejs wiersza polecenia
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Wersja usługi Resource Manager dla [tutaj](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym temacie opisano sposób tworzenia maszyny wirtualnej systemu Linux (VM) za pomocą klasycznego wiersza polecenia platformy Azure przy użyciu klasycznego modelu wdrażania. Używamy obrazu systemu Linux z dostępnych **obrazów** na platformie Azure. Klasyczne poleceń interfejsu wiersza polecenia platformy Azure zapewniają następujące opcje konfiguracji, między innymi:

* Łączenie maszyny Wirtualnej z siecią wirtualną
* Dodawanie maszyny Wirtualnej do istniejącej usługi w chmurze
* Dodawanie maszyny Wirtualnej do istniejącego konta magazynu
* Dodawanie maszyny Wirtualnej w zestawie dostępności lub lokalizacji

> [!IMPORTANT]
> Jeśli chcesz, aby maszyny Wirtualnej, aby użyć sieci wirtualnej, aby połączyć do niego bezpośrednio przez nazwę hosta lub konfigurowanie połączeń między środowiskami lokalnymi, upewnij się, że możesz określić tę sieć podczas tworzenia maszyny Wirtualnej. Maszynę wirtualną można skonfigurować do dołączenia do sieci wirtualnej tylko wtedy, gdy tworzysz maszynę Wirtualną. Aby uzyskać szczegółowe informacje w sieciach wirtualnych, zobacz [Omówienie usługi Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Jak utworzyć Maszynę wirtualną systemu Linux przy użyciu klasycznego modelu wdrażania
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

