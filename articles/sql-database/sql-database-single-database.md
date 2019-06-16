---
title: Co to jest pojedynczej bazy danych Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o pojedynczej bazy danych w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 36b1fb96ac1dd89375588a65063ce729f6ac825d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794387"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co to jest pojedynczej bazy danych w usłudze Azure SQL Database

Opcja wdrażania pojedynczej bazy danych tworzy bazę danych w usłudze Azure SQL Database przy użyciu swój własny zestaw zasobów i jest zarządzana za pośrednictwem serwera usługi SQL Database. Za pomocą pojedynczej bazy danych, każda baza danych jest odizolowana od innych i przenośna, z własną warstwę usług, w ramach [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) i gwarancji obliczenia rozmiaru.

> [!IMPORTANT]
> Pojedyncza baza danych jest jednym z trzech opcji wdrożenia usługi Azure SQL Database. Istnieją dwie [pul elastycznych](sql-database-elastic-pool.md) i [wystąpienia zarządzanego](sql-database-managed-instance.md).
> [!NOTE]
> Słownik terminów w usłudze Azure SQL Database, zobacz [słownik terminów bazy danych SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamiczna skalowalność

Możesz utworzyć swoją pierwszą aplikację na małej, pojedynczej bazy danych przy niskich kosztach w warstwa wystąpień obliczeniowych bez użycia serwera (wersja zapoznawcza) lub rozmiar małego wystąpienia obliczeniowego w warstwie zainicjowanych zasobów obliczeniowych. Możesz zmienić [warstwy obliczeń lub usługa](sql-database-single-database-scale.md) ręcznie lub programowo w dowolnym momencie, aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

## <a name="single-databases-and-elastic-pools"></a>Pojedyncze bazy danych i elastyczne pule

Pojedynczą bazę danych można przenieść do lub z [puli elastycznej](sql-database-elastic-pool.md) na potrzeby udostępniania zasobów. W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. Pule elastyczne są przeznaczone do rozwiązania tego problemu. Koncepcja jest prosta. Przydzielasz zasoby wydajności do puli, a nie poszczególnych baz danych i płać za zasoby zbiorczą wydajnością puli, a nie dla wydajności pojedynczej bazy danych.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Możesz użyć wbudowanych [monitorowania wydajności](sql-database-performance.md) i [narzędzia wysyłające alerty](sql-database-insights-alerts-portal.md), połączone z ocenami wydajności. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Ponadto usługa SQL Database może [tworzyć metryki i dzienniki diagnostyczne](sql-database-metrics-diag-logging.md), które ułatwiają monitorowanie.

## <a name="availability-capabilities"></a>Możliwości dostępności

Pojedyncze bazy danych, pul elastycznych i zarządzanych wystąpień przez zapewniają wiele charakterystykę dostępności. Aby uzyskać informacje, zobacz [charakterystykę dostępności](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Różnice w języku Transact-SQL

Większość funkcji języka Transact-SQL, których aplikacje są w pełni obsługiwane zarówno w przypadku programu Microsoft SQL Server, jak i usługi Azure SQL Database. Na przykład podstawowe elementy języka SQL, takich jak typy danych, operatory, ciąg, arytmetyczne, logiczne oraz funkcje kursora, działają tak samo programu SQL Server i bazy danych SQL. Istnieją, jednak niewielkie różnice języka T-SQL w DDL (języka definicji danych) i elementy DML (język edycji danych), w efekcie instrukcje języka T-SQL oraz zapytania, które są tylko częściowo obsługiwane (co omówiono w dalszej części tego artykułu).
Ponadto istnieją pewne funkcje i składni, która nie jest obsługiwane na wszystkich, ponieważ usługa Azure SQL Database zaprojektowano w celu uniezależnić funkcje od bazy danych master i systemu operacyjnego. W efekcie większości działań na poziomie serwera nie mają zastosowania do bazy danych SQL. Instrukcje języka T-SQL i opcje nie są dostępne, skonfiguruj opcje poziomu serwera, składników systemu operacyjnego lub określania pliku konfiguracji systemu. Gdy wymagane są takie funkcje, właściwe rozwiązanie jest często dostępne w inny sposób funkcję z bazą danych SQL lub innej platformy Azure lub usługi.

Aby uzyskać więcej informacji, zobacz [różnice rozpoznawania języka Transact-SQL podczas migracji do usługi SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Bezpieczeństwo

SQL Database oferuje szeroką gamę [wbudowanych rozwiązań zabezpieczeń i zgodności](sql-database-security-overview.md) funkcje, aby pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń i zgodności.

> [!IMPORTANT]
> Usługa Azure SQL Database (wszystkich opcjach wdrażania) zostanie certyfikowana na liczbie standardów zgodności. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najbardziej aktualną listą ze zgodnością bazy danych SQL.

## <a name="next-steps"></a>Kolejne kroki

- Aby szybko rozpocząć pracę z pojedynczej bazy danych, zacznij od [pojedynczy guide.md Szybki Start bazy danych](sql-database-single-database-quickstart-guide.md).
- Aby dowiedzieć się więcej o migracji bazy danych programu SQL Server na platformie Azure, zobacz [migracja do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).