---
title: Monitorowanie i dostrajanie wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Porady dotyczące dostrajania w usłudze Azure SQL Database do oceny i poprawy wydajności.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 60f60b9ef055ea38a2036c4f9c5b6aa2c1f6526d
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648058"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Usługa Azure SQL Database odbywa się automatycznie i usługi danych elastyczne, gdzie użytkownik może łatwo monitorować użycie, dodawanie lub usuwanie zasobów (procesor CPU, pamięć, we/wy), Znajdź zaleceń, które mogą zwiększyć wydajność bazy danych lub pozwolić dostosowywać się do obciążenia bazy danych i automatycznie zoptymalizować wydajność.

## <a name="monitoring-database-performance"></a>Monitorowanie wydajności bazy danych

Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Usługa Azure SQL Database umożliwia określenie możliwości w celu zwiększenia i zoptymalizowania wydajności zapytań bez konieczności zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Można zastosować te zalecenia dotyczące dostrajania, aby zwiększyć wydajność przetwarzania obciążenia. Możesz również pozwalają bazy danych Azure SQL, aby [automatycznie zoptymalizować wydajność kwerend](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i weryfikowanie, czy poprawiają wydajność bazy danych.

Masz następujące opcje monitorowania i rozwiązywanie problemów z wydajnością bazy danych:

- W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **baz danych SQL**wybierz bazy danych, a następnie użyć wykres monitorowanie wyszukiwać zasoby zbliża się do ich maksymalnej. Użycie jednostek DTU jest wyświetlana domyślnie. Kliknij przycisk **Edytuj** zmienić zakres czasu i wartości wyświetlane.
- Użyj [Query Performance Insight](sql-database-query-performance.md) można identyfikować zapytania, które możesz wydać na w pełni korzystać z zasobów.
- Użyj [SQL Database Advisor](sql-database-advisor-portal.md) Aby wyświetlić zalecenia dotyczące tworzenia i usuwanie indeksów, parametryzacji zapytań i naprawia problemy ze schematem.
- Użyj [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) automatyczne monitorowanie wydajności bazy danych. Po wykryciu problemu z wydajnością, dziennik diagnostyczny jest generowany ze szczegółami i głównej przyczyny Analysis (analiza głównej przyczyny) problemu. Zalecenie dotyczące poprawy wydajności znajduje się, gdy jest to możliwe.
- [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) i pozwól SQL Azure, bazy danych automatycznego rozwiązywania zidentyfikowanych problemów z wydajnością.
- Użyj [dynamicznych widoków zarządzania (DMV)](sql-database-monitoring-with-dmvs.md), [zdarzeniom rozszerzonym](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-xevent-db-diff-from-svr)i [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) szczegółowe Rozwiązywanie problemów z wydajnością.

> [!TIP]
> Zobacz [wskazówki dotyczące wydajności](sql-database-performance-guidance.md) można znaleźć technik, które można użyć w celu poprawy wydajności usługi Azure SQL Database po zidentyfikowaniu problemu z wydajnością za pomocą co najmniej jednym z powyższych metod.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal

W [witryny Azure portal](https://portal.azure.com/), możesz monitorować wykorzystanie pojedynczej bazy danych s, wybrać bazę danych, a następnie klikając polecenie **monitorowanie** wykresu. Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

- Procent użycia procesora CPU
- Procent użycia jednostek DTU
- Procent użycia operacji we/wy na danych
- Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk możesz nadal wyświetlać je w **monitorowanie** wykresu z dodatkowymi informacjami na **metryki** okna. Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) artykuły, aby uzyskać więcej informacji na temat warstw usługi.  

![Monitorowanie warstw usług pod względem wydajności bazy danych.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Możesz również skonfigurować alerty dotyczące metryk wydajności. Kliknij przycisk **Dodaj alert** w oknie **Metryka**. Użyj kreatora, aby skonfigurować alert. Istnieje możliwość ustawienia alertu, który będzie wysyłany, gdy metryki przekroczą określony próg lub spadną poniżej określonego progu.

Jeśli na przykład spodziewasz się, że obciążenie bazy danych wzrośnie, możesz skonfigurować alert polegający na wysłaniu wiadomości e-mail, gdy dowolna z metryk wydajności osiągnie 80%. Umożliwia to jako wczesne ostrzeżenie zorientować się, kiedy może być konieczne przejdź do następnego najwyższy rozmiaru obliczeń.

Metryki wydajności może również pomóc w określeniu, jeśli jesteś w stanie zmiany na starszą niższe rozmiaru obliczeń. Załóżmy, że używasz bazy danych, której warstwa i poziom to Standardowa S2, a wszystkie metryki wydajności pokazują, że baza danych wykorzystuje średnio nie więcej niż 10% w określonym czasie. Istnieje prawdopodobieństwo, że baza danych będzie działać równie dobrze na poziomie Standardowa S1. Jednak należy pamiętać o obciążeniach, które chwilowo wzrastają przed podjęciem decyzji o przeniesieniu na niższym rozmiaru obliczeń lub.

## <a name="troubleshoot-performance-issues"></a>Rozwiązywanie problemów z wydajnością

Aby zdiagnozować i rozwiązać problemy z wydajnością, Rozpocznij od zrozumienia stan każdej aktywnej kwerendy i warunki powodujące problemy z wydajnością istotne dla każdego stanu obciążenia. Aby poprawić wydajność usługi Azure SQL Database, Dowiedz się, że każde żądanie aktywne zapytanie z aplikacji znajduje się w bieżących lub stan oczekujący. Podczas rozwiązywania problemów z wydajnością w usłudze Azure SQL Database, pamiętać poniższej tabeli jako możesz przeczytać ten artykuł, aby zdiagnozować i rozwiązać problemy z wydajnością.

![Stany obciążenia](./media/sql-database-monitor-tune-overview/workload-states.png)

W przypadku obciążeń z problemów z wydajnością, to problem z wydajnością może być spowodowane rywalizacji o zasoby procesora CPU ( **dotyczące uruchamiania** warunku) lub poszczególnych zapytań oczekuje na coś ( **oczekiwania dotyczące** warunku ).

## <a name="running-related-performance-issues"></a>Problemy z wydajnością dotyczące uruchamiania

Ogólną wytyczną wykorzystaniu Procesora jest stale wynosiło co najmniej 80%, przypadku problemów z wydajnością powiązane działania. Jeśli masz problem z dotyczące uruchamiania, może być spowodowany przez niewystarczające zasoby procesora CPU lub może być związany z jednym z następujących warunków:

- Zbyt wiele uruchomionych zapytań
- Zbyt wiele kompilacji zapytań
- Jedna lub więcej wykonywanie kwerend korzystają z planu optymalne zapytania

Jeśli okaże się, że masz problemu z wydajnością dotyczące uruchamiania, celem użytkownika jest identyfikowanie problemu dokładne przy użyciu co najmniej jednej metody. Najczęściej stosowanymi metodami do identyfikowania problemów dotyczących uruchamiania są:

- Użyj [witryny Azure portal](#monitor-databases-using-the-azure-portal) monitorować wykorzystanie procesora CPU w procentach.
- Należy użyć następującego [dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych Azure SQL Database. Dla co 15 sekund, istnieje jeden wiersz, nawet jeśli nie ma działania w bazie danych. Dane historyczne są utrzymywane przez jedną godzinę.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca dane dotyczące użycia i magazynu procesora CPU dla usługi Azure SQL Database. Dane są zbierane i agregowane w 5 minutowych interwałach.

> [!IMPORTANT]
> Aby uzyskać zestaw zapytań T-SQL, przy użyciu tych widoków DMV, aby rozwiązać problemy dotyczące użycia procesora CPU, zobacz [problemy z wydajnością Procesora zidentyfikować](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

Po zidentyfikowaniu problemu, można dostrajanie problematycznych zapytań lub Uaktualnij rozmiar obliczeń lub warstwę można zwiększyć pojemność usługi Azure SQL database w celu ochrony przed rozproszonymi wymagania dotyczące procesora CPU usługi. Aby uzyskać informacje na temat skalowania zasobów dla pojedynczych baz danych, zobacz [skalowanie pojedynczej bazy danych zasobów w usłudze Azure SQL Database](sql-database-single-database-scale.md) i skalowania zasobów dla pul elastycznych, zobacz [skalowanie elastycznej puli zasobów w usłudze Azure SQL Baza danych](sql-database-elastic-pool-scale.md). Aby uzyskać informacje na temat skalowania wystąpienia zarządzanego, zobacz [limity zasobów na poziomie wystąpienia](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

## <a name="waiting-related-performance-issues"></a>Problemy z wydajnością związanych z oczekiwania

Gdy masz pewność, że nie występują wysoka-procesor CPU, problem z wydajnością związanych z uruchamiania, występują problemu z wydajnością związanych z oczekiwania. To znaczy zasobów procesora CPU nie są używane efektywne, ponieważ Procesor oczekuje na innego zasobu. W tym przypadku następnym krokiem jest do identyfikowania zasobów procesora CPU oczekują na. Najczęstsze metody wyświetlane u góry oczekiwania kategorii typów:

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) udostępnia statystyki oczekiwania na zapytanie wraz z upływem czasu. W Store zapytania oczekiwania typów są połączone w kategoriach oczekiwania. Mapowanie oczekiwania kategorie typów oczekiwania jest dostępna w [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) zwraca informacje o wszystkich czeka napotykanych przez wątki, które są wykonywane podczas operacji. Ten widok zagregowane umożliwia diagnozowanie problemów z wydajnością za pomocą usługi Azure SQL Database, a także przy użyciu określonego zapytania i partie.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) zwraca informacje o kolejce oczekiwania zadań, które oczekują na niektórych zasobów.

Jak pokazano na poprzednim wykresie, czeka najczęściej są:

- Blokady (blokowanie)
- OPERACJE WE/WY
- `tempdb`-związane z rywalizacji o zasoby
- Czeka przydział pamięci

> [!IMPORTANT]
> Dla zestawu zapytań T-SQL, przy użyciu tych widoków DMV do rozwiązywania tych problemów związanych z oczekiwania, zobacz:
>
> - [Identyfikowanie problemów z wydajnością operacji We/Wy](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identyfikowanie `tempdb` problemy z wydajnością](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identyfikowanie czeka przydział pamięci](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

## <a name="improving-database-performance-with-more-resources"></a>Poprawa wydajności bazy danych przy użyciu większej ilości zasobów

Na koniec Brak elementów informacje z możliwością działania, które może poprawić wydajność bazy danych, możesz zmienić ilość zasobów dostępnych w usłudze Azure SQL Database. Można przypisać więcej zasobów, zmieniając [warstwy usługi jednostki DTU](sql-database-service-tiers-dtu.md) pojedynczej bazy danych lub zwiększenie jednostek Edtu elastycznej puli w dowolnej chwili. Alternatywnie Jeśli używasz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md), możesz zmienić warstwę usługi lub zwiększenie zasobów przydzielonych do bazy danych.

1. Dla pojedynczych baz danych, możesz [zmienianie warstw usług](sql-database-service-tiers-dtu.md) lub [zasoby obliczeniowe](sql-database-service-tiers-vcore.md) na żądanie w celu poprawy wydajności bazy danych.
2. W przypadku wielu baz danych można rozważyć użycie [pul elastycznych](sql-database-elastic-pool-guidance.md) automatycznie skalować zasoby.

## <a name="tune-and-refactor-application-or-database-code"></a>Dostrajanie i refaktoryzacji aplikacji lub kodu bazy danych

Możesz zmienić kod aplikacji, aby bardziej optymalnie korzystania z bazy danych, zmień indeksów, wymusić plany lub użyj wskazówek, aby ręcznie dostosować bazy danych do obciążenia. Znajdź niektóre wskazówki i porady dotyczące ręcznego dostosowywania i ponowne napisanie kodu w [tematu wskazówki dotyczące wydajności](sql-database-performance-guidance.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć automatyczne dostrajanie w usłudze Azure SQL Database i umożliwiają zarządzanie obciążeniem pracą zapewnia w pełni funkcja dostrajania automatycznego, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby korzystać z ręcznego dostrajania, możesz przejrzeć [dostrajania zalecenia w witrynie Azure portal](sql-database-advisor-portal.md) i ręcznie zastosować te, które poprawić wydajność zapytań.
- Zmienić zasoby, które są dostępne w bazie danych, zmieniając [warstwy usługi Azure SQL Database](sql-database-performance-guidance.md)
