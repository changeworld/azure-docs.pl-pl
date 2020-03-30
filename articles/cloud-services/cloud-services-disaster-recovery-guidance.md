---
title: Obsługa zakłóceń usługi platformy Azure, które mają wpływ na usługi w chmurze Azure
titleSuffix: Azure Cloud Services
description: Dowiedz się, co należy zrobić w przypadku zakłóceń w działaniu usługi platformy Azure, które mają wpływ na usługi w chmurze platformy Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157511"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co zrobić w przypadku zakłóceń w działaniu usługi platformy Azure, które mają wpływ na usługi w chmurze Azure
W firmie Microsoft dokładamy starań, aby nasze usługi były zawsze dostępne, gdy ich potrzebujesz. Siły pozostające poza naszą kontrolą czasami wpływają na nas w sposób, który powoduje nieplanowane zakłócenia w świadczeniu usług.

Firma Microsoft zapewnia umowę dotyczącą poziomu usług (SLA) dotyczącą swoich usług jako zobowiązanie do dostępności i łączności. Umowa SLA dla poszczególnych usług platformy Azure znajduje się w [witrynie Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele wbudowanych funkcji platformy, które obsługują aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji na temat tych usług, przeczytaj [odzyskiwanie po awarii i wysoką dostępność dla aplikacji platformy Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

W tym artykule opisano scenariusz odzyskiwania po awarii, gdy cały region doświadcza awarii z powodu poważnej klęski żywiołowej lub powszechnej przerwy w świadczeniu usług. Są to rzadkie zdarzenia, ale należy przygotować się na możliwość wystąpienia awarii całego regionu. Jeśli w całym regionie wystąpi zakłócenie usługi, lokalnie nadmiarowe kopie danych będą tymczasowo niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob i tabel usługi Azure Storage są przechowywane w innym regionie. W przypadku całkowitej awarii regionalnej lub awarii, w której regionu podstawowego nie można odzyskać, platforma Azure ponownie mapuje wszystkie wpisy DNS do regionu replikowanego geograficznie.

> [!NOTE]
> Należy pamiętać, że nie masz żadnej kontroli nad tym procesem i wystąpi tylko w przypadku przerw w działaniu usługi w całym centrum danych. Z tego powodu należy również polegać na innych strategii tworzenia kopii zapasowych specyficznych dla aplikacji, aby osiągnąć najwyższy poziom dostępności. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii i wysoka dostępność aplikacji utworzonych na platformie Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Jeśli chcesz mieć wpływ na własne pracy awaryjnej, warto rozważyć użycie magazynu geograficznego dostępu do [odczytu (RA-GRS)](../storage/common/storage-redundancy.md), który tworzy kopię tylko do odczytu danych w innym regionie.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opcja 1: Użyj wdrożenia kopii zapasowej za pośrednictwem usługi Azure Traffic Manager
Najbardziej niezawodne rozwiązanie do odzyskiwania po awarii obejmuje utrzymywanie wielu wdrożeń aplikacji w różnych regionach, a następnie używanie [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) do kierowania ruchu między nimi. Usługa Azure Traffic Manager udostępnia wiele [metod routingu,](../traffic-manager/traffic-manager-routing-methods.md)dzięki czemu można wybrać, czy zarządzać wdrożeniami przy użyciu modelu podstawowego/kopii zapasowej, czy podzielić ruch między nimi.

![Równoważenie usług w chmurze azure w różnych regionach za pomocą usługi Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Aby uzyskać najszybszą reakcję na utratę regionu, należy skonfigurować [monitorowanie punktu końcowego](../traffic-manager/traffic-manager-monitoring.md)usługi Traffic Manager.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opcja 2: Wdrażanie aplikacji w nowym regionie
Utrzymywanie wielu aktywnych wdrożeń zgodnie z opisem w poprzedniej opcji wiąże się z dodatkowymi kosztami bieżącymi. Jeśli cel czasu odzyskiwania (RTO) jest wystarczająco elastyczny i masz oryginalny kod lub skompilowany pakiet usług w chmurze, możesz utworzyć nowe wystąpienie aplikacji w innym regionie i zaktualizować rekordy DNS, aby wskazywały nowe wdrożenie.

Aby uzyskać więcej informacji na temat tworzenia i wdrażania aplikacji usługi w chmurze, zobacz [Jak utworzyć i wdrożyć usługę w chmurze.](cloud-services-how-to-create-deploy-portal.md)

W zależności od źródeł danych aplikacji może być konieczne sprawdzenie procedur odzyskiwania dla źródła danych aplikacji.

* W przypadku źródeł danych usługi Azure Storage zobacz [Nadmiarowość usługi Azure Storage,](../storage/common/storage-redundancy.md) aby sprawdzić opcje, które są dostępne na podstawie wybranego modelu nadmiarowości dla aplikacji.
* W przypadku źródeł bazy danych SQL przeczytaj [omówienie: Ciągłość działania chmury i odzyskiwanie po awarii bazy danych z bazą danych SQL,](../sql-database/sql-database-business-continuity.md) aby sprawdzić opcje, które są dostępne na podstawie wybranego modelu replikacji dla aplikacji.


## <a name="option-3-wait-for-recovery"></a>Opcja 3: Poczekaj na powrót do zdrowia
W takim przypadku nie jest wymagana żadna akcja z Twojej strony, ale usługa będzie niedostępna do czasu przywrócenia regionu. Bieżący stan usługi można zobaczyć na [pulpicie nawigacyjnym kondycji usługi Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, jak zaimplementować strategię odzyskiwania po awarii i wysokiej dostępności, zobacz [Odzyskiwanie po awarii i wysoka dostępność dla aplikacji platformy Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

Aby opracować szczegółową wiedzę techniczną na temat możliwości platformy w chmurze, zobacz [wskazówki techniczne dotyczące odporności platformy Azure.](/azure/architecture/checklist/resiliency-per-service)