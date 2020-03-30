---
title: Zarządzanie siecią produkcyjną platformy Azure — Microsoft Azure
description: W tym artykule opisano, jak firma Microsoft zarządza siecią produkcyjną platformy Azure i obsługuje ją w celu zabezpieczenia centrów danych platformy Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727126"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Zarządzanie siecią produkcyjną platformy Azure i ich działanie    
W tym artykule opisano, jak firma Microsoft zarządza siecią produkcyjną platformy Azure i obsługuje ją w celu zabezpieczenia centrów danych platformy Azure.

## <a name="monitor-log-and-report"></a>Monitorowanie, rejestrowanie i raportowanie

Zarządzanie i działanie sieci produkcyjnej platformy Azure jest skoordynowanym wysiłkiem między zespołami operacyjnymi platformy Azure i usługi Azure SQL Database. Zespoły korzystają z kilku narzędzi do monitorowania wydajności systemu i aplikacji w środowisku. Używają odpowiednich narzędzi do monitorowania urządzeń sieciowych, serwerów, usług i procesów aplikacji.

Aby zapewnić bezpieczne wykonywanie usług uruchomionych w środowisku platformy Azure, zespoły operacyjne implementują wiele poziomów monitorowania, rejestrowania i raportowania, w tym następujące akcje:

- Przede wszystkim agent monitorowania firmy Microsoft (MMA) gromadzi informacje o monitorowaniu i diagnostyce dziennika z wielu miejsc, w tym kontrolera sieci szkieletowej (FC) i głównego systemu operacyjnego (OS) i zapisuje je do plików dziennika. Agent po pewnym czasie wypchnie przetrawny podzbiór informacji do wstępnie skonfigurowanego konta magazynu platformy Azure. Ponadto usługa monitorowania i diagnostyki wolnostojącej odczytuje różne dane dziennika monitorowania i diagnostyki oraz podsumowuje informacje. Usługa monitorowania i diagnostyki zapisuje informacje do zintegrowanego dziennika. Platforma Azure korzysta z niestandardowego monitorowania zabezpieczeń platformy Azure, które jest rozszerzeniem systemu monitorowania platformy Azure. Zawiera składniki, które obserwują, analizują i raportują zdarzenia istotne dla zabezpieczeń z różnych punktów platformy.

- Platforma azure SQL Database Windows Fabric zapewnia usługi zarządzania, wdrażania, rozwoju i nadzoru operacyjnego dla usługi Azure SQL Database. Platforma oferuje rozproszone, wieloetapowe usługi wdrażania, monitorowanie kondycji, automatyczne naprawy i zgodność z wersjami usługi. Świadczy następujące usługi:

   - Możliwości modelowania usług ze środowiskiem programistycznym o wysokiej wierności (klastry centrów danych są kosztowne i rzadkie).
   - Przepływy pracy wdrażania i uaktualniania jednym kliknięciem w celu przygotowania do sieci i konserwacji.
   - Raportowanie kondycji za pomocą zautomatyzowanych przepływów pracy naprawy, aby umożliwić samonaprawianie.
   - Monitorowanie w czasie rzeczywistym, alerty i debugowanie obiektów w węzłach systemu rozproszonego.
   - Scentralizowane zbieranie danych operacyjnych i metryk do rozproszonej analizy przyczyn źródłowych i wglądu w usługi.
   - Narzędzia operacyjne do wdrażania, zarządzania zmianami i monitorowania.
   - Platforma azure SQL Database Windows Fabric i skrypty watchdog działają w sposób ciągły i monitorują w czasie rzeczywistym.

Jeśli wystąpią jakieś anomalie, proces reagowania na zdarzenia, po którym następuje zespół triage zdarzeń platformy Azure jest aktywowany. Odpowiedni personel pomocy technicznej platformy Azure są powiadamiani, aby odpowiedzieć na incydent. Śledzenie i rozwiązywanie problemów są dokumentowane i zarządzane w scentralizowanym systemie biletowym. Metryki dostępności systemu są dostępne w ramach umowy o zachowaniu poufności (NDA) i na żądanie.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Sieć korporacyjna i wieloczynnikowy dostęp do produkcji
Baza użytkowników sieci firmowej obejmuje personel pomocy technicznej platformy Azure. Sieć firmowa obsługuje wewnętrzne funkcje firmowe i obejmuje dostęp do aplikacji wewnętrznych, które są używane do obsługi klienta platformy Azure. Sieć firmowa jest zarówno logicznie, jak i fizycznie oddzielona od sieci produkcyjnej platformy Azure. Pracownicy platformy Azure uzyskują dostęp do sieci firmowej przy użyciu stacji roboczych i laptopów platformy Azure. Wszyscy użytkownicy muszą mieć konto usługi Azure Active Directory (Azure AD), w tym nazwę użytkownika i hasło, aby uzyskać dostęp do zasobów sieci firmowej. Dostęp do sieci firmowej korzysta z kont usługi Azure AD, które są wydawane wszystkim pracownikom, wykonawcom i dostawcom firmy Microsoft i zarządzane przez firmę Microsoft Information Technology. Unikatowe identyfikatory użytkowników wyróżniają personel na podstawie ich statusu zatrudnienia w firmie Microsoft.

Dostęp do wewnętrznych aplikacji platformy Azure jest kontrolowany za pomocą uwierzytelniania za pomocą usług federacyjnych Active Directory (AD FS). Usługi AD FS to usługa obsługiwana przez firmę Microsoft Information Technology, która zapewnia uwierzytelnianie użytkowników sieci firmowej za pomocą stosowania bezpiecznego tokenu i oświadczeń użytkowników. Usługi AD FS umożliwiają wewnętrznym aplikacjom platformy Azure uwierzytelnienie użytkowników w domenie firmy Microsoft active directory. Aby uzyskać dostęp do sieci produkcyjnej ze środowiska sieci firmowej, użytkownicy muszą uwierzytelniać się przy użyciu uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
