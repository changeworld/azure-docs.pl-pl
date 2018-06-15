---
title: Dokumentacja pakietu Azure Operations Management Suite (OMS) — samouczki | Microsoft Docs
description: Pakiet Microsoft Operations Management Suite (OMS) to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania systemami IT, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę. W tym artykule opisano różne usługi w ramach pakietu OMS i umieszczono linki do szczegółowych treści na ich temat.
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: ''
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23071215"
---
# <a name="what-is-operations-management-suite-oms"></a>Co to jest pakiet Operations Management Suite (OMS)?
Pakiet Microsoft Operations Management Suite (OMS) to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania systemami IT, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę.  Ponieważ oprogramowanie OMS jest zaimplementowane jako usługa w chmurze, może być dostępne szybko przy minimalnym poziomie inwestycji w usługi infrastruktury.  Nowe funkcje są dostarczane automatycznie, co eliminuje koszty konserwacji i uaktualniania.

Pakiet OMS dostarcza wartościowych usług, a ponadto można go zintegrować ze składnikami rozwiązania System Center, takimi jak program System Center Operations Manager, co pozwoli rozszerzyć wcześniejsze inwestycje w rozwiązania do zarządzania na środowisko chmury.  System Center i pakiet OMS mogą pracować razem, zapewniając pełnię możliwości hybrydowego zarządzania.

Poniższe sekcje zawierają ogólny opis różnych obszarów wartości pakietu OMS i usług, które je implementują.  Przed zapoznaniem się ze szczegółową dokumentacją każdego składnika pakietu OMS można skorzystać z omówienia architektury pakietu OMS.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Wgląd w dane i analizy](media/operations-management-suite-overview/icon-insight-analytics.png) Wgląd w dane i analizy
[Usługa Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) pomaga w zbieraniu, korelowaniu i wyszukiwaniu danych dziennika i danych wydajnościowych wygenerowanych przez systemy operacyjne i aplikacje, a także podejmowaniu odpowiednich działań na podstawie tych danych. Udostępnia informacje operacyjne w czasie rzeczywistym przy użyciu zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, umożliwiając łatwe analizowanie milionów rekordów dla wszystkich obciążeń i serwerów, niezależnie od ich lokalizacji fizycznej.

Do usługi Log Analytics można łatwo dodawać rozwiązania definiujące dane, które mają być zbierane, i logikę ich analizy.  Rozwiązania mogą zawierać dodatkową funkcjonalność, która jest automatycznie dostarczana do agentów po wykonaniu minimalnej konfiguracji lub bez konfiguracji.  Oprócz używania narzędzi analizy dostarczonych przez indywidualne rozwiązania można wykonywać niestandardowe wyszukiwania w całym zestawie danych w celu skorelowania danych między systemami i aplikacjami.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automatyzacja i kontrola](media/operations-management-suite-overview/icon-automation-control.png) Automatyzacja i kontrola
Usługa Azure Automation pozwala automatyzować procesy administracyjne za pomocą opartych na programie PowerShell elementów [Runbook](../automation/automation-runbook-types.md), które działają w chmurze Azure.  Elementy Runbook mają dostęp do dowolnego produktu lub usługi, którymi można zarządzać za pomocą programu PowerShell, w tym do zasobów znajdujących się w innych chmurach, jak Amazon Web Services (AWS).  Elementy Runbook mogą być również wykonywane na serwerze w lokalnym centrum danych w celu zarządzania zasobami lokalnymi.

Usługa Azure Automation udostępnia funkcję zarządzania konfiguracją za pomocą programu [PowerShell DSC](../automation/automation-dsc-overview.md).  Można tworzyć zasoby DSC hostowane na platformie Azure i nimi zarządzać oraz stosować je w systemach chmurowych i lokalnych w celu definiowania i automatycznego wymuszania ich konfiguracji.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Ochrona i odzyskiwanie](media/operations-management-suite-overview/icon-protection-recovery.png) Ochrona i odzyskiwanie po awarii
Usługa [Azure Backup](http://azure.microsoft.com/documentation/services/backup) chroni dane aplikacji i przechowuje je przez wiele lat bez konieczności ponoszenia jakichkolwiek inwestycji kapitałowych i przy minimalnych kosztach operacyjnych.  Poza tworzeniem kopii zapasowych z obciążeń aplikacji, takich jak SQL Server i SharePoint, może także tworzyć kopie zapasowe danych z fizycznych i wirtualnych serwerów z systemem Windows.  Może również być wykorzystywana przez program System Center Data Protection Manager (DPM) do replikacji chronionych danych na platformę Azure w celu uzyskania nadmiarowości i długoterminowego przechowywania.

Usługa [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) przyczynia się do realizacji strategii ciągłości biznesowej i odzyskiwania po awarii (BCDR, business continuity and disaster recovery) poprzez organizowanie replikacji, pracy w trybie failover i odzyskiwania danych w odniesieniu do lokalnych maszyn wirtualnych funkcji Hyper-V, maszyn wirtualnych VMware oraz serwerów fizycznych z systemem Windows lub Linux. Można replikować maszyny do dodatkowego centrum danych lub rozszerzyć centrum danych przez replikowanie ich na platformę Azure. Usługa Site Recovery udostępnia także prosty tryb failover oraz odzyskiwanie dla obciążeń. Integruje się z mechanizmami odzyskiwania po awarii, takimi jak SQL Server AlwaysOn, i udostępnia plany odzyskiwania w celu zapewnienia łatwego trybu failover dla obciążeń, które są rozmieszczone warstwowo na różnych maszynach.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Zabezpieczenia i zgodność w ramach pakietu OMS](media/operations-management-suite-overview/icon-security-compliance.png) Zabezpieczenia i zgodność
Usługa zabezpieczeń i zgodności pomaga zidentyfikować, oszacować i ograniczyć zagrożenia bezpieczeństwa dotyczące infrastruktury.  Te funkcje oprogramowania OMS są implementowane za pomocą wielu rozwiązań w usłudze Log Analytics, która analizuje dane dziennika i konfiguracji z systemów agentów, aby pomóc w zapewnieniu bezpieczeństwa środowiska użytkownika.

* [Rozwiązanie zabezpieczeń i inspekcji](oms-security-getting-started.md) zbiera i analizuje zdarzenia zabezpieczeń w zarządzanych systemach w celu identyfikacji podejrzanych działań.
* [Rozwiązanie chroniące przed złośliwym kodem](../log-analytics/log-analytics-malware.md) dostarcza raporty dotyczące stanu ochrony przed złośliwym kodem w zarządzanych systemach.  
* Rozwiązanie aktualizacji systemu wykonuje analizę aktualizacji zabezpieczeń i innych aktualizacji w zarządzanych systemach, dzięki czemu można łatwo identyfikować systemy wymagające zastosowania poprawek.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Dowiedz się więcej o usłudze [Azure Automation](../automation/automation-intro.md).
* Dowiedz się więcej o usłudze [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Dowiedz się więcej o usłudze [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

