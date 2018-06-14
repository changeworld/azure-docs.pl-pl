---
title: Konfigurowanie punktów końcowych w klasycznym maszyny Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować punktów końcowych dla maszyny Wirtualnej systemu Linux, w portalu Azure, aby umożliwić komunikację z maszyny wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 03cb90dcdcc7a7407898ddd8cbc30f1af0833676
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30840986"
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Jak skonfigurować punkty końcowe w klasycznym maszyny wirtualnej systemu Linux na platformie Azure
Wszystkie maszyny wirtualne systemu Linux utworzone na platformie Azure przy użyciu klasycznego modelu wdrażania można automatycznie komunikują się za pośrednictwem kanału między maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej sieci prywatnej. Jednak komputery w Internecie lub innych sieci wirtualnych wymagają punktów końcowych, aby skierować ruch sieciowy przychodzący do maszyny wirtualnej. W tym artykule jest również dostępny do [maszyn wirtualnych Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W **Resource Manager** model wdrażania, punkty końcowe są skonfigurowane przy użyciu **grup zabezpieczeń sieci (NSG)**. Aby uzyskać więcej informacji, zobacz [Otwieranie portów i punkty końcowe](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Po utworzeniu maszyny wirtualnej systemu Linux w portalu Azure, punkt końcowy dla protokołu Secure Shell (SSH) jest zwykle tworzony automatycznie. Dodatkowe punkty końcowe można skonfigurować podczas tworzenia maszyny wirtualnej lub po jego zgodnie z potrzebami.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Kolejne kroki
* Punkt końcowy maszyny Wirtualnej można również utworzyć za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Uruchom **utworzyć punktu końcowego maszyny wirtualnej azure** polecenia.
* Jeśli utworzono maszynę wirtualną w modelu wdrażania usługi Resource Manager, można użyć wiersza polecenia platformy Azure w trybie Menedżera zasobów, aby [Utwórz grupy zabezpieczeń sieci](../../../virtual-network/tutorial-filter-network-traffic-cli.md) sterujących ruchem do maszyny Wirtualnej.
