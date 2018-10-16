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
ms.date: 10/15/2018
ms.openlocfilehash: fc97aa18328fafc299ad941e6bf12dd21e9029d0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345292"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Usługa Azure SQL Database odbywa się automatycznie i usługi danych elastyczne, gdzie użytkownik może łatwo monitorować użycie, dodawanie lub usuwanie zasobów (procesor CPU, pamięć, we/wy), Znajdź zaleceń, które mogą zwiększyć wydajność bazy danych lub pozwolić dostosowywać się do obciążenia bazy danych i automatycznie zoptymalizować wydajność.

## <a name="the-state-of-an-active-query"></a>Stan aktywne zapytanie

Aby poprawić wydajność usługi Azure SQL Database, Dowiedz się, że każde żądanie aktywne zapytanie z aplikacji znajduje się w bieżących lub stan oczekujący. Podczas rozwiązywania problemów z wydajnością w usłudze Azure SQL Database, pamiętać o następujących wykresu:

![Stany obciążenia](./media/sql-database-monitor-tune-overview/workload-states.png)

W przypadku obciążeń z problemów z wydajnością, wydajność wystawiania Moje się z powodu rywalizacji o zasoby procesora CPU ( **dotyczące uruchamiania** warunku) lub poszczególnych zapytań oczekuje na coś ( **oczekiwania dotyczące** warunku) .

- **Intensywne użycie procesora CPU w usłudze Azure SQL database**:

  Może zostać wyświetlony nadmiernego użycia Procesora wykorzystanie powodujące problemy z wydajnością w następujących warunkach:

  - Zbyt wiele uruchomionych zapytań
  - Zbyt wiele kompilacji zapytań
  - Co najmniej jeden wykonywanie zapytań jest przy użyciu planu optymalne zapytania

  Jeśli jest to w przypadku obciążenia, celem użytkownika jest identyfikowanie i dostosowywanie skojarzone zapytania lub Uaktualnij rozmiar obliczeń lub warstwę można zwiększyć pojemność usługi Azure SQL database w celu ochrony przed rozproszonymi wymagania dotyczące procesora CPU usługi. Aby uzyskać więcej informacji w skalowaniu zasobów dla pojedynczych baz danych, zobacz [skalowanie pojedynczej bazy danych zasobów w usłudze Azure SQL Database](sql-database-single-database-scale.md) i skalowania zasobów dla pul elastycznych, zobacz [skalowanie elastycznej puli zasobów w usłudze Azure SQL Baza danych](sql-database-elastic-pool-scale.md).

- **Poszczególnych zapytań oczekuje na coś**

  Pojedynczych zapytań mogą mieć problemy z wydajnością z powodu zapytań Trwa oczekiwanie na coś. W tym scenariuszu celem użytkownika jest usunięcie lub zmniejszyć czas oczekiwania.

### <a name="determine-if-you-have-a-running-related-performance-issue"></a>Określić, czy istnieją problemu z wydajnością dotyczące uruchamiania

Można zidentyfikować problemy z wydajnością dotyczące uruchamiania przy użyciu kilku metod. Najbardziej typowe metody to:

- Użyj [witryny Azure portal](#monitor-databases-using-the-azure-portal) monitorować wykorzystanie procesora CPU w procentach.
- Należy użyć następującego [dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych Azure SQL Database. Dla co 15 sekund, istnieje jeden wiersz, nawet jeśli nie ma działania w bazie danych. Dane historyczne są utrzymywane przez jedną godzinę.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca dane dotyczące użycia i magazynu procesora CPU dla usługi Azure SQL Database. Dane są zbierane i agregowane w 5 minutowych interwałach.

> [!TIP]
> Ogólną wytyczną wykorzystaniu Procesora jest stale wynosiło co najmniej 80%, przypadku problemów z wydajnością powiązane działania.

### <a name="determine-if-you-have-a-waiting-related-performance-issue"></a>Określić, czy masz problem z wydajnością związanych z oczekiwania

Najpierw mieć pewność, że nie jest problem z wydajnością procesora CPU na wysoki, powiązanego z uruchomiona. Jeśli nie jest dostępne, następnym krokiem jest określenie najważniejszych czeka skojarzone z obciążenia aplikacji.  Typowe metody wyświetlane u góry oczekiwania kategorii typów:

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) udostępnia statystyki oczekiwania na zapytanie wraz z upływem czasu. W Store zapytania oczekiwania typów są połączone w kategoriach oczekiwania. Mapowanie oczekiwania kategorie typów oczekiwania jest dostępna w [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) zwraca informacje o wszystkich czeka napotykanych przez wątki, które są wykonywane podczas operacji. Ten widok zagregowane umożliwia diagnozowanie problemów z wydajnością za pomocą usługi Azure SQL Database, a także przy użyciu określonego zapytania i partie.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) zwraca informacje o kolejce oczekiwania zadań, które oczekują na niektórych zasobów.

Jak pokazano na poprzednim wykresie, czeka najczęściej są:

- Blokady (blokowanie)
- OPERACJE WE/WY
- rywalizację związane z bazy danych tempdb
- Czeka przydział pamięci

W zależności od wyświetlanych każda kategoria oczekiwania ma inną ścieżkę rozwiązywania problemów.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Omówienie monitorowania wydajności bazy danych w usłudze Azure SQL Database

Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Monitorowanie pomaga ustalić, czy baza danych ma nadmiarowej pojemności lub występują problemy, ponieważ zasoby maksymalnego limitu, a następnie zdecydować, czy nadszedł czas, aby dopasować rozmiar obliczeń i warstwy bazy danych w usługi [zakupu opartego na jednostkach DTU model](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). Możesz monitorować bazy danych w [witryny Azure portal](https://portal.azure.com) za pomocą następujących narzędzi graficznych lub przy użyciu języka SQL [dynamicznych widoków zarządzania (DMV)](sql-database-monitoring-with-dmvs.md).

Usługa Azure SQL Database umożliwia określenie możliwości w celu zwiększenia i zoptymalizowania wydajności zapytań bez konieczności zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Można zastosować te zalecenia dotyczące dostrajania, aby zwiększyć wydajność przetwarzania obciążenia.
Możesz również pozwalają bazy danych Azure SQL, aby [automatycznie zoptymalizować wydajność kwerend](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i weryfikowanie, czy poprawiają wydajność bazy danych. Masz następujące opcje monitorowania i rozwiązywanie problemów z wydajnością bazy danych:

- W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **baz danych SQL**wybierz bazy danych, a następnie użyć wykres monitorowanie wyszukiwać zasoby zbliża się do ich maksymalnej. Użycie jednostek DTU jest wyświetlana domyślnie. Kliknij przycisk **Edytuj** zmienić zakres czasu i wartości wyświetlane.
- Użyj [Query Performance Insight](sql-database-query-performance.md) można identyfikować zapytania, które możesz wydać na w pełni korzystać z zasobów.
- Użyj [SQL Database Advisor](sql-database-advisor-portal.md) Aby wyświetlić zalecenia dotyczące tworzenia i usuwanie indeksów, parametryzacji zapytań i naprawia problemy ze schematem.
- Użyj [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) automatyczne monitorowanie wydajności bazy danych. Po wykryciu problemu z wydajnością, dziennik diagnostyczny jest generowany ze szczegółami i głównej przyczyny Analysis (analiza głównej przyczyny) problemu. Zalecenie dotyczące poprawy wydajności znajduje się, gdy jest to możliwe.
- [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) i pozwól SQL Azure, bazy danych automatycznego rozwiązywania zidentyfikowanych problemów z wydajnością.
- Możesz również użyć [dynamicznych widoków zarządzania (DMV)](sql-database-monitoring-with-dmvs.md), [zdarzeniom rozszerzonym (`XEvents`) (sql-database/sql-database-xevent-db-diff-from-svr.md) i [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) w celu uzyskania wydajności Parametry w czasie rzeczywistym. Zobacz [wskazówki dotyczące wydajności](sql-database-performance-guidance.md) można znaleźć technik, które można użyć, aby poprawić wydajność usługi Azure SQL Database, jeśli znajdziesz problem za pomocą tych raportów lub widoków.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal

W witrynie [Azure Portal](https://portal.azure.com/) możesz wybrać bazę danych i kliknąć wykres **Monitorowanie**, aby monitorować wykorzystanie pojedynczej bazy danych. Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

- Procent użycia procesora CPU
- Procent użycia jednostek DTU
- Procent użycia operacji we/wy na danych
- Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk możesz nadal wyświetlać je w **monitorowanie** wykresu z dodatkowymi informacjami na **metryki** okna. Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) artykuły, aby uzyskać więcej informacji na temat warstw usługi.  

![Monitorowanie warstw usług pod względem wydajności bazy danych.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Możesz również skonfigurować alerty dotyczące metryk wydajności. Kliknij przycisk **Dodaj alert** w oknie **Metryka**. Użyj kreatora, aby skonfigurować alert. Istnieje możliwość ustawienia alertu, który będzie wysyłany, gdy metryki przekroczą określony próg lub spadną poniżej określonego progu.

Jeśli na przykład spodziewasz się, że obciążenie bazy danych wzrośnie, możesz skonfigurować alert polegający na wysłaniu wiadomości e-mail, gdy dowolna z metryk wydajności osiągnie 80%. Umożliwia to jako wczesne ostrzeżenie zorientować się, kiedy może być konieczne Przełącz na następny większy rozmiar obliczeń.

Metryki wydajności może również pomóc w określeniu, jeśli jesteś w stanie zmiany na starszą niższe rozmiaru obliczeń. Załóżmy, że używasz bazy danych, której warstwa i poziom to Standardowa S2, a wszystkie metryki wydajności pokazują, że baza danych wykorzystuje średnio nie więcej niż 10% w określonym czasie. Istnieje prawdopodobieństwo, że baza danych będzie działać równie dobrze na poziomie Standardowa S1. Jednak należy pamiętać o obciążeniach, które chwilowo wzrastają przed podjęciem decyzji o przeniesieniu na niższym rozmiaru obliczeń lub.

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
