---
title: Konfigurowanie i zarządzanie
description: Dowiedz się, jak skonfigurować bazę danych SQL i zarządzać nią.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209417"
---
# <a name="how-to-use-azure-sql-database"></a>Jak korzystać z bazy danych SQL usługi Azure

W tej sekcji można znaleźć różne przewodniki, skrypty i wyjaśnienia, które mogą pomóc w zarządzaniu i konfigurowaniu bazy danych SQL Azure. Można również znaleźć szczegółowe instrukcje dotyczące [pojedynczej bazy danych](sql-database-howto-single-database.md) i [wystąpienia zarządzanego](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Ładowanie danych

- [Kopiowanie pojedynczej bazy danych lub puli bazy danych na platformie Azure](sql-database-copy.md)
- [Importowanie bazy danych z pliku BACPAC](sql-database-import.md)
- [Eksportowanie bazy danych do pliku BACPAC](sql-database-export.md)
- [Ładowanie danych za pomocą protokołu BCP](sql-database-load-from-csv-with-bcp.md)
- [Ładowanie danych za pomocą usługi ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronizacja danych

- [SQL Data Sync](sql-database-sync-data.md)
- [Agent synchronizacji danych](sql-database-data-sync-agent.md)
- [Replikowanie zmian schematu](sql-database-update-sync-schema.md)
- [Monitorowanie za pomocą pakietu OMS](sql-database-sync-monitor-oms.md)
- [Najlepsze rozwiązania dotyczące funkcji Data Sync](sql-database-best-practices-data-sync.md)
- [Rozwiązywanie problemów z usługą Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorowanie i dostrajanie

- [Ręczne dostrajanie](sql-database-performance-guidance.md)
- [Korzystanie z widoków DMV do monitorowania wydajności](sql-database-monitoring-with-dmvs.md)
- [Korzystanie z magazynu zapytań do monitorowania wydajności](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Korzystanie z dziennika diagnostycznego funkcji Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorowanie miejsca OLTP w pamięci](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Rozszerzone wydarzenia](sql-database-xevent-db-diff-from-svr.md)
- [Przechowywanie zdarzeń rozszerzonych w pliku zdarzeń](sql-database-xevent-code-event-file.md)
- [Przechowywanie zdarzeń rozszerzonych w buforze pierścieniowym](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Konfigurowanie uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md)
- [Konfigurowanie dostępu warunkowego](sql-database-conditional-access.md)
- [Uwierzytelnianie wieloskładnikowe usługi AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurowanie uwierzytelniania wieloskładnikowego](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurowanie zasad przechowywania danych czasowych](sql-database-temporal-tables-retention-policy.md)
- [Konfigurowanie szyfrowania TDE przy usługi BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Obracanie kluczy BYOK szyfrowania TDE](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Usuwanie ochrony szyfrowania TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurowanie przetwarzania OLTP w pamięci](sql-database-in-memory-oltp-migration.md)
- [Konfigurowanie automatyzacji platformy Azure](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Opracowywanie aplikacji

- [Łączność](sql-database-libraries.md)
- [Korzystanie z łącznika Spark](sql-database-spark-connector.md)
- [Uwierzytelnij aplikację](sql-database-client-id-keys.md)
- [Użyj dosadowania, aby uzyskać lepszą wydajność](sql-database-use-batching-to-improve-performance.md)
- [Wskazówki dotyczące łączności](sql-database-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Konfigurowanie aliasu DNS programu PowerShell](dns-alias-powershell.md)
- [Porty — ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C i C++](sql-database-develop-cplusplus-simple.md)
- [Programu excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Projektowanie aplikacji

- [Projektowanie po kątem odzyskiwania po awarii](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Projektowanie pod kątem elastycznych pul](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Projektowanie pod kątem uaktualnień aplikacji](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Projektowanie wielodostępnych aplikacji SaaS

- [Wzorce projektowe SaaS](saas-tenancy-app-design-patterns.md)
- [Indeksator wideo SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpieczenia aplikacji SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [poradnikach dotyczących wystąpieniach zarządzanych](sql-database-howto-managed-instance.md).
- Dowiedz się więcej o [poradnikach dotyczących pojedynczych baz danych.](sql-database-howto-single-database.md)
