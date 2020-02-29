---
title: Klasyczny do Azure Resource Manager migracji Technical szczegółowe
description: Szczegółowee techniczne głębokiej migracji zasobów z klasycznego modelu wdrażania do Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 75732cf58a68948bf225e15e9227a3fa7592e738
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914929"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Szczegółowee techniczne głębokiej migracji obsługiwanej przez platformę z wersji klasycznej do Azure Resource Manager

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Przechodźmy głębokie szczegółowe na Migrowanie z klasycznego modelu wdrażania platformy Azure do modelu wdrażania Azure Resource Manager. Zapoznaj się z zasobami na poziomie zasobów i funkcji, aby ułatwić zrozumienie, w jaki sposób platforma Azure migruje zasoby między dwoma modelami wdrażania. Aby uzyskać więcej informacji, przeczytaj artykuł dotyczący anonsu dotyczącego usługi: [obsługiwana przez platform migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
