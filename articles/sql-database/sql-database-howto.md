---
title: Jak skonfigurować usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i zarządzać usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440506"
---
# <a name="how-to-use-azure-sql-database"></a>Jak używać usługi Azure SQL Database

W tej sekcji można znaleźć różne przewodniki, skrypty i wyjaśnieniami, które mogą ułatwić zarządzanie i konfigurowanie usługi Azure SQL Database. Możesz również znaleźć określonego przewodniki z instrukcjami dla [pojedynczej bazy danych](sql-database-howto-single-database.md) i [wystąpienia zarządzanego](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Ładowanie danych

- [Skopiuj pojedynczej bazy danych w obrębie platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Importowanie bazy danych z pliku BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Eksportowanie bazy danych do pliku BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Ładowanie danych za pomocą narzędzia BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Load data with ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json) (Ładowanie danych za pomocą usługi ADF)

### <a name="data-sync"></a>Synchronizacja danych

- [SQL Data Sync](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Agent synchronizacji danych](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Replikowanie zmian schematu](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Monitorowanie za pomocą pakietu OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Najlepsze rozwiązania dotyczące synchronizacji danych](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Rozwiązywanie problemów z synchronizacji danych](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Monitorowanie i dostrajanie

-  [Ręczne dostrajanie](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Korzystanie z widoków DMV do monitorowania wydajności](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Korzystanie z magazynu zapytań do monitorowania wydajności](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Rozwiązywanie problemów z wydajnością dzięki inteligentnej analizie](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Użyj dziennik diagnostyczny Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Monitorowanie miejsca OLTP w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Zdarzenia rozszerzone

- [Zdarzenia rozszerzone](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Store zdarzeń rozszerzonych do pliku zdarzenia](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Store zdarzeń rozszerzonych do bufor cykliczny](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Konfigurowanie uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Konfigurowanie dostępu warunkowego](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Uwierzytelnianie wieloskładnikowe dla usługi AAD](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurowanie uwierzytelniania wieloskładnikowego](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Skonfiguruj zasady przechowywania danych czasowych](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Konfigurowanie funkcji TDE przy użyciu funkcji BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Obróć klucze TDE BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Usuwanie ochrony TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Konfigurowanie przetwarzania OLTP w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Konfigurowanie usługi Azure Automation](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Tworzenie aplikacji

- [Łączność](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Korzystanie z łącznika Spark](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Uwierzytelnianie aplikacji](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Komunikaty o błędach](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Użyj, przetwarzanie wsadowe w celu zapewnienia lepszej wydajności](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Wskazówki dotyczące łączności](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [Aliasy DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Konfiguracja DNS alias programu PowerShell](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Ports - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12) (Porty — ADO.NET)
- [C i C++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Projektowanie aplikacji

- [Projektowanie pod kątem odzyskiwania po awarii](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Projektowanie pod kątem pul elastycznych](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Projektowanie pod kątem uaktualnienia aplikacji](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Projektowanie wielodostępnych aplikacji SaaS

- [Wzorce projektowe SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [Indeksator wideo SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Zabezpieczenia aplikacji SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [instrukcje przeprowadza w wystąpieniu zarządzanym](sql-database-howto-managed-instance.md).
- Dowiedz się więcej o [instrukcje przeprowadza w pojedynczej bazy danych](sql-database-howto-single-database.md).
