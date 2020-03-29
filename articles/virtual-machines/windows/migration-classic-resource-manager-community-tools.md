---
title: Narzędzia społeczności — przenoszenie zasobów klasycznych do usługi Azure Resource Manager
description: W tym artykule przedstawiono katalogi narzędzi dostarczonych przez społeczność w celu ułatwienia migracji zasobów usługi IaaS z klasycznego do modelu wdrażania usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f4298af05f021f0a9579a9837308be5d15a3c14f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915405"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Narzędzia społeczności do migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager

> [!IMPORTANT]
> Obecnie około 90% maszyn wirtualnych IaaS korzysta z [usługi Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Od 28 lutego 2020 r. klasyczne maszyny wirtualne zostały przestarzałe i zostaną całkowicie wycofane 1 marca 2023 r. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym uszczukaniu i [o tym, jak wpływa na Ciebie](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

W tym artykule przedstawiono katalogi narzędzi, które zostały dostarczone przez społeczność, aby pomóc w migracji zasobów IaaS z klasycznego do modelu wdrażania usługi Azure Resource Manager.

> [!NOTE]
> Narzędzia te nie są oficjalnie obsługiwane przez pomoc techniczną firmy Microsoft. W związku z tym są one open source na GitHub i jesteśmy szczęśliwi, aby zaakceptować prs dla poprawek lub dodatkowych scenariuszy. Aby zgłosić problem, użyj funkcji problemów z githubem.
> 
> Migracja za pomocą tych narzędzi spowoduje przestoje klasycznej maszyny wirtualnej. Jeśli szukasz migracji obsługiwanej przez platformę, odwiedź stronę 
> 
>   * [Obsługiwana przez platformę migracja zasobów IaaS z klasycznego do stosu usługi Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Głęboka kontrola technicznego na platformie obsługiwała migrację z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrowanie zasobów IaaS z klasycznego do usługi Azure Resource Manager przy użyciu usługi Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Jest to zbiór narzędzi pomocniczych utworzonych w ramach migracji w przedsiębiorstwie z usługi Azure Service Management do usługi Azure Resource Manager. To narzędzie umożliwia replikowanie infrastruktury do innej subskrypcji, która może służyć do testowania migracji i rozwiązać wszelkie problemy przed uruchomieniem migracji w subskrypcji produkcyjnej.

[Łącze do dokumentacji narzędzia](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz to dodatkowa opcja migracji pełnego zestawu klasycznych zasobów IaaS do zasobów Usługi Azure Resource Manager IaaS. Migracja może wystąpić w ramach tej samej subskrypcji lub między różnymi subskrypcjami i typami subskrypcji (np. subskrypcje CSP).

[Łącze do dokumentacji narzędzia](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

