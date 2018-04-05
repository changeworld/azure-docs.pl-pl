---
title: Co robić w przypadku platformy Azure usługa przerw w działaniu, które ma wpływ na usługi w chmurze Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku Azure zakłócenia, który ma wpływ na usługi w chmurze Azure.
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
ms.openlocfilehash: 7028417c95aa6969793c00d0bb270c96e56164fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co robić w przypadku platformy Azure usługa przerw w działaniu, które ma wpływ na usługi w chmurze Azure
W firmie Microsoft firma Microsoft nad tym, aby upewnić się, że naszych usług są zawsze dostępne dla Ciebie przydatne. Wymusza naszych niezależnych czasami wpływ nam w sposób, aby spowodować zakłócenia w świadczeniu usług nieplanowane.

Firma Microsoft udostępnia Umowa dotycząca poziomu usług (SLA) dla swoich usług jako zobowiązanie czas pracy i łączności. Umowa SLA dla poszczególnych usług Azure można znaleźć w folderze [umowy dotyczące poziomu usług Azure](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele wbudowanych funkcji obsługujących aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji o tych usług, przeczytaj [odzyskiwania po awarii i wysoką dostępność aplikacji Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

W tym artykule omówiono scenariusza odzyskiwania awaryjnego wartość true, gdy całego regionu ulegnie awarii z powodu poważne klęski lub powszechnie przerw w obsłudze. Są to rzadkie wystąpienia, ale należy przygotować dla jest awarii całego regionu. Całego regionu napotyka przerw w działaniu usługi, Magazyn lokalnie nadmiarowy kopie danych tymczasowo będą niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob magazynu Azure i tabele są przechowywane w innym regionie. W przypadku ukończenia regionalnej awarii lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania Azure, ponownie mapuje wszystkie wpisy DNS do regionu replikacją geograficzną.

> [!NOTE]
> Należy pamiętać, że nie masz żadnych kontrolę nad ten proces i nastąpią dla zakłócenia w świadczeniu usług centrum danych na poziomie. W związku z tym musi również polegać na innych strategii tworzenia kopii zapasowych specyficzne dla aplikacji do osiągnięcia najwyższy poziom dostępności. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii i wysokiej dostępności dla aplikacji tworzonych w systemie Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Jeśli chcesz mieć możliwość wpływają na własnych trybu failover, warto rozważyć użycie [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), który tworzy kopię danych tylko do odczytu w innym regionie.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opcja 1: Użycie kopii zapasowej wdrożenia za pośrednictwem usługi Azure Traffic Manager
Najbardziej niezawodne rozwiązanie odzyskiwania po awarii wymaga obsługi wielu wdrożeń aplikacji w różnych regionach, a następnie za pomocą [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwiających kierowanie ruchem między nimi. Menedżer ruchu Azure udostępnia wiele [metody routingu](../traffic-manager/traffic-manager-routing-methods.md), więc możesz wybrać, czy zarządzanie wdrożeniami przy użyciu modelu podstawowej lub tworzenia kopii zapasowej lub podzielenie ruchu między nimi.

![Równoważenie usług Azure Cloud Services w regionach z usługi Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Najszybszym odpowiedzi na utratę regionu, ważne jest, aby skonfigurować Menedżera ruchu [monitorowania punktów końcowych](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opcja 2: Wdrażanie aplikacji do nowego regionu
Obsługa wielu aktywnych wdrożeń, zgodnie z opisem w poprzedniej opcji wiąże się dodatkowe bieżących kosztów. Oryginalny kod lub skompilowanych pakietu usługi w chmurze celu czasu odzyskiwania (RTO) jest wystarczająco elastyczny, należy utworzyć nowe wystąpienie aplikacji w innym regionie i zaktualizować rekordów DNS tak, aby wskazywał nowe wdrożenie.

Aby uzyskać więcej szczegółów na temat sposobu tworzenia i wdrażania aplikacji usługi w chmurze, zobacz [sposobu tworzenia i wdrażania usługi w chmurze](cloud-services-how-to-create-deploy-portal.md).

W zależności od źródła danych aplikacji konieczne może być sprawdź procedury odzyskiwania dla źródła danych aplikacji.

* Dla źródeł danych usługi Azure Storage, zobacz [replikacja usługi Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) sprawdzenia opcje, które są dostępne na podstawie wybranego replikacji modelu dla aplikacji.
* Dla bazy danych SQL źródeł, przeczytaj [omówienie: firm odzyskiwania po awarii ciągłości i bazy danych z bazy danych SQL w chmurze](../sql-database/sql-database-business-continuity.md) sprawdzania dostępnych opcji opartą na modelu replikacji wybranych aplikacji.


## <a name="option-3-wait-for-recovery"></a>Opcja 3: Poczekaj, aż odzyskiwanie
W takim przypadku nie jest wymagana żadna akcja ze strony użytkownika, ale usługi będą niedostępne do momentu przywrócenia regionu. Możesz wyświetlać bieżący stan usługi na [pulpit nawigacyjny kondycji usługi Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sposobu wdrażania odzyskiwania po awarii i strategii wysokiej dostępności, zobacz [odzyskiwania po awarii i wysoką dostępność aplikacji Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Aby opracować techniczne szczegółowy opis możliwości platformy chmury, zobacz [wskazówki techniczne Azure odporności](../resiliency/resiliency-technical-guidance.md).