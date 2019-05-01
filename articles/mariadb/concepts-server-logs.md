---
title: Dzienniki serwera usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano dzienników dostępnych w usłudze Azure Database dla MariaDB oraz dostępne parametry włączenie rejestrowania różnych poziomów.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 8a78a9b8f0772a83e45ac2b926878e61e6ee2e61
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926332"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Dzienników serwera w usłudze Azure Database dla serwera MariaDB
W usłudze Azure Database dla serwera MariaDB dziennik dotyczący wolnego zapytania jest dostępna dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwane. Dziennik dotyczący wolnego zapytania może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat w dzienniku wolnych zapytań, zobacz dokumentację MariaDB [dziennik dotyczący wolnego zapytania](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Dzienniki dostępu serwera
Można wyświetlić listę, a następnie pobrać dzienniki serwera MariaDB przy użyciu witryny Azure portal i interfejsu wiersza polecenia platformy Azure — Azure Database.

W witrynie Azure portal wybierz usługi Azure Database dla serwera MariaDB. W obszarze **monitorowanie** nagłówka, wybierz **dzienniki serwera** strony.

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfiguruj i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Przechowywanie dzienników
Dzienniki są dostępne przez maksymalnie siedem dni od ich tworzenia. Jeśli całkowity rozmiar dostępne dzienniki przekracza 7 GB, najstarsze pliki zostaną usunięte do momentu zwolnienia miejsca.

Dzienniki zostały obrócone co 24 godziny lub 7 GB, zależnie co nastąpi wcześniej.

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby ją włączyć, ustaw slow_query_log na wartość ON.

Inne parametry, które można dostosować obejmują:

- **long_query_time**: Jeśli zapytanie dłużej niż long_query_time (w sekundach) to zapytanie jest rejestrowane. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli ON zawiera administracyjnej instrukcji takich jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisywane slow_query_log.
- **log_queries_not_using_indexes**: Określa, czy zapytań, które nie korzystają z indeksów są rejestrowane slow_query_log
- **log_throttle_queries_not_using_indexes**: Ten parametr ogranicza liczbę zapytań — indeksowanie, które mogą być zapisywane w dzienniku wolnych zapytań. Ten parametr staje się skuteczny po log_queries_not_using_indexes jest ustawiona na wartość ON.

Zobacz MariaDB [wolne dokumentacji dziennika zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/) pełne opisy parametrów dziennik wolnych zapytań.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Azure Database dla serwera MariaDB jest zintegrowany z dzienników diagnostycznych usługi Azure Monitor. Po włączeniu dzienniki wolnych zapytań serwera MariaDB, można je emitowane dzienniki usługi Azure Monitor, usługa Event Hubs lub usługi Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz, jak części [dokumentacja dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Ta funkcja diagnostyki dla dzienników serwera jest dostępna tylko w ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci [warstw cenowych](concepts-pricing-tiers.md).

W poniższej tabeli opisano, co znajduje się w każdym dzienniku. W zależności od danych wyjściowych metody, pola, znajdujące się i kolejność, w jakiej są wyświetlane mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t` [UTC] | Czas rozpoczęcia zapytania |
| `query_time_s` | Całkowity czas trwania zapytania zajęło wykonanie |
| `lock_time_s` | Całkowity czas trwania zapytania został zablokowany. |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wierszy, wysłane |
| `rows_examined_s` | Liczba wierszy, które badania |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Wstaw identyfikator |
| `sql_text_s` | Pełne zapytanie w języku |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Kolejne kroki
- [Jak skonfigurować i dostęp do dzienników serwera w witrynie Azure portal](howto-configure-server-logs-portal.md).
