---
title: Wycofujemy klasyczne maszyny wirtualne platformy Azure 1 marca 2023 r.
description: Artykuł zawiera ogólny przegląd klasycznej emerytury maszyn wirtualnych
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668807"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrowanie zasobów usługi IaaS do usługi Azure Resource Manager do 1 marca 2023 r. 

W 2014 r. uruchomiliśmy usługę IaaS w usłudze Azure Resource Manager i od tego czasu zwiększamy możliwości. Ponieważ [usługa Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ma teraz pełne możliwości IaaS i inne ulepszenia, przestarzałe zarządzanie maszynami wirtualnymi IaaS za pośrednictwem usługi Azure Service Manager w dniu 28 lutego 2020 r., a ta funkcja zostanie całkowicie wycofana 1 marca 2023 r. 

Obecnie około 90% maszyn wirtualnych IaaS korzysta z usługi Azure Resource Manager. Jeśli korzystasz z zasobów IaaS za pośrednictwem usługi Azure Service Manager (ASM), zacznij planować migrację już teraz i ukończ ją do 1 marca 2023 r., aby skorzystać z [usługi Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/management/)

Klasyczne maszyny wirtualne będą zgodne z [zasadami nowoczesnego cyklu życia](https://support.microsoft.com/help/30881/modern-lifecycle-policy) w celu przejścia na emeryturę.

## <a name="how-does-this-affect-me"></a>Jak to wpłynie na mnie? 

1) Od 28 lutego 2020 r. klienci, którzy nie korzystali z maszyn wirtualnych IaaS za pośrednictwem usługi Azure Service Manager (ASM) w lutym 2020 r., nie będą już mogli tworzyć klasycznych maszyn wirtualnych. 
2) W dniu 1 marca 2023 r. klienci nie będą już mogli uruchamiać maszyn wirtualnych IaaS przy użyciu usługi Azure Service Manager, a wszystkie, które są nadal uruchomione lub przydzielone, zostaną zatrzymane i cofnięte. 
2) W dniu 1 marca 2023 r. subskrypcje, które nie zostały zmigrowane do usługi Azure Resource Manager, zostaną poinformowane o osi czasu usuwania pozostałych klasycznych maszyn wirtualnych.  

Na następujące usługi i funkcje platformy Azure **NIE** będzie miało wpływu ta emerytura: 
- Cloud Services 
- Konta magazynu **nie** używane przez klasyczne maszyny wirtualne 
- Sieci wirtualne (sieci wirtualne) **nie** używane przez klasyczne maszyny wirtualne. 
- Inne zasoby klasyczne

## <a name="what-actions-should-i-take"></a>Jakie działania należy podjąć? 

- Zacznij planować migrację do usługi Azure Resource Manager już dziś. 

- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) o migrowaniu klasycznych maszyn wirtualnych [z systemem Linux](./linux/migration-classic-resource-manager-plan.md) i [Windows](./windows/migration-classic-resource-manager-plan.md) do usługi Azure Resource Manager.

- Aby uzyskać więcej informacji, zapoznaj się z [często zadawanymi pytaniami dotyczącymi migracji klasycznej do usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- W przypadku pytań technicznych, problemów i subskrypcji na białej liście [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- W przypadku innych pytań, które nie są częścią faq i opinii, komentarz poniżej.
