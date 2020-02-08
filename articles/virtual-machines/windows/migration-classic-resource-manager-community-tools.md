---
title: Narzędzia społeczności — przenoszenie klasycznych zasobów do Azure Resource Manager
description: W tym artykule opisano narzędzia dostarczone przez społeczność w celu ułatwienia migracji zasobów IaaS z klasycznej do modelu wdrażania Azure Resource Manager.
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
ms.openlocfilehash: 5688854404ca6d3e64ca6d59276628d08bbf3ebe
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086008"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Narzędzia społeczności do migrowania zasobów IaaS z klasycznego do Azure Resource Manager
W tym artykule opisano narzędzia dostarczone przez społeczność, które ułatwiają migrację zasobów IaaS z klasycznej do modelu wdrażania Azure Resource Manager.

> [!NOTE]
> Te narzędzia nie są oficjalnie obsługiwane przez pomoc techniczna firmy Microsoft. W związku z tym są one otwarte w serwisie GitHub, a firma Microsoft chętnie zaakceptuje żądań ściągnięcia w zakresie poprawek lub dodatkowych scenariuszy. Aby zgłosić problem, użyj funkcji problemy w witrynie GitHub.
> 
> Migrowanie za pomocą tych narzędzi spowoduje przestoje dla klasycznej maszyny wirtualnej. Jeśli szukasz migracji obsługiwanej przez platformę, odwiedź stronę 
> 
>   * [Migracja IaaS zasobów z platformy klasycznej do Azure Resource Manager stosu](migration-classic-resource-manager-overview.md)
>   * [Szczegółowa szczegółowe na platformie obsługiwana migracja z klasycznej do Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager przy użyciu Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Jest to kolekcja narzędzi pomocniczych utworzonych w ramach migracji przedsiębiorstwa z usługi Azure Service Management do Azure Resource Manager. To narzędzie pozwala replikować infrastrukturę do innej subskrypcji, która może służyć do testowania migracji i wypróbować wszelkie problemy przed uruchomieniem migracji w ramach subskrypcji produkcyjnej.

[Link do dokumentacji narzędziowej](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz to dodatkowa opcja migracji kompletnego zestawu klasycznych zasobów IaaS do Azure Resource Manager zasobów IaaS. Migracja może odbywać się w ramach tej samej subskrypcji lub między różnymi subskrypcjami i typami subskrypcji (np. subskrypcjami CSP).

[Link do dokumentacji narzędziowej](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

