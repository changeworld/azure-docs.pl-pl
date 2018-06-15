---
title: Społeczności tools — zasoby klasyczne przeniesienia do usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule kataloguje narzędzia, które zostały opublikowane za pomogą migracji zasobów IaaS z klasycznego modelu wdrażania usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
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
ms.openlocfilehash: cce1906e75646f2fb9ea30842e968d14830f3497
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913940"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Narzędzia społeczności związane z migracją zasobów IaaS od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager
W tym artykule kataloguje narzędzia, które zostały dołączone przez społeczność ułatwiające migrację zasobów IaaS z klasycznego modelu wdrażania usługi Azure Resource Manager.

> [!NOTE]
> Oficjalnie tych narzędzi nie są obsługiwane przez Microsoft Support. W związku z tym są otwarte powierzając jej ich konserwację w serwisie GitHub i chętnie zaakceptować PRs poprawki lub dodatkowych scenariuszach. Aby zgłosić problem, należy użyć funkcji problemów GitHub.
> 
> Migrowanie z tych narzędzi spowoduje, że przestój klasyczne maszyny wirtualnej. Jeśli szukasz migracji z obsługiwanych platform, odwiedź 
> 
>   * [Platformy obsługiwane migracji zasobów IaaS ze środowiska klasycznego do stosu usługi Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Techniczne szczegółowe informacje na temat platformy obsługiwana migracja z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu programu Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Jest to zestaw narzędzi pomocnika utworzona w ramach migracji przedsiębiorstwa z zarządzania usługą Azure do usługi Azure Resource Manager. To narzędzie umożliwia replikowanie infrastruktury do innej subskrypcji, która może służyć do testowania migracji i żelaza ewentualnych problemów przed uruchomieniem migracji w ramach subskrypcji w środowisku produkcyjnym.

[Połącz się z dokumentacją narzędzia](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz jest dodatkową opcję migracji kompletny zestaw zasobów IaaS klasycznych zasobów IaaS Menedżera zasobów Azure. Migracja może wystąpić w ramach tej samej subskrypcji lub różnych subskrypcji i rodzajów subskrypcji (np: dostawcy usług Kryptograficznych subskrypcji).

[Połącz się z dokumentacją narzędzia](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager obsługiwane platformy](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migrowania zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

