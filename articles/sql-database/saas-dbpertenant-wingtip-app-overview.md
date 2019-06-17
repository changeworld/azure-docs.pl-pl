---
title: Przykład aplikacji wielodostępnej bazy danych SQL Azure — aplikacji Wingtip SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, korzystając z przykładowej aplikacji wielodostępnych, która korzysta z usługi Azure SQL Database, w przykładzie aplikacji Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 1c16ea44418d99ee1f80a7d0ef7a3e5b3f118f46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485257"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Wprowadzenie do wielodostępną aplikację SaaS, która używa wzorca bazy danych dla dzierżawcy z usługą SQL Database

Aplikacja SaaS o nazwie Wingtip jest przykładowej aplikacji wielodostępnych. Aplikacja używa wzorca aplikacji SaaS database dla dzierżawcy do obsługi wielu dzierżaw. Aplikacja prezentuje funkcje usługi Azure SQL Database umożliwiających stosowanie scenariuszy SaaS przy użyciu kilku wzorców projektowania i zarządzania SaaS. Aby szybko rozpocząć pracę, aplikacji SaaS o nazwie Wingtip wdraża w mniej niż pięć minut.

Skrypty kodu i zarządzanie źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Przed rozpoczęciem wykonywania tej procedury, zobacz [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) instrukcje pobierania i odblokować Wingtip Tickets skrypty zarządzania.

## <a name="application-architecture"></a>Architektura aplikacji

Aplikacja SaaS o nazwie Wingtip używa modelu bazy danych dla dzierżawcy. Pule elastyczne SQL wykorzystuje, aby zmaksymalizować wydajność. W przypadku aprowizacji i dzierżaw mapowania do swoich danych baza danych wykazu jest używany. Podstawowa aplikacja SaaS o nazwie Wingtip używa puli z trzema przykładowymi dzierżawami oraz bazy danych wykazów. Serwery wykazu i dzierżawy zostały udostępnione przy użyciu aliasów DNS. Te aliasy są używane do obsługi odwołanie do aktywnego zasoby używane przez aplikację Wingtip. Następujące aliasy zostały zaktualizowane pod punktu odzyskiwania zasobów w ramach samouczków odzyskiwania po awarii. Kończenie wiele wyników samouczki aplikacji Wingtip SaaS w dodatki do początkowego wdrożenia. Dodatki, takie jak analitycznej bazy danych i Zarządzanie schematami wielu baz danych zostały wprowadzone.


![Architektury SaaS o nazwie Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Przejdź do samouczków i współdziałać z aplikacją, skupić się na wzorców SaaS w odniesieniu do warstwy danych. Innymi słowy skupić się na warstwę danych, a nie overanalyze samej aplikacji. Zrozumienie implementacji SaaS tych wzorców jest kluczem do implementacji tych wzorców w aplikacjach. Należy również rozważyć wszystkich niezbędnych modyfikacji konkretnych potrzeb biznesowych.

## <a name="sql-database-wingtip-saas-tutorials"></a>Samouczki aplikacji Wingtip SaaS usługi SQL Database

Po wdrożeniu aplikacji, zapoznaj się z następujących samouczków, które są kompilowane w na początkowym wdrożeniu. Te samouczki Eksplorowanie typowych wzorców SaaS, które wykorzystują wbudowane funkcje usługi SQL Database, Azure SQL Data Warehouse i innymi usługami platformy Azure. Samouczki zawierają skrypty programu PowerShell, wraz ze szczegółowymi wyjaśnieniami. Wyjaśnienia uprościć, zrozumienia i stosowania tych samych wzorców zarządzania SaaS w swoich aplikacjach.


| Samouczek | Opis |
|:--|:--|
| [Wskazówki i porady dotyczące na przykład aplikacji SaaS wielodostępne bazy danych SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Pobierz i uruchom skrypty programu PowerShell, aby przygotować części aplikacji. |
|[Wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-get-started-deploy.md)|  Wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip z subskrypcją platformy Azure. |
|[Dzierżawcy aprowizacji i wykazu](saas-dbpertenant-provision-and-catalog.md)| Dowiedz się, jak aplikacja łączy się z dzierżaw przy użyciu bazy danych wykazu oraz sposób mapowania dzierżaw wykazu danych. |
|[Monitorowanie i zarządzanie wydajnością](saas-dbpertenant-performance-monitoring.md)| Dowiedz się, jak korzystać z funkcji monitorowania bazy danych SQL i Ustawiaj alerty, po przekroczeniu progów wydajności. |
|[Monitorowanie przy użyciu dzienników usługi Azure Monitor](saas-dbpertenant-log-analytics.md) | Dowiedz się, jak używać [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md) do monitorowania dużych ilości zasobów dla wielu pul. |
|[Przywracanie pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)| Dowiedz się, jak przywrócić bazy danych do wcześniejszego punktu w czasie. Poznasz również sposób przywracania bazą danych równoległych, co pozostawia istniejącej bazy danych dzierżawy w trybie online. |
|[Zarządzanie schematem bazy danych dzierżaw](saas-tenancy-schema-management.md)| Dowiedz się, jak zaktualizować schematu i aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw. |
|[Uruchamiania rozproszonych zapytań w wielu dzierżawach](saas-tenancy-cross-tenant-reporting.md) | Tworzenie bazy danych analizy ad hoc i uruchamianie zapytań rozproszonych w czasie rzeczywistym dla wszystkich dzierżaw.  |
|[Uruchamianie analizy w danych wyodrębniony dzierżawy](saas-tenancy-tenant-analytics.md) | Wyodrębnianie danych dzierżawy do analizy bazy danych ani na magazyn danych dla zapytań analizy w trybie offline. |


## <a name="next-steps"></a>Kolejne kroki

- [Ogólne wskazówki i porady dotyczące wdrażania i skorzystaj z przykładu aplikacji SaaS o nazwie Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wdrażanie aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-get-started-deploy.md)
