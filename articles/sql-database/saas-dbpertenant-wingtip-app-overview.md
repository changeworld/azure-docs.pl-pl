---
title: Przykład wielodostępnych aplikacji bazy danych SQL Azure — Wingtip SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się przy użyciu wielodostępnym przykładowej aplikacji, która używa usługi Azure SQL Database w przykładzie Wingtip SaaS
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 224639dcc7da950801c7a5959ec14fc5ac7313e0
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Wprowadzenie do wielodostępnych aplikacji SaaS, który korzysta ze wzorca bazy danych na dzierżawcy z bazy danych SQL

Aplikacja Wingtip SaaS jest przykładowa aplikacja wielodostępnym. Aplikacja korzysta ze wzorca aplikacji SaaS bazy danych dla dzierżawy do obsługi wielu dzierżawców. Funkcje bazy danych SQL Azure, które umożliwiają scenariusze SaaS przy użyciu kilku wzorce projektowania i zarządzania SaaS aplikacji, których są wyświetlane. Aby szybko rozpocząć pracę, aplikacja Wingtip SaaS wdraża w mniej niż pięć minut.

Skrypty zarządzania i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Przed rozpoczęciem, zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty zarządzania Wingtip biletów.

## <a name="application-architecture"></a>Architektura aplikacji

Aplikacja Wingtip SaaS korzysta z modelu bazy danych dla dzierżawy. Aby zmaksymalizować wydajność używa puli elastycznej SQL. Dla dzierżawcy mapowania do swoich danych i udostępniania bazy danych katalogu jest używany. Podstawowe aplikacji Wingtip SaaS używa puli z trzech dzierżawcami próbki oraz baza danych katalogu. Kończenie wiele wyników samouczki Wingtip SaaS w dodatki do momentu pierwszego wdrożenia. Dodatki, takie jak analityczne baz danych i schemat bazy danych między zarządzania zostały wprowadzone.


![Architektura SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Jak przejść przez samouczków i pracy z aplikacją, skupić się na wzorce SaaS w odniesieniu do warstwy danych. Innymi słowy skupić się na warstwie danych, a nie overanalyze samej aplikacji. Opis wykonania tych SaaS wzorce jest kluczem do wykonania tych wzorców w aplikacjach. Należy również rozważyć wszelkie potrzebne modyfikacje dla konkretnych potrzeb biznesowych.

## <a name="sql-database-wingtip-saas-tutorials"></a>Samouczki SaaS Wingtip bazy danych SQL

Po wdrożeniu aplikacji, Poznaj następujące samouczki w początkowym wdrożeniu. Te samouczki Poznaj typowe wzorce SaaS, które korzystają z wbudowanych funkcji bazy danych SQL, Magazyn danych SQL Azure i innymi usługami Azure. Samouczki zawierają skryptów programu PowerShell z szczegółowe wyjaśnienia. Wyjaśnienia uprościć opis i wdrażanie tego samego wzorców Zarządzanie SaaS w aplikacji.


| Samouczek | Opis |
|:--|:--|
| [Wskazówki i porady dotyczące na przykład aplikacji SaaS wielodostępnym bazy danych SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Pobierz i uruchom skrypty programu PowerShell, aby przygotować części aplikacji. |
|[Wdrażanie i Eksploruj aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)|  Wdrażanie i Eksploruj aplikacji Wingtip SaaS z subskrypcją platformy Azure. |
|[Dostarczanie i katalogu dzierżawcy](saas-dbpertenant-provision-and-catalog.md)| Dowiedz się, jak aplikacja łączy się z dzierżawcy przy użyciu bazy danych katalogu oraz jak katalogu mapuje dzierżawcy do swoich danych. |
|[Monitorowanie i zarządzanie nimi wydajności](saas-dbpertenant-performance-monitoring.md)| Dowiedz się, jak używać funkcji monitorowania bazy danych SQL i Ustaw alerty po przekroczeniu progów wydajności. |
|[Monitorowanie za pomocą usługi Analiza dzienników Azure](saas-dbpertenant-log-analytics.md) | Dowiedz się, jak używać [analizy dzienników](../log-analytics/log-analytics-overview.md) do monitorowania dużej ilości zasobów przez wiele pul. |
|[Przywracanie pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)| Dowiedz się, jak przywracanie dzierżawy bazy danych do wcześniejszego punktu w czasie. Również informacje o sposobie Przywróć równoległych bazę danych, co Pozostawia istniejącą bazę danych dzierżawcy online. |
|[Zarządzanie dzierżawy schematu bazy danych](saas-tenancy-schema-management.md)| Dowiedz się, jak zaktualizować schemat i aktualizowanie danych referencyjnych dla wszystkich baz danych dzierżawy. |
|[Uruchamianie zapytań rozproszonych między dzierżawy](saas-tenancy-cross-tenant-reporting.md) | Tworzenie bazy danych analizy ad hoc i uruchamiane w czasie rzeczywistym zapytań rozproszonych w ramach wszystkich dzierżawców.  |
|[Uruchom analizy danych wyodrębnionych dzierżawy](saas-tenancy-tenant-analytics.md) | Wyodrębnianie danych dzierżawy do analityka bazy danych lub dane magazynu dla zapytania analityczne w trybie offline. |


## <a name="next-steps"></a>Kolejne kroki

- [Ogólne wskazówki i porady dotyczące wdrażanie i użytkowanie przykład aplikacji SaaS biletów Wingtip](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wdrażanie aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
