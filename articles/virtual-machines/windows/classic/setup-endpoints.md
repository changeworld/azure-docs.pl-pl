---
title: Skonfiguruj punkty końcowe na klasycznej maszynie Wirtualnej Windows | Dokumentacja firmy Microsoft
description: Dowiedz się skonfigurować punkty końcowe dla klasycznych maszyn wirtualnych Windows w witrynie Azure portal, aby umożliwić komunikację z maszyną wirtualną Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618243"
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Jak skonfigurować punkty końcowe na klasycznej maszynie wirtualnej Windows na platformie Azure
Wszystkie Windows, które maszyny wirtualne, które tworzysz na platformie Azure przy użyciu klasycznego modelu wdrażania można automatycznie komunikują się za pośrednictwem prywatnej kanał sieciowy z innymi maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej. Jednak komputery w Internecie lub innymi sieciami wirtualnymi wymagają punktów końcowych do kierowania ruchu sieciowego przychodzącego do maszyny wirtualnej. W tym artykule jest także dostępna dla [maszyn wirtualnych systemu Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W **usługi Resource Manager** modelu wdrażania, punkty końcowe są konfigurowane przy użyciu **sieciowych grup zabezpieczeń (NSG)**. Aby uzyskać więcej informacji, zobacz [zezwalanie na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Po utworzeniu maszyny wirtualnej Windows w witrynie Azure portal wspólnych punktów końcowych, takich jak te dotyczące usług pulpitu zdalnego i komunikacji zdalnej programu Windows PowerShell są zazwyczaj tworzone automatycznie. Dodatkowe punkty końcowe można skonfigurować podczas tworzenia maszyny wirtualnej lub po jego zgodnie z potrzebami.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować punkt końcowy maszyny Wirtualnej za pomocą polecenia cmdlet programu Azure PowerShell, zobacz [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Aby użyć polecenia cmdlet programu Azure PowerShell do zarządzania listy ACL punktu końcowego, zobacz [Zarządzanie listy kontroli dostępu (ACL) dla punktów końcowych przy użyciu programu PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Jeśli utworzono maszynę wirtualną w modelu wdrażania usługi Resource Manager, możesz użyć programu Azure PowerShell [tworzenie sieciowych grup zabezpieczeń](../../../virtual-network/tutorial-filter-network-traffic.md) umożliwiają kontrolowanie ruchu do maszyny Wirtualnej.
