---
title: Konfigurowanie punktów końcowych w klasycznym maszyny Wirtualnej systemu Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, można skonfigurować punkty końcowe dla klasycznego maszyny Wirtualnej systemu Windows w portalu Azure, aby umożliwić komunikację z maszyny wirtualnej systemu Windows na platformie Azure.
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
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Jak skonfigurować punkty końcowe w klasycznym maszyny wirtualnej systemu Windows na platformie Azure
Wszystkie okna, które maszyn wirtualnych, które tworzenia na platformie Azure przy użyciu klasycznego modelu wdrażania można automatycznie komunikują się za pośrednictwem prywatnej kanał sieciowy między maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej. Jednak komputery w Internecie lub innych sieci wirtualnych wymagają punktów końcowych, aby skierować ruch sieciowy przychodzący do maszyny wirtualnej. W tym artykule jest również dostępny do [maszyn wirtualnych systemu Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W **Resource Manager** model wdrażania, punkty końcowe są skonfigurowane przy użyciu **grup zabezpieczeń sieci (NSG)**. Aby uzyskać więcej informacji, zobacz [dostęp do zewnętrznych sieci maszyny Wirtualnej przy użyciu portalu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Po utworzeniu maszyny wirtualnej systemu Windows w portalu Azure, typowe punkty końcowe, takich jak te dotyczące usług pulpitu zdalnego i komunikacji zdalnej programu Windows PowerShell są zazwyczaj tworzone automatycznie. Dodatkowe punkty końcowe można skonfigurować podczas tworzenia maszyny wirtualnej lub po jego zgodnie z potrzebami.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby użyć polecenia cmdlet programu Azure PowerShell do konfigurowania punktu końcowego maszyny Wirtualnej, zobacz [AzureEndpoint Dodaj](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Aby użyć polecenia cmdlet programu Azure PowerShell do zarządzania listy ACL punktu końcowego, zobacz [Zarządzanie listy kontroli dostępu (ACL) dla punktów końcowych przy użyciu programu PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Jeśli utworzono maszynę wirtualną w modelu wdrażania usługi Resource Manager, możesz użyć programu Azure PowerShell do [Utwórz grupy zabezpieczeń sieci](../../../virtual-network/tutorial-filter-network-traffic.md) sterujących ruchem do maszyny Wirtualnej.
