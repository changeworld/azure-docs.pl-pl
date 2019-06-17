---
title: Jak skonfigurować usługi Azure SQL Database — pojedynczy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i zarządzać usługi Azure SQL Database — Pojedyncza baza danych
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 4c6799372f203f021a07ae52a1d7f591aae5afad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60339010"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Jak korzystać z pojedynczej bazy danych w usłudze Azure SQL Database

W tej sekcji można znaleźć różne przewodniki, skrypty i wyjaśnieniami, które mogą ułatwić zarządzanie i konfigurowanie pojedynczej bazy danych w usłudze Azure SQL Database

## <a name="migrate"></a>Migrate (Migracja)

- [Migrowanie do usługi SQL Database](sql-database-single-database-migrate.md) — Dowiedz się więcej na temat procesu zalecane migracji i narzędzia do migracji do wystąpienia zarządzanego.
- Dowiedz się, jak [Zarządzanie usługą SQL database po migracji](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Konfigurowanie replikacji transakcyjnej](replication-to-sql-database.md) do replikowania Data między bazami danych.
- [Konfigurowanie wykrywania zagrożeń](sql-database-threat-detection.md) umożliwi identyfikowanie podejrzanych działań, takich jak wstrzykiwanie kodu SQL lub dostępu z podejrzanych lokalizacji bazy danych SQL Azure.
- [Konfigurowanie dynamicznego maskowania danych](sql-database-dynamic-data-masking-get-started-portal.md) Aby chronić dane poufne.
- [Konfigurowanie przechowywania kopii zapasowych](sql-database-long-term-backup-retention-configure.md) dla bazy danych można przechowywać kopie zapasowe w usłudze Azure Blob Storage. Alternatywą jest [konfigurowanie przechowywania kopii zapasowych przy użyciu magazynu platformy Azure (przestarzałe)](sql-database-long-term-backup-retention-configure-vault.md) podejście.
- [Konfigurowanie replikacji geograficznej](sql-database-geo-replication-portal.md) pozwala utrzymywać replikę bazy danych w innym regionie.
- [Skonfiguruj zabezpieczenia dla replik geograficznie](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorowania i dostrajania bazy danych

- [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) pozwala zoptymalizować wydajność przetwarzania obciążenia bazy danych SQL Azure.
- [Włącz powiadomienia e-mail dostrajania automatycznego](sql-database-automatic-tuning-email-notifications.md) Aby uzyskać informacje na temat zalecenia dotyczące dostrajania.
- [Stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md) i zoptymalizuj bazę danych.
- [Tworzenie alertów](sql-database-insights-alerts-portal.md) można uzyskać powiadomienia z usługi Azure SQL Database.
- [Rozwiązywanie problemów z łącznością](sql-database-troubleshoot-common-connection-issues.md) Jeśli zauważysz, że niektóre problemy z łącznością między aplikacjami i bazy danych. Można również użyć [kondycja zasobu dla problemów z łącznością](sql-database-resource-health.md).
- [Zarządzanie przestrzenią pliku](sql-database-file-space-management.md) monitorować użycie magazynu w bazie danych.

## <a name="query-distributed-data"></a>Wykonywanie zapytań względem danych rozproszonych

- [Wykonywanie zapytań względem danych partycjonowanych w pionie](sql-database-elastic-query-getting-started-vertical.md) w wielu bazach danych.
- [Raport w warstwie danych skalowanych w poziomie](sql-database-elastic-query-horizontal-partitioning.md).
- [Zapytanie względem tabel z różnymi schematami](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Zadania Elastic Database

- [Tworzenie i zarządzanie nimi](elastic-jobs-powershell.md) zadania Elastic Database przy użyciu programu PowerShell.
- [Tworzenie i zarządzanie nimi](elastic-jobs-tsql.md) zadania Elastic Database przy użyciu języka Transact-SQL.
- [Migrowanie ze starych zadania elastycznego](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Dzielenie bazy danych na fragmenty

- [Biblioteka kliencka uaktualnienia elastic database](sql-database-elastic-scale-upgrade-client-library.md).
- [Tworzenie aplikacji podzielonej na fragmenty](sql-database-elastic-scale-get-started.md).
- [Wykonywanie zapytań dotyczących danych w poziomie podzielonych na fragmenty](sql-database-elastic-query-getting-started.md).
- Uruchom [zapytania z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md).
- [Przenoszenie danych podzielonych na fragmenty](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurowanie zabezpieczeń](sql-database-elastic-scale-split-merge-security-configuration.md) we fragmentach bazy danych.
- [Dodawanie fragmentu](sql-database-elastic-scale-add-a-shard.md) w bieżącym zestawie fragmentów bazy danych.
- [Rozwiązywanie problemów z mapą fragmentów](sql-database-elastic-database-recovery-manager.md).
- [Migrowanie bazy danych podzielonej na fragmenty](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Tworzenie liczników](sql-database-elastic-database-perf-counters.md).
- [Użyj entity framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) wykonywać zapytania względem danych podzielonych na fragmenty.
- [Użyj programem Dapper framework](sql-database-elastic-scale-working-with-dapper.md) wykonywać zapytania względem danych podzielonych na fragmenty.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [instrukcje prowadzi do wystąpienia zarządzanego](sql-database-howto-managed-instance.md)
