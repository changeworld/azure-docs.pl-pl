---
title: Scenariusze odzyskiwania po awarii
description: Dowiedz się, co należy zrobić w przypadku, gdy zakłócenie usługi platformy Azure wpływa na maszyny wirtualne platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: f2dc43e1f07d449bf2f8ed39ce4523c99b551dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115616"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Co zrobić, jeśli zakłócenie usługi platformy Azure wpływa na maszyny wirtualne platformy Azure
W firmie Microsoft dokładamy starań, aby nasze usługi były zawsze dostępne, gdy ich potrzebujesz. Siły pozostające poza naszą kontrolą czasami wpływają na nas w sposób, który powoduje nieplanowane zakłócenia w świadczeniu usług.

Firma Microsoft zapewnia umowę dotyczącą poziomu usług (SLA) dotyczącą swoich usług jako zobowiązanie do dostępności i łączności. Umowa SLA dla poszczególnych usług platformy Azure znajduje się w [witrynie Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele wbudowanych funkcji platformy, które obsługują aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji na temat tych usług, przeczytaj [odzyskiwanie po awarii i wysoką dostępność dla aplikacji platformy Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

W tym artykule opisano scenariusz odzyskiwania po awarii, gdy cały region doświadcza awarii z powodu poważnej klęski żywiołowej lub powszechnej przerwy w świadczeniu usług. Są to rzadkie zdarzenia, ale należy przygotować się na możliwość wystąpienia awarii całego regionu. Jeśli w całym regionie wystąpi zakłócenie usługi, lokalnie nadmiarowe kopie danych będą tymczasowo niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob i tabel usługi Azure Storage są przechowywane w innym regionie. W przypadku całkowitej awarii regionalnej lub awarii, w której regionu podstawowego nie można odzyskać, platforma Azure ponownie mapuje wszystkie wpisy DNS do regionu replikowanego geograficznie.

Aby ułatwić obsługę tych rzadkich wystąpień, firma Microsoft zapewnia następujące wskazówki dotyczące maszyn wirtualnych platformy Azure w przypadku zakłócenia usługi w całym regionie, w którym jest wdrażana aplikacja maszyny wirtualnej platformy Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opcja 1: Inicjowanie pracy awaryjnej przy użyciu usługi Azure Site Recovery
Można skonfigurować usługę Azure Site Recovery dla maszyn wirtualnych, dzięki czemu można odzyskać aplikację za pomocą jednego kliknięcia w ciągu kilku minut. Można replikować do wybranego regionu platformy Azure i nie ogranicza się do sparowanych regionów. Można rozpocząć, [replikując maszyny wirtualne](https://aka.ms/a2a-getting-started). Można [utworzyć plan odzyskiwania,](../site-recovery/site-recovery-create-recovery-plans.md) dzięki czemu można zautomatyzować cały proces pracy awaryjnej dla aplikacji. Można [wcześniej przetestować swoje prace awaryjne](../site-recovery/site-recovery-test-failover-to-azure.md) bez wpływu na aplikację produkcyjną lub trwającą replikację. W przypadku zakłócenia regionu podstawowego wystarczy [zainicjować pracy awaryjnej](../site-recovery/site-recovery-failover.md) i przenieść aplikację w regionie docelowym.


## <a name="option-2-wait-for-recovery"></a>Opcja 2: Poczekaj na powrót do zdrowia
W takim przypadku nie jest wymagane żadne działanie z Twojej strony. Wiedz, że pilnie pracujemy nad przywróceniem dostępności usług. Bieżący stan usługi można zobaczyć na naszym [pulpicie nawigacyjnym usługi Azure Service Health.](https://azure.microsoft.com/status/)

Jest to najlepsze rozwiązanie, jeśli nie skonfigurowano usługi Azure Site Recovery, magazynu geograficznego dostępu do odczytu lub magazynu geograficznie nadmiarowego przed zakłóceniem. Jeśli skonfigurowano magazyn geograficznie nadmiarowy lub dostęp do odczytu magazynu geograficznego nadmiarowego dla konta magazynu, na którym są przechowywane wirtualne dyski twarde maszyny Wirtualnej (VHD), można spojrzeć na odzyskanie dysku wirtualnego obrazu podstawowego i spróbować aprowizować nową maszynę wirtualną z niego. Nie jest to preferowana opcja, ponieważ nie ma gwarancji synchronizacji danych. W związku z tym ta opcja nie jest gwarantowana do pracy.


> [!NOTE]
> Należy pamiętać, że nie masz żadnej kontroli nad tym procesem i będzie występować tylko w przypadku przerw w świadczeniu usług w całym regionie. Z tego powodu należy również polegać na innych strategii tworzenia kopii zapasowych specyficznych dla aplikacji, aby osiągnąć najwyższy poziom dostępności. Aby uzyskać więcej informacji, zobacz sekcję [Dotyczącą strategii odzyskiwania danych po awarii](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Następne kroki

- [Rozpocznij ochronę aplikacji uruchomionych na maszynach wirtualnych platformy Azure](https://aka.ms/a2a-getting-started) przy użyciu usługi Azure Site Recovery

- Aby dowiedzieć się więcej o tym, jak zaimplementować strategię odzyskiwania po awarii i wysokiej dostępności, zobacz [Odzyskiwanie po awarii i wysoka dostępność dla aplikacji platformy Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

- Aby opracować szczegółową wiedzę techniczną na temat możliwości platformy w chmurze, zobacz [wskazówki techniczne dotyczące odporności platformy Azure.](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)


- Jeśli instrukcje nie są jasne lub jeśli chcesz, aby firma Microsoft zrobiła operacje w jego imieniu, skontaktuj się z [działem obsługi klienta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
