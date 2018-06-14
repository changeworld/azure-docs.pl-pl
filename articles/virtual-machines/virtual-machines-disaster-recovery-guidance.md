---
title: Scenariusze odzyskiwania po awarii dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku, gdy przerw w działaniu usługi Azure ma wpływ na maszynach wirtualnych platformy Azure.
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
ms.openlocfilehash: 70aec41c885ab81371f5318f7557b0e628ac3308
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915419"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Co robić w przypadku, gdy przerw w działaniu usługi Azure ma wpływ na maszynach wirtualnych platformy Azure
W firmie Microsoft firma Microsoft nad tym, aby upewnić się, że naszych usług są zawsze dostępne dla Ciebie przydatne. Wymusza naszych niezależnych czasami wpływ nam w sposób, aby spowodować zakłócenia w świadczeniu usług nieplanowane.

Firma Microsoft udostępnia Umowa dotycząca poziomu usług (SLA) dla swoich usług jako zobowiązanie czas pracy i łączności. Umowa SLA dla poszczególnych usług Azure można znaleźć w folderze [umowy dotyczące poziomu usług Azure](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele wbudowanych funkcji obsługujących aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji o tych usług, przeczytaj [odzyskiwania po awarii i wysoką dostępność aplikacji Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

W tym artykule omówiono scenariusza odzyskiwania awaryjnego wartość true, gdy całego regionu ulegnie awarii z powodu poważne klęski lub powszechnie przerw w obsłudze. Są to rzadkie wystąpienia, ale należy przygotować dla jest awarii całego regionu. Całego regionu napotyka przerw w działaniu usługi, Magazyn lokalnie nadmiarowy kopie danych tymczasowo będą niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob magazynu Azure i tabele są przechowywane w innym regionie. W przypadku ukończenia regionalnej awarii lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania Azure, ponownie mapuje wszystkie wpisy DNS do regionu replikacją geograficzną.

Aby ułatwić obsługę tych zdarzeń w rzadkich, udostępniamy następujące wskazówki dla maszyn wirtualnych platformy Azure w przypadku przerw w działaniu usługi dla całego regionu, w której wdrażana jest aplikacja maszyny wirtualnej platformy Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opcja 1: Zainicjuj tryb failover przy użyciu usługi Azure Site Recovery
Można skonfigurować usługi Azure Site Recovery dla maszyn wirtualnych, dzięki czemu będzie można odzyskać aplikacji za pomocą jednego kliknięcia w kilku minut. Można replikować do regionu Azure wybranych przez użytkownika i nie jest ograniczona do sparowanego regionów. Możesz rozpocząć pracę przez [replikowanie maszyn wirtualnych](https://aka.ms/a2a-getting-started). Możesz [Tworzenie planu odzyskiwania](../site-recovery/site-recovery-create-recovery-plans.md) tak, aby zautomatyzować całej pracy awaryjnej dla aplikacji. Możesz [testy z trybu failover](../site-recovery/site-recovery-test-failover-to-azure.md) uprzednio bez wpływu na aplikacji produkcyjnej lub trwającej replikacji. W przypadku przerwania regionu podstawowego, wystarczy [Zainicjuj tryb failover](../site-recovery/site-recovery-failover.md) i aplikacji w docelowym regionie.


## <a name="option-2-wait-for-recovery"></a>Opcja 2: Poczekaj, aż odzyskiwanie
W takim przypadku jest wymagana żadna akcja ze strony użytkownika. Wiedzieć, że firma Microsoft pracuje dokładnie do przywrócenia dostępności usługi. Możesz wyświetlać bieżący stan usługi na naszych [pulpit nawigacyjny kondycji usługi Azure](https://azure.microsoft.com/status/).

Jest najlepszym rozwiązaniem, jeśli nie skonfigurowano usługi Azure Site Recovery, dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu lub magazynu geograficznie nadmiarowego przed zakłóceń. Jeśli zdefiniowano magazynu geograficznie nadmiarowego lub magazynu geograficznie nadmiarowego dostęp do odczytu dla konta magazynu przechowywania maszyny Wirtualnej wirtualnego dysku twardego (VHD), może wyglądać odzyskać obraz podstawowy dysk VHD i spróbuj udostępnienia nowej maszyny Wirtualnej z niego. To nie jest preferowaną opcję, ponieważ nie ma żadnych gwarancji synchronizacji danych. W związku z tym ta opcja nie jest gwarantowana do pracy.


> [!NOTE]
> Należy pamiętać, że nie masz żadnych kontrolę nad ten proces i będą występować tylko dla zakłócenia w świadczeniu usług całej regionu. W związku z tym musi również polegać na innych strategii tworzenia kopii zapasowych specyficzne dla aplikacji do osiągnięcia najwyższy poziom dostępności. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [danych strategii odzyskiwania po awarii](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Kolejne kroki

- Uruchom [ochrona aplikacji uruchomionych na maszynach wirtualnych Azure](https://aka.ms/a2a-getting-started) przy użyciu usługi Azure Site Recovery

- Aby dowiedzieć się więcej na temat sposobu wdrażania odzyskiwania po awarii i strategii wysokiej dostępności, zobacz [odzyskiwania po awarii i wysoką dostępność aplikacji Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Aby opracować techniczne szczegółowy opis możliwości platformy chmury, zobacz [wskazówki techniczne Azure odporności](../resiliency/resiliency-technical-guidance.md).


- Jeśli są zgodnie z instrukcjami, usuń zaznaczenie, albo jeśli chcesz wykonywać operacje, w imieniu użytkownika przez firmę Microsoft, skontaktuj się z [techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
