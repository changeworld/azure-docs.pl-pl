---
title: Narzędzia społeczności — przenoszenie zasobów klasycznych do usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule Skatalogowano narzędzia udostępnione przez społeczność, aby ułatwić migrację zasobów IaaS od modelu klasycznego do modelu wdrażania usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 63e1ad044204bf7695d274fa46f06523fd9d460f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720283"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Narzędzia społeczności związane z migracją zasobów IaaS od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager
W tym artykule Skatalogowano narzędzia udostępnione przez społeczność ułatwiających migrację zasobów IaaS z modelu klasycznego do modelu wdrażania usługi Azure Resource Manager.

> [!NOTE]
> Narzędzia te nie są oficjalnie obsługiwane przez Microsoft Support. W związku z tym są open source w serwisie GitHub i przekażemy do akceptowania żądań ściągnięcia dla poprawki lub dodatkowych scenariuszy. Aby zgłosić problem, należy użyć funkcji problemy usługi GitHub.
> 
> Migrowanie za pomocą tych narzędzi spowoduje, że przestój klasycznej maszyny wirtualnej. Jeśli szukasz migracja platformy obsługiwane odwiedź 
> 
>   * [Platformy obsługiwane migracji zasobów rozwiązania IaaS z modelu klasycznego do stosu usługi Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Rozbudowana technicznie na platformie obsługiwana migracja z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Jest to zestaw narzędzi pomocnika utworzonego w ramach migracji enterprise z usługą Azure Service Management do usługi Azure Resource Manager. To narzędzie umożliwia replikowanie infrastruktury do innej subskrypcji, która może służyć do testowania migracji i żelaza się wszelkie problemy przed uruchomieniem migracji subskrypcji produkcyjnych.

[Link do dokumentacji dotyczącej narzędzia](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz jest dodatkową opcję migracji kompletny zestaw klasycznych zasobów IaaS do zasobów z infrastrukturą IaaS usługi Azure Resource Manager. Migracja może wystąpić w ramach tej samej subskrypcji lub między różnymi subskrypcjami i typów subskrypcji (np: Subskrypcje programu CSP).

[Link do dokumentacji dotyczącej narzędzia](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji obsługiwanej przez platformę zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia umożliwia migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj często zadawane pytania dotyczące migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

