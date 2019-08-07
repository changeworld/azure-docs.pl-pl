---
title: Zarządzanie platformą Azure — omówienie — zarządzanie platformą Azure
description: Omówienie obszarów zarządzania dla aplikacji platformy Azure i zasobów wraz z łączami do zawartości w narzędzia do zarządzania platformy Azure.
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 12/06/2018
ms.author: dacoulte
ms.openlocfilehash: 958f82ef94a08adaf2bbae1ef8ceefae5db289ea
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780797"
---
# <a name="overview-of-management-services-in-azure"></a>Przegląd usług zarządzania na platformie Azure

Ład na platformie Azure jest jednym z aspektów zarządzania. W tym artykule opisano różne obszary zarządzania, wdrażania i konserwowania zasobów na platformie Azure.

Zarządzanie dotyczy zadań i procesów wymaganych do obsługi aplikacji biznesowych i zasobów, które je obsługują. Platforma Azure oferuje wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania. Usługi te nie są tylko w przypadku zasobów na platformie Azure, ale także w innych chmurach i w środowisku lokalnym. Opis różnych narzędzi i jak one współdziałają ze sobą jest pierwszym krokiem projektowania pełnego środowiska zarządzania.

Poniższy diagram przedstawia różne obszary zarządzania wymagane do obsługi dowolnej aplikacji lub zasobu. Te obszary można traktować jako cykl życia. Każdy obszar jest wymagany kolejno ciągłe okresie istnienia zasobu. Cykl życia tego zasobu zaczyna się od początkowego wdrożenia, następuje działanie, a na koniec podczas wycofania.

![Dyscypliny zarządzania na platformie Azure](../monitoring/media/management-overview/management-capabilities.png)

Nie pojedyncza usługa platformy Azure nie spełnia całkowicie wymagań dla obszaru zarządzania określonego. Zamiast tego każdy jest wykonywane przez wiele usług, współpracujących. Niektóre usługi, takie jak usługa Application Insights, oferują ukierunkowane funkcje monitorowania dla aplikacji sieci web. Inne, takie jak dzienniki Azure Monitor, przechowują dane zarządzania dla innych usług. Ta funkcja umożliwia analizowanie danych różnych typów zbieranych przez różne usługi.

W poniższych sekcjach krótko opisano różne obszary zarządzania oraz podano linki do szczegółowych informacji o głównych usługach platformy Azure dotyczących tych obszarów.

## <a name="monitor"></a>Monitorowanie

Monitorowanie to proces zbierania i analizowania danych inspekcji wydajności, kondycja i dostępność zasobów. Skuteczna Strategia monitorowania pomoże Ci zrozumienie sposobu działania, składników i zwiększyć czas aktywności z powiadomieniami. Przeczytaj omówienie monitorowania, która obejmuje różne usługi używane w [monitorowania platformy Azure, aplikacje i zasoby](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurowanie

Konfigurowanie dotyczy początkowego wdrażania i konfiguracji zasobów i rutynowej konserwacji.
Automatyzacja tych zadań pozwala wyeliminować nadmiarowość, zminimalizować poświęcany czas i wysiłek oraz zwiększyć dokładność i wydajność. [Usługa Azure Automation](../automation/automation-intro.md) dostarcza większość usług do automatyzowania zadań konfiguracji. Podczas gdy elementy runbook obsługują automatyzację procesów, zarządzanie konfiguracją i aktualizacjami pomaga zarządzać konfiguracją.

## <a name="govern"></a>Ład

Ład dotyczy mechanizmów i procesów kontrolowania aplikacji i zasobów na platformie Azure. Obejmuje to planowanie inicjatyw i wyznaczanie strategicznych priorytetów.
Ład na platformie Azure jest zaimplementowany głównie za pomocą dwóch usług. [Usługa Azure Policy](./policy/overview.md) służy do tworzenia, przypisywania oraz zarządzanie nimi definicji zasad w celu wymuszania reguł dla zasobów. Ta funkcja zapewnia tych zasobów zgodnie ze standardami firmy. [Azure Cost Management](../cost-management/overview-cost-mgt.md) umożliwia śledzenie użycia i wydatków w chmurze dla zasobów platformy Azure i innych dostawców chmury.

## <a name="secure"></a>Bezpieczeństwo

Zarządzać zabezpieczeniami zasobów i danych. Program zabezpieczeń obejmuje ocenę zagrożeń, zbieranie i analizowanie danych, a także zgodności aplikacji i zasobów. Zabezpieczenia monitorowania i analizy zagrożeń są dostarczane przez [usługi Azure Security Center](../security-center/security-center-intro.md), która obejmuje ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w obciążeniach chmury hybrydowej. Zobacz [wprowadzenie do zabezpieczeń platformy Azure](../security/fundamentals/overview.md) wyczerpujące informacje i wskazówki dotyczące zabezpieczenia zasobów platformy Azure.

## <a name="protect"></a>Ochrona

Ochrona odnosi się do aplikacji i danych jest dostępne, nawet w przypadku awarii, które są poza Twoją kontrolą. Ochrona na platformie Azure jest zapewniana przez dwie usługi. [Usługa Azure Backup](../backup/backup-introduction-to-azure-backup.md) oferuje tworzenie kopii zapasowych i odzyskiwanie danych w chmurze lub lokalnie. [Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) zapewnia ciągłość działania i natychmiastowe odzyskiwanie podczas awarii.

## <a name="migrate"></a>Migrate (Migracja)

Migracja dotyczy przenoszenia obciążeń działających obecnie lokalnie do chmury platformy Azure.
[Usługa Azure Migrate](../migrate/migrate-overview.md) jest usługą, która ułatwia ocenę przydatności migrację lokalnych maszyn wirtualnych na platformie Azure. Usługa Azure Site Recovery umożliwia migrowanie maszyn wirtualnych [ze środowiska lokalnego](../site-recovery/migrate-tutorial-on-premises-azure.md) lub [z usług Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Usługa Azure Database Migration](../dms/dms-overview.md) pomaga w migracji źródeł baz danych do platformy danych.
