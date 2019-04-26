---
title: Co należy zrobić w przypadku platformy Azure usługa przerw w działaniu, która ma wpływ na usługach Azure Cloud Services | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku przerwania działania usługi platformy Azure, która ma wpływ na usługach Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mmccrory
ms.openlocfilehash: 4b355a779a2e9f78f4cbf8ed5425200ce1df2f1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337281"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co należy zrobić w przypadku platformy Azure usługa przerw w działaniu, która ma wpływ na usługach Azure Cloud Services
W firmie Microsoft pracujemy nad upewnij się, że nasze usługi są zawsze dostępne dla Ciebie gdy ich potrzebujesz. Wymusza naszych niezależnych czasami wpływu na nas w sposób, aby spowodować przerwy w świadczeniu usług nieplanowane.

Firma Microsoft oferuje umowę poziomu usług (SLA) jej usług jako zobowiązanie dotyczące czasu dostępności i łączności. Umowa SLA dla poszczególnych usług platformy Azure znajduje się w temacie [umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele funkcji wbudowanych platformy, które obsługują aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji o tych usług, przeczytaj [odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

W tym artykule opisano true scenariusza odzyskiwania po awarii, w razie awarii z powodu głównych klęski żywiołowe lub przerw w działaniu usługi powszechne całego regionu. Są to rzadko, ale muszą być przygotowane na możliwość, że występuje awaria cały region. Jeśli cały region przerw w działaniu usługi, lokalnie nadmiarowe kopie danych będzie tymczasowo niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob usługi Azure Storage i tabel są przechowywane w innym regionie. W przypadku pełną regionalnej awarii lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania Azure, ponownie mapuje wszystkie wpisy DNS dla regionu z replikacją geograficzną.

> [!NOTE]
> Należy pamiętać, że nie masz żadnych kontrolować ten proces i miało miejsce tylko dla przerwy w świadczeniu usług całego centrum danych. W związku z tym należy również polegać na innych strategii tworzenia kopii zapasowych specyficzne dla aplikacji, aby osiągnąć najwyższą dostępność. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii i wysoka dostępność aplikacji utworzonych w systemie Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Jeśli chcesz można było mieć wpływ na własnych trybu failover, warto rozważyć użycie [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), która tworzy kopię tylko do odczytu danych w innym regionie.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opcja 1: Przy użyciu kopii zapasowej wdrożenia za pomocą usługi Azure Traffic Manager
Najbardziej niezawodne rozwiązanie odzyskiwania po awarii obejmuje obsługi wielu wdrożeń będących częścią Twojej aplikacji w różnych regionach, a następnie za pomocą [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) do kierowania ruchem między nimi. Usługa Azure Traffic Manager udostępnia wiele [metod routingu](../traffic-manager/traffic-manager-routing-methods.md), dzięki czemu można wybrać, czy zarządzanie wdrożeniami przy użyciu modelu podstawowy/kopia zapasowa lub podzielenie ruchu między nimi.

![Równoważenie usług w chmurze platformy Azure między regionami przy użyciu usługi Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Najszybszy odpowiedź utratą w regionie, ważne jest, aby skonfigurować usługi Traffic Manager [monitorowania punktu końcowego](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opcja 2: Wdróż aplikację w nowym regionie
Obsługa wielu aktywnych wdrożeń, zgodnie z opisem w poprzedniej opcji powoduje dodatkowe koszty bieżące. Jeśli cel czasu odzyskiwania (RTO) jest na tyle elastyczna, a oryginalny kod lub skompilowany pakiet usług w chmurze, możesz utworzyć nowe wystąpienie aplikacji w innym regionie i rekordami DNS, aby wskazywał nowe wdrożenie aktualizacji.

Aby uzyskać więcej szczegółów na temat sposobu tworzenia i wdrażania aplikacji usługi w chmurze, zobacz [jak utworzyć i wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).

W zależności od źródła danych aplikacji może być konieczne sprawdź procedury odzyskiwania dla źródła danych aplikacji.

* Dla źródeł danych usługi Azure Storage, zobacz [replikacja usługi Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) do zapoznania się na opcje, które są dostępne na podstawie wybranego replikacji modelu dla aplikacji.
* Dla źródeł bazy danych SQL, przeczytaj [omówienie: Business ciągłość działalności biznesowej i bazy danych odzyskiwania po awarii z usługą SQL Database w chmurze](../sql-database/sql-database-business-continuity.md) do zapoznania się na opcje, które są dostępne na podstawie wybranej replikacji modelu dla aplikacji.


## <a name="option-3-wait-for-recovery"></a>Opcja 3: Poczekaj, aż odzyskiwanie
W tym przypadku jest wymagana żadna akcja ze strony użytkownika, ale usługi będą niedostępne do momentu przywrócenia regionu. Można wyświetlić bieżący stan usługi na [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sposobu wdrażania odzyskiwania po awarii i strategii wysokiej dostępności, zobacz [odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Aby opracować szczegółowe technicznego zrozumienia możliwości platformy chmury, zobacz [wskazówek technicznych odporność platformy Azure](../resiliency/resiliency-technical-guidance.md).