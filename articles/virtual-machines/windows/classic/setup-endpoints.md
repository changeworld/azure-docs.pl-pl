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
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957169"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Skonfiguruj punkty końcowe na maszynie wirtualnej Windows przy użyciu klasycznego modelu wdrażania
Windows maszyny wirtualne (VM), które tworzysz na platformie Azure przy użyciu klasycznego modelu wdrażania automatycznie mogą komunikować się za pośrednictwem kanału sieci prywatnej z innymi maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej. Jednak komputery w Internecie lub innymi sieciami wirtualnymi wymagają punktów końcowych do kierowania ruchu sieciowego przychodzącego do maszyny Wirtualnej. 

Można również skonfigurować punkty końcowe na [maszyn wirtualnych systemu Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../../resource-manager-deployment-model.md). W tym artykule opisano klasyczny model wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W **usługi Resource Manager** modelu wdrażania, punkty końcowe są konfigurowane przy użyciu **sieciowych grup zabezpieczeń (NSG)**. Aby uzyskać więcej informacji, zobacz [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Po utworzeniu maszyny Wirtualnej z systemem Windows w witrynie Azure portal wspólnych punktów końcowych, takich jak punkty końcowe dla pulpitu zdalnego i komunikacji zdalnej programu Windows PowerShell, są zazwyczaj tworzone automatycznie. Dodatkowe punkty końcowe można skonfigurować później zgodnie z potrzebami.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować punkt końcowy maszyny Wirtualnej za pomocą polecenia cmdlet programu Azure PowerShell, zobacz [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Aby użyć polecenia cmdlet programu Azure PowerShell do zarządzania listy ACL punktu końcowego, zobacz [Zarządzanie listy kontroli dostępu (ACL) dla punktów końcowych przy użyciu programu PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Jeśli utworzono maszynę wirtualną w modelu wdrażania usługi Resource Manager, możesz użyć programu Azure PowerShell [tworzenie sieciowych grup zabezpieczeń](../../../virtual-network/tutorial-filter-network-traffic.md) umożliwiają kontrolowanie ruchu do maszyny Wirtualnej.
