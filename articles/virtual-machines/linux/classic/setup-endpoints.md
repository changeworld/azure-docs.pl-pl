---
title: Skonfigurować punkty końcowe na klasycznej maszyny Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować punkty końcowe dla maszyny Wirtualnej z systemem Linux w witrynie Azure portal, aby umożliwić komunikację z maszyną wirtualną systemu Linux na platformie Azure
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295691"
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Jak skonfigurować punkty końcowe na klasycznej maszynie wirtualnej systemu Linux na platformie Azure
Wszystkie maszyny wirtualne systemu Linux, które tworzysz na platformie Azure przy użyciu klasycznego modelu wdrażania automatycznie mogą komunikować się za pośrednictwem kanału sieci prywatnej z innymi maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej. Jednak komputery w Internecie lub innymi sieciami wirtualnymi wymagają punktów końcowych do kierowania ruchu sieciowego przychodzącego do maszyny wirtualnej. W tym artykule jest także dostępna dla [maszyn wirtualnych Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W **usługi Resource Manager** modelu wdrażania, punkty końcowe są konfigurowane przy użyciu **sieciowych grup zabezpieczeń (NSG)**. Aby uzyskać więcej informacji, zobacz [Otwieranie portów i punktów końcowych](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Po utworzeniu maszyny wirtualnej systemu Linux w witrynie Azure portal, punkt końcowy dla protokołu Secure Shell (SSH) jest zazwyczaj tworzony automatycznie. Dodatkowe punkty końcowe można skonfigurować podczas tworzenia maszyny wirtualnej lub po jego zgodnie z potrzebami.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Kolejne kroki
* Można również utworzyć punkt końcowy maszyny Wirtualnej przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Uruchom **punktu końcowego maszyny wirtualnej platformy azure utworzyć** polecenia.
* Jeśli utworzono maszynę wirtualną w modelu wdrażania usługi Resource Manager, można użyć wiersza polecenia platformy Azure w trybie usługi Resource Manager, aby [tworzenie sieciowych grup zabezpieczeń](../../../virtual-network/tutorial-filter-network-traffic-cli.md) umożliwiają kontrolowanie ruchu do maszyny Wirtualnej.
