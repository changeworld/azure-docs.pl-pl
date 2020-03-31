---
title: Omówienie zarządzania usługą Azure — zarządzanie usługą Azure
description: Omówienie obszarów zarządzania aplikacjami i zasobami platformy Azure z łączami do zawartości narzędzi do zarządzania platformy Azure.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75980896"
---
# <a name="overview-of-management-services-in-azure"></a>Omówienie usług zarządzania na platformie Azure

Zarządzanie na platformie Azure jest jednym z aspektów usługi Azure Management. W tym artykule opisano różne obszary zarządzania do wdrażania i obsługi zasobów na platformie Azure.

Zarządzanie dotyczy zadań i procesów wymaganych do obsługi aplikacji biznesowych i zasobów, które je obsługują. Platforma Azure ma wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania. Te usługi są nie tylko dla zasobów na platformie Azure, ale także w innych chmurach i lokalnie. Zrozumienie różnych narzędzi i ich współpraca jest pierwszym krokiem w projektowaniu kompletnego środowiska zarządzania.

Poniższy diagram przedstawia różne obszary zarządzania wymagane do obsługi dowolnej aplikacji lub zasobu. Te różne obszary można traktować jako cykl życia. Każdy obszar jest wymagany w ciągłych odstępach czasu przez cały okres eksploatacji zasobu. Ten cykl życia zasobu rozpoczyna się od początkowego wdrożenia, poprzez kontynuowanie operacji i na koniec po wycofaniu.

![Dyscypliny zarządzania na platformie Azure](../monitoring/media/management-overview/management-capabilities.png)

Żadna pojedyncza usługa platformy Azure całkowicie spełnia wymagania określonego obszaru zarządzania. Zamiast tego każdy jest realizowany przez kilka usług współpracujących ze sobą. Niektóre usługi, takie jak usługa Application Insights, zapewniają funkcje monitorowania ukierunkowanego dla aplikacji sieci web. Inne, takie jak dzienniki usługi Azure Monitor, przechowują dane zarządzania dla innych usług. Ta funkcja umożliwia analizowanie danych różnych typów zebranych przez różne usługi.

W poniższych sekcjach krótko opisano różne obszary zarządzania oraz podano linki do szczegółowych informacji o głównych usługach platformy Azure dotyczących tych obszarów.

## <a name="monitor"></a>Monitorowanie

Monitorowanie jest czynnością zbierania i analizowania danych w celu inspekcji wydajności, kondycji i dostępności zasobów. Skuteczna strategia monitorowania pomaga zrozumieć działanie składników i zwiększyć czas pracy bez przestojów dzięki powiadomieniom. Przeczytaj omówienie monitorowania, które obejmuje różne usługi używane w [monitorowaniu aplikacji i zasobów platformy Azure.](../monitoring/monitoring-overview.md)

## <a name="configure"></a>Konfigurowanie

Konfiguracja odnosi się do początkowego wdrażania i konfiguracji zasobów i bieżącej konserwacji.
Automatyzacja tych zadań pozwala wyeliminować nadmiarowość, minimalizując czas i wysiłek oraz zwiększając dokładność i wydajność. [Usługa Azure Automation](../automation/automation-intro.md) dostarcza większość usług do automatyzowania zadań konfiguracji. Podczas gdy elementy runbook obsługują automatyzację procesów, zarządzanie konfiguracją i aktualizacjami pomaga w zarządzaniu konfiguracją.

## <a name="govern"></a>Ład

Ład dotyczy mechanizmów i procesów kontrolowania aplikacji i zasobów na platformie Azure. Obejmuje to planowanie inicjatyw i wyznaczanie strategicznych priorytetów.
Ład na platformie Azure jest zaimplementowany głównie za pomocą dwóch usług. [Usługa Azure Policy](./policy/overview.md) umożliwia tworzenie, przypisywanie i zarządzanie definicjami zasad w celu wymuszania reguł dla zasobów. Ta funkcja utrzymuje te zasoby zgodnie ze standardami firmy. [Usługa Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) umożliwia śledzenie użycia chmury i wydatków dla zasobów platformy Azure i innych dostawców chmury.

## <a name="secure"></a>Bezpieczeństwo

Zarządzaj bezpieczeństwem zasobów i danych. Program zabezpieczeń obejmuje ocenę zagrożeń, zbieranie i analizowanie danych oraz zgodność aplikacji i zasobów. Usługa [Azure Security Center](../security-center/security-center-intro.md)zapewnia monitorowanie zabezpieczeń i analizę zagrożeń, która obejmuje ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w przypadku obciążeń chmury hybrydowej. Zobacz [Wprowadzenie do zabezpieczeń platformy Azure, aby](../security/fundamentals/overview.md) uzyskać wyczerpujące informacje i wskazówki dotyczące zabezpieczania zasobów platformy Azure.

## <a name="protect"></a>Ochrona

Ochrona odnosi się do utrzymania aplikacji i danych dostępnych, nawet przy awariach, które są poza kontrolą. Ochrona na platformie Azure jest zapewniana przez dwie usługi. [Usługa Azure Backup](../backup/backup-introduction-to-azure-backup.md) oferuje tworzenie kopii zapasowych i odzyskiwanie danych w chmurze lub lokalnie. [Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) zapewnia ciągłość działania i natychmiastowe odzyskiwanie podczas awarii.

## <a name="migrate"></a>Migrate (Migracja)

Migracja dotyczy przenoszenia obciążeń działających obecnie lokalnie do chmury platformy Azure.
[Usługa Azure Migrate](../migrate/migrate-overview.md) to usługa, która pomaga ocenić przydatność migracji lokalnych maszyn wirtualnych na platformę Azure. Usługa Azure Site Recovery migruje maszyny [wirtualne z lokalnego](../site-recovery/migrate-tutorial-on-premises-azure.md) lub [z usług Amazon Web Services.](../site-recovery/migrate-tutorial-aws-azure.md) [Migracja bazy danych platformy Azure](../dms/dms-overview.md) pomaga w migracji źródeł bazy danych na platformy Azure Data.
