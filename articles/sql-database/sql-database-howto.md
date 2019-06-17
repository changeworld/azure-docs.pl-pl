---
title: Jak skonfigurować usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i zarządzać usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: cd2406bb4825ac295a6f9a3a18ba466cdc20c55d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120852"
---
# <a name="how-to-use-azure-sql-database"></a>Jak używać usługi Azure SQL Database

W tej sekcji można znaleźć różne przewodniki, skrypty i wyjaśnieniami, które mogą ułatwić zarządzanie i konfigurowanie usługi Azure SQL Database. Możesz również znaleźć określonego przewodniki z instrukcjami dla [pojedynczej bazy danych](sql-database-howto-single-database.md) i [wystąpienia zarządzanego](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Ładowanie danych

- [Kopiowanie pojedynczej bazy danych lub baza danych w puli w obrębie platformy Azure](sql-database-copy.md)
- [Importowanie bazy danych z pliku BACPAC](sql-database-import.md)
- [Eksportowanie bazy danych do pliku BACPAC](sql-database-export.md)
- [Ładowanie danych za pomocą narzędzia BCP](sql-database-load-from-csv-with-bcp.md)
- [Load data with ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json) (Ładowanie danych za pomocą usługi ADF)

### <a name="data-sync"></a>Synchronizacja danych

- [SQL Data Sync](sql-database-sync-data.md)
- [Agent synchronizacji danych](sql-database-data-sync-agent.md)
- [Replikowanie zmian schematu](sql-database-update-sync-schema.md)
- [Monitorowanie za pomocą pakietu OMS](sql-database-sync-monitor-oms.md)
- [Najlepsze rozwiązania dotyczące synchronizacji danych](sql-database-best-practices-data-sync.md)
- [Rozwiązywanie problemów z synchronizacji danych](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorowanie i dostrajanie

- [Ręczne dostrajanie](sql-database-performance-guidance.md)
- [Korzystanie z widoków DMV do monitorowania wydajności](sql-database-monitoring-with-dmvs.md)
- [Korzystanie z magazynu zapytań do monitorowania wydajności](sql-database-operate-query-store.md)
- [Rozwiązywanie problemów z wydajnością dzięki inteligentnej analizie](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Użyj dziennik diagnostyczny Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorowanie miejsca OLTP w pamięci](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Zdarzenia rozszerzone](sql-database-xevent-db-diff-from-svr.md)
- [Store zdarzeń rozszerzonych do pliku zdarzenia](sql-database-xevent-code-event-file.md)
- [Store zdarzeń rozszerzonych do bufor cykliczny](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Konfigurowanie uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md)
- [Konfigurowanie dostępu warunkowego](sql-database-conditional-access.md)
- [Uwierzytelnianie wieloskładnikowe dla usługi AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurowanie uwierzytelniania wieloskładnikowego](sql-database-ssms-mfa-authentication-configure.md)
- [Skonfiguruj zasady przechowywania danych czasowych](sql-database-temporal-tables-retention-policy.md)
- [Konfigurowanie funkcji TDE przy użyciu funkcji BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Obróć klucze TDE BYOK](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Usuwanie ochrony TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurowanie przetwarzania OLTP w pamięci](sql-database-in-memory-oltp-migration.md)
- [Konfigurowanie usługi Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Tworzenie aplikacji

- [Łączność](sql-database-libraries.md)
- [Korzystanie z łącznika Spark](sql-database-spark-connector.md)
- [Uwierzytelnianie aplikacji](sql-database-client-id-keys.md)
- [Komunikaty o błędach](sql-database-develop-error-messages.md)
- [Użyj, przetwarzanie wsadowe w celu zapewnienia lepszej wydajności](sql-database-use-batching-to-improve-performance.md)
- [Wskazówki dotyczące łączności](sql-database-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Konfiguracja DNS alias programu PowerShell](dns-alias-powershell.md)
- [Ports - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md) (Porty — ADO.NET)
- [C i C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Projektowanie aplikacji

- [Projektowanie po kątem odzyskiwania po awarii](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Projektowanie pod kątem pul elastycznych](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Projektowanie pod kątem uaktualnienia aplikacji](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Projektowanie wielodostępnych aplikacji SaaS

- [Wzorce projektowe SaaS](saas-tenancy-app-design-patterns.md)
- [Indeksator wideo SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpieczenia aplikacji SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [instrukcje przewodników dotyczących zarządzanych wystąpień przez](sql-database-howto-managed-instance.md).
- Dowiedz się więcej o [instrukcje prowadzi dla pojedynczych baz danych](sql-database-howto-single-database.md).
