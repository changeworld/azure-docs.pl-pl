---
title: Przykład aplikacji wielodostępnych — Wingtip SaaS
description: Ucz się przy użyciu przykładowej aplikacji wielodostępnych, która używa usługi Azure SQL Database, przykład SaaS Wingtip
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256499"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Wprowadzenie do wielodostępnejlizyK SaaS, który używa wzorca bazy danych na dzierżawcę z bazy danych SQL

Aplikacja Wingtip SaaS jest przykładową aplikacją wielodostępną. Aplikacja używa wzorca aplikacji SaaS bazy danych na dzierżawcę do obsługi wielu dzierżawców. Aplikacja prezentuje funkcje usługi Azure SQL Database, które umożliwiają scenariusze SaaS przy użyciu kilku wzorców projektowania i zarządzania SaaS. Aby szybko rozpocząć działania, aplikacja Wingtip SaaS wdraża się w mniej niż pięć minut.

Kod źródłowy aplikacji i skrypty zarządzania są dostępne w repozytorium [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Przed rozpoczęciem zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi pobierania i odblokowywania skryptów zarządzania biletami Wingtip.

## <a name="application-architecture"></a>Architektura aplikacji

Aplikacja Wingtip SaaS używa modelu bazy danych na dzierżawcę. Używa elastycznych pul SQL, aby zmaksymalizować wydajność. Do inicjowania obsługi administracyjnej i mapowania dzierżaw do ich danych, baza danych katalogu jest używana. Podstawowa aplikacja SaaS Wingtip używa puli z trzema przykładowymi dzierżawcami oraz bazą danych katalogu. Serwery katalogu i dzierżawy zostały aprowidzone za pomocą aliasów DNS. Te aliasy są używane do obsługi odwołania do aktywnych zasobów używanych przez aplikację Wingtip. Te aliasy są aktualizowane w celu wskazanie zasobów odzyskiwania w samouczkach odzyskiwania po awarii. Ukończenie wielu samouczków Wingtip SaaS powoduje dodatki do początkowego wdrożenia. Wprowadzono dodatki, takie jak analityczne bazy danych i zarządzanie schematem między bazami danych.


![Architektura Wingtip SaaS](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Podczas przechodzenia przez samouczki i pracy z aplikacją, skupić się na wzorce SaaS, ponieważ odnoszą się one do warstwy danych. Innymi słowy, skup się na warstwie danych i nie przeanalizuj samej aplikacji. Zrozumienie implementacji tych wzorców SaaS jest kluczem do implementacji tych wzorców w aplikacjach. Należy również wziąć pod uwagę wszelkie niezbędne modyfikacje dla określonych wymagań biznesowych.

## <a name="sql-database-wingtip-saas-tutorials"></a>Samouczki SaaS w programie SQL Database Wingtip

Po wdrożeniu aplikacji zapoznaj się z poniższymi samouczkami, które opierają się na początkowym wdrożeniu. Te samouczki eksplorują typowe wzorce SaaS, które korzystają z wbudowanych funkcji bazy danych SQL, usługi Azure SQL Data Warehouse i innych usług platformy Azure. Samouczki zawierają skrypty programu PowerShell ze szczegółowymi wyjaśnieniami. Objaśnienia upraszczają zrozumienie i implementację tych samych wzorców zarządzania SaaS w aplikacjach.


| Samouczek | Opis |
|:--|:--|
| [Wskazówki i wskazówki dotyczące przykładu wielodostępnych aplikacji SaaS bazy danych SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Pobierz i uruchom skrypty programu PowerShell, aby przygotować części aplikacji. |
|[Wdrażanie i eksplorowanie aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)|  Wdrażanie i eksplorowanie aplikacji SaaS Wingtip za pomocą subskrypcji platformy Azure. |
|[Dzierżawcy aprowizowania i katalogowania](saas-dbpertenant-provision-and-catalog.md)| Dowiedz się, jak aplikacja łączy się z dzierżawcami przy użyciu bazy danych wykazu i jak katalog mapuje dzierżawców do ich danych. |
|[Monitorowanie wydajności i zarządzanie nimi](saas-dbpertenant-performance-monitoring.md)| Dowiedz się, jak korzystać z funkcji monitorowania bazy danych SQL i ustawiaj alerty po przekroczeniu progów wydajności. |
|[Monitorowanie za pomocą dzienników usługi Azure Monitor](saas-dbpertenant-log-analytics.md) | Dowiedz się, jak używać [dzienników usługi Azure Monitor](../log-analytics/log-analytics-overview.md) do monitorowania dużych ilości zasobów w wielu pulach. |
|[Przywracanie pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)| Dowiedz się, jak przywrócić bazę danych dzierżawy do poprzedniego punktu w czasie. Dowiedz się również, jak przywrócić do równoległej bazy danych, która pozostawia istniejącą bazę danych dzierżawy w trybie online. |
|[Zarządzanie schematem bazy danych dzierżawy](saas-tenancy-schema-management.md)| Dowiedz się, jak zaktualizować schemat i zaktualizować dane referencyjne we wszystkich bazach danych dzierżawy. |
|[Uruchamianie kwerend rozproszonych między dzierżawcami](saas-tenancy-cross-tenant-reporting.md) | Utwórz bazę danych analizy ad hoc i uruchamiaj zapytania rozproszone w czasie rzeczywistym we wszystkich dzierżawach.  |
|[Uruchamianie analizy wyodrębnionych danych dzierżawy](saas-tenancy-tenant-analytics.md) | Wyodrębnij dane dzierżawy do bazy danych analizy lub magazynu danych dla zapytań analizy w trybie offline. |


## <a name="next-steps"></a>Następne kroki

- [Ogólne wskazówki i wskazówki dotyczące wdrażania i korzystania z przykładu aplikacji SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wdrażanie aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
