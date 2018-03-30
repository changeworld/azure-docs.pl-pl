---
title: Zarządzanie Azure - monitorowania | Dokumentacja firmy Microsoft
description: Platforma Azure ma wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania, nie tylko uruchomione na platformie Azure, ale także w innych chmur i lokalnych aplikacji.  Ten artykuł zawiera opis wysokiego poziomu różnych obszarów zarządzania i linki do zawartości na narzędzia Azure do zarządzania aplikacji w chmurze i zasobów.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-management---monitoring"></a>Zarządzanie Azure - monitorowania

Monitorowanie na platformie Azure jest jednym aspekcie zarządzania Azure.  W tym artykule krótko opisano różnych obszarów zarządzania wymagane do wdrożenia i obsługi aplikacji i zasobów na platformie Azure przy użyciu łącza do dokumentacji ułatwiających rozpoczęcie pracy.

## <a name="management-in-azure"></a>Zarządzanie w systemie Azure

Zarządzanie odwołuje się do zadań i procesy wymagane do obsługi aplikacji biznesowych i zasobów, które je obsługują.  Platforma Azure ma wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania, nie tylko uruchomione na platformie Azure, ale także w innych chmur i lokalnych aplikacji.  Opis różnych dostępnych narzędzi i sposób ich mogą być używane razem z różnych scenariuszy zarządzania jest pierwszy krok w projektowaniu środowisku pełnego zarządzania.

Na poniższym diagramie przedstawiono różnych obszarów zarządzania, które są wymagane do obsługi dowolnej aplikacji lub zasobie.  Te różnych obszarów można traktować pod względem cykl których każdy wymagany kolejno ciągłego za pośrednictwem żywotność zasobu.  Spowoduje to uruchomienie z jego początkowym wdrożeniu za pośrednictwem jego dalsze działanie, a na koniec, gdy został wycofany.

![Możliwości zarządzania](media/management-overview/management-capabilities.png)


Nie jednej usługi Azure całkowicie wstawia wymagania dotyczące zarządzania konkretnego obszaru, ale zamiast tego każdy są realizowane przez wiele usług pracujących razem.  Niektóre usługi, podaj docelowe funkcje, takie jak usługi Application Insights, która zapewnia monitorowania aplikacji sieci web.  Inne dostarczają typowych funkcji, takich jak analizy dzienników, która przechowuje dane zarządzania dla innych usług, co umożliwia analizowanie danych o różnych typach zebrane przez różnych usług.  

W poniższych sekcjach krótko opisano różnych obszarów zarządzania i znajdują się linki do szczegółowych zawartości w głównej usług Azure przeznaczony do rozwiązania problemów.

## <a name="monitor"></a>Monitorowanie
Monitorowanie jest czynnością zbierania i analizowania danych w celu ustalenia wydajności, kondycji i dostępności aplikacji biznesowych i zasobów, od których zależy. Strategia monitorowania skuteczne pomoże poznać szczegółowe funkcjonowanie poszczególnych składników aplikacji i zwiększenia Twojej pracy aktywnego informujący poważnych problemów, dzięki czemu można je rozwiązać, zanim wystąpią problemy.  Możesz przeczytać omówienie Monitoring platformy Azure, która identyfikuje różne usługi używane przez Strategia monitorowania w [Azure monitorowania aplikacji i zasobów](monitoring-overview.md).


## <a name="configure"></a>Konfigurowanie
Skonfiguruj odwołuje się do początkowe wdrożenie i konfigurację aplikacji i zasobów i ich rutynowej konserwacji z poprawki i aktualizacje.  Automatyzacja tych zadań za pomocą skryptu oraz zasady pozwala wyeliminować nadmiarowość, minimalizując czas i wysiłek i zwiększając wydajność sieci dokładność i wydajność.  [Automatyzacja Azure](..\automation\automation-intro.md) zawiera większość usług do automatyzacji zadań konfiguracji.  Oprócz elementów runbook do automatyzacji procesów zapewnia konfiguracji i zarządzania aktualizacjami, które ułatwiają zarządzanie konfiguracją za pomocą zasad i identyfikacji i wdrażania aktualizacji.

## <a name="govern"></a>Decydować
Zarząd zapewnia mechanizmy i procesy, aby zachować kontrolę nad aplikacji i zasobów na platformie Azure.  Obejmuje Planowanie z inicjatywy i ustawienie strategicznych priorytetów.  Zarządzanie w systemie Azure przede wszystkim jest realizowana za pomocą dwóch usług.  [Zasady usługi Azure](../azure-policy/azure-policy-introduction.md) służy do tworzenia, przypisywania, definicji i zarządzania nimi zasad wymuszających różnych zasad i akcje za pośrednictwem zasobów, dzięki czemu te zasoby pozostają zgodne ze standardami Twojej firmy i umowy dotyczące poziomu usług. [Azure kosztów zarządzania Cloudyn](../cost-management/overview.md) umożliwia śledzenie użycia chmury i wydatków zasobów platformy Azure i innych dostawców chmury, w tym usług AWS i Google.

## <a name="secure"></a>Bezpieczeństwo
Zarządzanie zabezpieczeniami aplikacji, zasobów i danych obejmuje kombinację oceny zagrożeń, zbieranie i analizowanie danych zabezpieczeń oraz zapewnienie, że aplikacje i zasoby są zaprojektowane i skonfigurowane w sposób bezpieczny.  Analiza monitorowania i zagrożeń zabezpieczeń są dostarczane przez [Centrum zabezpieczeń Azure](../security-center/security-center-intro.md) w tym ujednolicone zarządzanie zabezpieczeniami i advanced threat protection między obciążeń chmury hybrydowej.  Powinno również zostać wyświetlone [wprowadzenie do zabezpieczeń Azure](../security/azure-security.md) uzyskać szczegółowe informacje dotyczące zabezpieczeń w usłudze Azure i wytyczne dotyczące bezpiecznego Konfigurowanie zasobów Azure.


## <a name="protect"></a>Ochrona
Ochrona odwołuje się do zapewnienia, że aplikacje i dane są zawsze dostępne, nawet w przypadku awarii poza kontrolą.  Ochrony na platformie Azure jest zapewniana przez dwie usługi.  [Kopia zapasowa Azure](../backup/backup-introduction-to-azure-backup.md) zapewnia kopii zapasowych i odzyskiwania danych w chmurze lub lokalnie.    [Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) zapewnia wysoką dostępność aplikacji, zapewniając ciągłość prowadzenia działalności biznesowej i odzyskiwania natychmiastowego w przypadku awarii.

## <a name="migrate"></a>Migrate (Migracja) 
Migracja odwołuje się do przechodzenia obciążenia aktualnie uruchomione lokalnie w chmurze platformy Azure.  [Azure migracji](../migrate/migrate-overview.md) to usługa, która pomaga oceny przydatności migracji, w tym na podstawie rozmiaru i kosztów oszacowania lokalnymi maszynami wirtualnymi na platformie Azure.  Usługa Azure Site Recovery może pomóc w rzeczywistej migracji maszyn wirtualnych [z lokalnymi](../site-recovery/migrate-tutorial-on-premises-azure.md) lub [z usług Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Migracja bazy danych Azure](../dms/dms-overview.md) pomoże Migrowanie wielu źródeł bazy danych do platformy Azure danych.