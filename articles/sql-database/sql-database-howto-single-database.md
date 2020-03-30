---
title: Jak skonfigurować pojedynczą bazę danych
description: Dowiedz się, jak skonfigurować bazę danych SQL Azure i zarządzać nią — pojedyncza baza danych
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027723"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Jak korzystać z pojedynczej bazy danych w bazie danych SQL usługi Azure

W tej sekcji można znaleźć różne przewodniki, skrypty i wyjaśnienia, które mogą pomóc w zarządzaniu i konfigurowaniu pojedynczej bazy danych w bazie danych SQL Azure

## <a name="migrate"></a>Migrate (Migracja)

- [Migrowanie do bazy danych SQL](sql-database-single-database-migrate.md) — dowiedz się więcej o zalecanym procesie migracji i narzędziach migracji do wystąpienia zarządzanego.
- Dowiedz się, jak [zarządzać bazą danych SQL po migracji](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Skonfiguruj replikację transakcyjną](replication-to-sql-database.md) w celu replikowania daty między bazami danych.
- [Skonfiguruj wykrywanie zagrożeń,](sql-database-threat-detection.md) aby umożliwić usłudze Azure SQL Database identyfikowanie podejrzanych działań, takich jak iniekcja SQL lub dostęp z podejrzanych lokalizacji.
- [Skonfiguruj dynamiczne maskowanie danych,](sql-database-dynamic-data-masking-get-started-portal.md) aby chronić poufne dane.
- [Skonfiguruj przechowywanie kopii zapasowych](sql-database-long-term-backup-retention-configure.md) dla bazy danych, aby zachować kopie zapasowe w usłudze Azure Blob Storage. Alternatywnie istnieje [Konfigurowanie przechowywania kopii zapasowych przy użyciu usługi Azure vault (przestarzałe)](sql-database-long-term-backup-retention-configure-vault.md) podejście.
- [Skonfiguruj replikację geograficzną,](sql-database-geo-replication-portal.md) aby zachować replikę bazy danych w innym regionie.
- [Skonfiguruj zabezpieczenia replik geograficznych](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorowanie i dostrajanie bazy danych

- [Włącz automatyczne dostrajanie,](sql-database-automatic-tuning-enable.md) aby umożliwić usługę Azure SQL Database optymalizować wydajność obciążenia.
- [Włącz powiadomienia e-mail do automatycznego dostrajania,](sql-database-automatic-tuning-email-notifications.md) aby uzyskać informacje o zaleceniach dostrajania.
- [Zastosuj zalecenia dotyczące wydajności](sql-database-advisor-portal.md) i zoptymalizuj bazę danych.
- [Tworzenie alertów](sql-database-insights-alerts-portal.md) w celu otrzymywaniu powiadomień z bazy danych SQL Azure.
- [Rozwiązywanie problemów z łącznością,](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) jeśli zauważysz pewne problemy z łącznością między aplikacjami a bazą danych. Kondycji zasobów można również używać [w przypadku problemów z łącznością](sql-database-resource-health.md).
- [Zarządzanie przestrzenią plików](sql-database-file-space-management.md) w celu monitorowania użycia magazynu w bazie danych.

## <a name="query-distributed-data"></a>Wykonywanie zapytań względem danych rozproszonych

- [Zapytanie w pionie podzielonych na partycje danych](sql-database-elastic-query-getting-started-vertical.md) w wielu bazach danych.
- [Raport w warstwie danych skalowana w poziomie](sql-database-elastic-query-horizontal-partitioning.md).
- [Zapytanie między tabelami z różnymi schematami](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Zadania Elastic Database

- [Tworzenie i zarządzanie](elastic-jobs-powershell.md) Zadania elastycznej bazy danych przy użyciu programu PowerShell.
- [Tworzenie i zarządzanie](elastic-jobs-tsql.md) Zadania elastycznej bazy danych przy użyciu funkcji Transact-SQL.
- [Migruj ze starego zadania elastycznego](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Dzielenie bazy danych na fragmenty

- [Uaktualnienie biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-upgrade-client-library.md).
- [Tworzenie aplikacji podzielonej na fragmenty](sql-database-elastic-scale-get-started.md).
- [Zapytanie o dane podzielone poziomo](sql-database-elastic-query-getting-started.md).
- Uruchamianie [kwerend wielodyskowych](sql-database-elastic-scale-multishard-querying.md).
- [Przenoszenie podzielonych danych](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurowanie zabezpieczeń](sql-database-elastic-scale-split-merge-security-configuration.md) we fragmentach bazy danych.
- [Dodaj fragment](sql-database-elastic-scale-add-a-shard.md) do bieżącego zestawu fragmentów bazy danych.
- [Rozwiązywanie problemów z mapą fragmentów](sql-database-elastic-database-recovery-manager.md).
- [Migrowanie podzielonej bazy danych](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Tworzenie liczników](sql-database-elastic-database-perf-counters.md).
- [Użyj struktury jednostki](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) do wykonywania zapytań o dane podzielonej na fragmenty.
- [Użyj dapper framework](sql-database-elastic-scale-working-with-dapper.md) do kwerendy podzielonej danych.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [poradnikach dotyczących wystąpienia zarządzanego](sql-database-howto-managed-instance.md)
