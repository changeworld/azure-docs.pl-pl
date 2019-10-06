---
title: Zarządzanie platformą Azure — omówienie — zarządzanie platformą Azure
description: Przegląd obszarów zarządzania dla aplikacji i zasobów platformy Azure z linkami do zawartości w narzędziach zarządzania platformy Azure.
author: DCtheGeek
ms.service: governance
ms.topic: article
ms.date: 12/06/2018
ms.author: dacoulte
ms.openlocfilehash: 2535b64380f67da440698c7577840d5319e6ca4f
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978553"
---
# <a name="overview-of-management-services-in-azure"></a>Przegląd usług zarządzania na platformie Azure

Zarządzanie na platformie Azure to jeden aspekt zarządzania platformą Azure. W tym artykule opisano różne obszary zarządzania dotyczące wdrażania i utrzymywania zasobów na platformie Azure.

Zarządzanie odnosi się do zadań i procesów wymaganych do obsługi aplikacji firmowych i zasobów, które je obsługują. Platforma Azure oferuje wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania. Te usługi nie dotyczą tylko zasobów platformy Azure, ale również w innych chmurach i lokalnych. Zrozumienie różnych narzędzi i ich współdziałania to pierwszy krok projektowania kompletnego środowiska zarządzania.

Na poniższym diagramie przedstawiono różne obszary zarządzania wymagane do obsługi dowolnej aplikacji lub zasobu. Te różne obszary można traktować jako cykl życia. Każdy obszar jest wymagany w ciągłym pomyślnym przeniesieniu na cykl życia zasobów. Cykl życia zasobów rozpoczyna się od wstępnego wdrożenia, przez kontynuowanie operacji i ostatecznie po wycofaniu.

![Dyscypliny zarządzania na platformie Azure](../monitoring/media/management-overview/management-capabilities.png)

Żadna pojedyncza usługa platformy Azure nie wypełnia wymagań określonego obszaru zarządzania. Zamiast tego każdy jest realizowany przez kilka współpracujących ze sobą usług. Niektóre usługi, takie jak Application Insights, zapewniają funkcje monitorowania dla aplikacji sieci Web. Inne, takie jak dzienniki Azure Monitor, przechowują dane zarządzania dla innych usług. Ta funkcja umożliwia analizowanie danych różnych typów zbieranych przez różne usługi.

W poniższych sekcjach krótko opisano różne obszary zarządzania i przedstawiono linki do szczegółowej zawartości dotyczącej głównych usług platformy Azure przeznaczonych do ich rozwiązania.

## <a name="monitor"></a>Monitor

Monitorowanie to czynność zbierania i analizowania danych w celu przeprowadzenia inspekcji wydajności, kondycji i dostępności zasobów. Skuteczna strategia monitorowania pomaga zrozumieć działanie składników i zwiększyć czas pracy przy użyciu powiadomień. Zapoznaj się z omówieniem monitorowania, który obejmuje różne usługi używane do [monitorowania aplikacji i zasobów platformy Azure](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfiguracja

Konfiguracja dotyczy początkowego wdrożenia i konfiguracji zasobów oraz trwającej konserwacji.
Automatyzacja tych zadań pozwala wyeliminować nadmiarowość, zminimalizować czas i nakład pracy oraz zwiększyć dokładność i wydajność. [Azure Automation](../automation/automation-intro.md) zawiera zbiorcze usługi do automatyzowania zadań konfiguracyjnych. Chociaż elementy Runbook obsługują automatyzację procesów, konfigurację i zarządzanie aktualizacjami ułatwia zarządzanie konfiguracją.

## <a name="govern"></a>Decydując

Zarządzanie udostępnia mechanizmy i procesy umożliwiające zachowanie kontroli nad aplikacjami i zasobami na platformie Azure. Obejmuje ona planowanie inicjatyw i Ustawianie priorytetów strategicznych.
Zarządzanie na platformie Azure jest realizowane głównie za pomocą dwóch usług. [Azure Policy](./policy/overview.md) umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w celu wymuszania reguł dla zasobów. Ta funkcja zapewnia zgodność tych zasobów ze standardami firmowymi. [Azure Cost Management](../cost-management/overview-cost-mgt.md) umożliwia śledzenie użycia i wydatków w chmurze dla zasobów platformy Azure i innych dostawców chmury.

## <a name="secure"></a>Zapewnienia

Zarządzanie zabezpieczeniami zasobów i danych. Program zabezpieczeń obejmuje ocenianie zagrożeń, zbieranie i analizowanie danych oraz zgodność aplikacji i zasobów. Monitorowanie zabezpieczeń i analiza zagrożeń są udostępniane przez [Azure Security Center](../security-center/security-center-intro.md), w tym ujednolicone Zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Aby uzyskać wyczerpujące informacje i wskazówki dotyczące zabezpieczania zasobów platformy Azure, zobacz [wprowadzenie do zabezpieczeń platformy Azure](../security/fundamentals/overview.md) .

## <a name="protect"></a>Chronion

Ochrona dotyczy utrzymywania dostępu do aplikacji i danych, nawet w przypadku awarii, które wykraczają poza swoją kontrolę. Ochrona na platformie Azure jest świadczona przez dwie usługi. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) umożliwia tworzenie kopii zapasowych i odzyskiwanie danych, zarówno w chmurze, jak i lokalnie. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) zapewnia ciągłość działania i natychmiastowe odzyskiwanie podczas awarii.

## <a name="migrate"></a>Dokonać

Migracja dotyczy przenoszenia obciążeń działających obecnie lokalnie do chmury platformy Azure.
[Azure Migrate](../migrate/migrate-overview.md) to usługa, która ułatwia ocenę przydatności do migracji lokalnych maszyn wirtualnych na platformę Azure. Azure Site Recovery migruje maszyny wirtualne [z lokalizacji lokalnej](../site-recovery/migrate-tutorial-on-premises-azure.md) lub [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Migracja bazy danych platformy Azure](../dms/dms-overview.md) ułatwia Migrowanie źródeł baz danych do platform danych platformy Azure.
