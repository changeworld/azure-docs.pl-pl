---
title: Głębokie głębokie nurkowanie techniczne związane z migracją klasyczną do usługi Azure Resource Manager
description: Głębokie głębokie nurkowanie techniczne w zakresie migracji zasobów obsługiwanych przez platformę z klasycznego modelu wdrażania do usługi Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: a5277e23d92dd026aa19e278532869747709e646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944731"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager

> [!IMPORTANT]
> Obecnie około 90% maszyn wirtualnych IaaS korzysta z [usługi Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Od 28 lutego 2020 r. klasyczne maszyny wirtualne zostały przestarzałe i zostaną całkowicie wycofane 1 marca 2023 r. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym uszczukaniu i [o tym, jak wpływa na Ciebie](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Przejdźmy do głębokiego zagłębienia w migrację z klasycznego modelu wdrażania platformy Azure do modelu wdrażania usługi Azure Resource Manager. Przyjrzyjmy się zasobom na poziomie zasobów i funkcji, aby ułatwić zrozumienie, jak platforma Azure migruje zasoby między dwoma modelami wdrażania. Aby uzyskać więcej informacji, przeczytaj artykuł anonsu usługi: [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Narzędzia społecznościowe pomagające w migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
