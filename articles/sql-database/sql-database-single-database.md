---
title: Co to jest pojedyncza baza danych
description: Dowiedz się więcej o pojedynczej bazie danych w bazie danych SQL usługi Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500760"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co to jest pojedyncza baza danych w bazie danych SQL usługi Azure

Opcja wdrażania pojedynczej bazy danych tworzy bazę danych w usłudze Azure SQL Database z własnym zestawem zasobów i jest zarządzana za pośrednictwem serwera bazy danych SQL. W przypadku pojedynczej bazy danych każda baza danych jest odizolowana od siebie i przenośna, każda z własną warstwą usług w [modelu zakupów opartym na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartym na wynikach wirtualnych](sql-database-service-tiers-vcore.md) i gwarantowanym rozmiarze obliczeń.

> [!IMPORTANT]
> Pojedyncza baza danych jest jedną z trzech opcji wdrażania usługi Azure SQL Database. Pozostałe dwa to [pule elastyczne](sql-database-elastic-pool.md) i [wystąpienie zarządzane](sql-database-managed-instance.md).
> [!NOTE]
> Aby uzyskać glosariusz terminów w usłudze Azure SQL Database, zobacz [glosariusz terminów bazy danych SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Skalowalność dynamiczna

Pierwszą aplikację można utworzyć na małej, pojedynczej bazie danych przy niskich kosztach w warstwie obliczeniowej bezserwerowej lub w małym rozmiarze obliczeniowym w warstwie obliczeniowej aprowizowanego. Warstwę [obliczeniową lub warstwę usług](sql-database-single-database-scale.md) można zmienić ręcznie lub programowo w dowolnym momencie, aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

## <a name="single-databases-and-elastic-pools"></a>Pojedyncze bazy danych i elastyczne pule

Pojedyncza baza danych może być przeniesiona do lub z [puli elastycznej](sql-database-elastic-pool.md) do udostępniania zasobów. W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. Pule elastyczne zostały zaprojektowane, aby rozwiązać ten problem. Koncepcja jest prosta. Zasoby wydajności można przydzielić do puli, a nie do pojedynczej bazy danych i płacić za zbiorowe zasoby wydajności puli, a nie dla wydajności pojedynczej bazy danych.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

W połączeniu z ocenami wydajności używasz wbudowanych narzędzi [do monitorowania wydajności](sql-database-performance-guidance.md) i [alertów.](sql-database-insights-alerts-portal.md) Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Ponadto baza danych SQL może [emitować metryki i dzienniki zasobów](sql-database-metrics-diag-logging.md) w celu łatwiejszego monitorowania.

## <a name="availability-capabilities"></a>Możliwości dostępności

Pojedyncze bazy danych, puli elastyczne i wystąpienia zarządzane zapewniają wiele cech dostępności. Aby uzyskać więcej informacji, zobacz [Charakterystyka dostępności](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Różnice między transact-SQL

Większość funkcji Transact-SQL, których używają aplikacje, jest w pełni obsługiwana zarówno w programie Microsoft SQL Server, jak i w bazie danych SQL Azure. Na przykład podstawowe składniki SQL, takie jak typy danych, operatory, ciąg, arytmetyka, logiczne i funkcje kursora, działają identycznie w sql server i bazy danych SQL. Istnieje jednak kilka różnic T-SQL w DDL (język definicji danych) i DML (język manipulowania danymi) elementy w wyniku instrukcji T-SQL i kwerend, które są tylko częściowo obsługiwane (które omówimy w dalszej części tego artykułu).
Ponadto istnieją pewne funkcje i składnia, która nie jest obsługiwana w ogóle, ponieważ usługa Azure SQL Database jest przeznaczona do izolowania funkcji od zależności od głównej bazy danych i systemu operacyjnego. W związku z tym większość działań na poziomie serwera są nieodpowiednie dla bazy danych SQL. Instrukcje i opcje T-SQL nie są dostępne, jeśli konfigurują opcje na poziomie serwera, składniki systemu operacyjnego lub określają konfigurację systemu plików. Gdy takie możliwości są wymagane, odpowiednia alternatywa jest często dostępna w inny sposób z bazy danych SQL lub z innej funkcji lub usługi platformy Azure.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie różnic transact-SQL podczas migracji do bazy danych SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Zabezpieczenia

Baza danych SQL zawiera szereg wbudowanych funkcji [zabezpieczeń i zgodności,](sql-database-security-overview.md) które pomagają aplikacji spełnić różne wymagania dotyczące zabezpieczeń i zgodności.

> [!IMPORTANT]
> Usługa Azure SQL Database (wszystkie opcje wdrażania) została certyfikowana zgodnie z wieloma standardami zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania platformy Microsoft Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

## <a name="next-steps"></a>Następne kroki

- Aby szybko rozpocząć pracę z pojedynczą bazą danych, zacznij od [przewodnika Szybki start pojedynczej bazy danych](sql-database-single-database-quickstart-guide.md).
- Aby dowiedzieć się więcej o migrowaniu bazy danych programu SQL Server na platformę Azure, zobacz [Migrowanie do bazy danych SQL Azure](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
