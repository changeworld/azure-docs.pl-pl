---
title: Scenariusze odzyskiwania po awarii dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku, gdy przerw w działaniu usługi platformy Azure ma wpływ na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc71e8564b35f4fdd4153a04c66a3d8c5df88c30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478848"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Co należy zrobić w przypadku, gdy przerw w działaniu usługi platformy Azure ma wpływ na maszynach wirtualnych platformy Azure
W firmie Microsoft pracujemy nad upewnij się, że nasze usługi są zawsze dostępne dla Ciebie gdy ich potrzebujesz. Wymusza naszych niezależnych czasami wpływu na nas w sposób, aby spowodować przerwy w świadczeniu usług nieplanowane.

Firma Microsoft oferuje umowę poziomu usług (SLA) jej usług jako zobowiązanie dotyczące czasu dostępności i łączności. Umowa SLA dla poszczególnych usług platformy Azure znajduje się w temacie [umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele funkcji wbudowanych platformy, które obsługują aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji o tych usług, przeczytaj [odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

W tym artykule opisano true scenariusza odzyskiwania po awarii, w razie awarii z powodu głównych klęski żywiołowe lub przerw w działaniu usługi powszechne całego regionu. Są to rzadko, ale muszą być przygotowane na możliwość, że występuje awaria cały region. Jeśli cały region przerw w działaniu usługi, lokalnie nadmiarowe kopie danych będzie tymczasowo niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob usługi Azure Storage i tabel są przechowywane w innym regionie. W przypadku pełną regionalnej awarii lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania Azure, ponownie mapuje wszystkie wpisy DNS dla regionu z replikacją geograficzną.

Aby obsługiwać te rzadko, firma Microsoft zapewnia następujące wskazówki dotyczące maszyn wirtualnych platformy Azure w przypadku przerwy w działaniu usługi dla całego regionu, w którym wdrożono aplikację maszyny wirtualnej platformy Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opcja 1: Zainicjuj tryb failover przy użyciu usługi Azure Site Recovery
Można skonfigurować usługi Azure Site Recovery dla maszyn wirtualnych, dzięki czemu będzie można odzyskać aplikacji za pomocą jednego kliknięcia w ciągu kilku minut. Można replikować do platformy Azure wybranym przez siebie regionie, a nie ogranicza się do połączonych w parę regionów. Możesz rozpocząć od [replikowanie maszyn wirtualnych](https://aka.ms/a2a-getting-started). Możesz [utworzyć plan odzyskiwania](../site-recovery/site-recovery-create-recovery-plans.md) tak, aby zautomatyzować trybu failover w całej aplikacji. Możesz [testy z trybu failover](../site-recovery/site-recovery-test-failover-to-azure.md) uprzednio bez wywierania wpływu na aplikację w środowisku produkcyjnym lub trwającej replikacji. W przypadku przerwania regionu podstawowego, właśnie [zainicjować trybu failover](../site-recovery/site-recovery-failover.md) i utworzyć aplikację w regionie docelowym.


## <a name="option-2-wait-for-recovery"></a>Opcja 2: Poczekaj, aż odzyskiwanie
W takim wypadku żadna akcja ze strony użytkownika jest wymagana. Wiadomo, że pracujemy, aby przywrócić dostępność usługi. Można wyświetlić bieżący stan usługi na naszych [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/).

To najlepsza opcja, jeśli nie skonfigurowano usługi Azure Site Recovery, Magazyn geograficznie nadmiarowy geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy przed zakłócenie. Jeśli skonfigurowano Magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy dostęp do odczytu dla konta magazynu przechowywania wirtualnych dysków twardych maszyny Wirtualnej (VHD), możesz się przyjrzeć w celu odzyskania na podstawowy obraz wirtualnego dysku twardego i spróbuj do aprowizacji nowej maszyny Wirtualnej z niego. Nie jest preferowaną opcją, ponieważ nie ma żadnej gwarancji synchronizacji danych. W związku z tym ta opcja nie jest gwarantowane do pracy.


> [!NOTE]
> Należy pamiętać, że nie masz żadnych kontrolować ten proces i miało miejsce tylko dla przerwy w świadczeniu usług obejmujących cały region. W związku z tym należy również polegać na innych strategii tworzenia kopii zapasowych specyficzne dla aplikacji, aby osiągnąć najwyższą dostępność. Aby uzyskać więcej informacji, zobacz sekcję na [danych Strategie odzyskiwania po awarii](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Kolejne kroki

- Rozpocznij [ochronę Twoich aplikacji uruchomionych na maszynach wirtualnych Azure](https://aka.ms/a2a-getting-started) przy użyciu usługi Azure Site Recovery

- Aby dowiedzieć się więcej na temat sposobu wdrażania odzyskiwania po awarii i strategii wysokiej dostępności, zobacz [odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Aby opracować szczegółowe technicznego zrozumienia możliwości platformy chmury, zobacz [wskazówek technicznych odporność platformy Azure](../resiliency/resiliency-technical-guidance.md).


- Jeśli wyczyść lub jeśli chcesz wykonywać operacje w imieniu użytkownika przez firmę Microsoft, skontaktuj się z instrukcjami [techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
