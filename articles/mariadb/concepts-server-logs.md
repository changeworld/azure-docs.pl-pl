---
title: Dzienniki wolnych zapytań — Azure Database for MariaDB
description: Opisuje dzienniki dostępne w Azure Database for MariaDB i dostępne parametry dotyczące włączania różnych poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 651094f043162cdc5f6d522c90c7567ae94a4274
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746662"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Wolne dzienniki zapytań w Azure Database for MariaDB
W Azure Database for MariaDB dziennik wolnych zapytań jest dostępny dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwany. Dziennik wolnych zapytań może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat długiego dziennika zapytań, zobacz dokumentację MariaDB dla [wolnego dziennika zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Dostęp do dzienników wolnych zapytań
Można wyświetlać i pobierać Azure Database for MariaDB wolnych dzienników zapytań przy użyciu Azure Portal i interfejsu wiersza polecenia platformy Azure.

W Azure Portal wybierz serwer Azure Database for MariaDB. W obszarze nagłówek **monitorowania** wybierz stronę **Dzienniki serwera** .

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie i dostęp do dzienników serwera przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-cli.md).

Podobnie można potokować dzienniki, aby Azure Monitor przy użyciu dzienników diagnostycznych. Aby uzyskać więcej informacji, zobacz [poniżej](concepts-server-logs.md#diagnostic-logs) .

## <a name="log-retention"></a>Przechowywanie dziennika
Dzienniki są dostępne przez maksymalnie siedem dni od ich utworzenia. Jeśli całkowity rozmiar dostępnych dzienników przekracza 7 GB, najstarsze pliki zostaną usunięte do momentu udostępnienia miejsca.

Dzienniki są obracane co 24 godziny lub 7 GB, w zależności od tego, co nastąpi wcześniej.

## <a name="configure-slow-query-logging"></a>Skonfiguruj rejestrowanie wolnych zapytań
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby ją włączyć, ustaw wartość slow_query_log na włączone.

Inne parametry, które można dostosować, obejmują:

- **long_query_time**: Jeśli zapytanie trwa dłużej niż long_query_time (w sekundach), rejestrowane jest zapytanie. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli on zawiera instrukcje administracyjne, takie jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisanych do slow_query_log.
- **log_queries_not_using_indexes**: określa, czy zapytania, które nie używają indeksów są rejestrowane w slow_query_log
- **log_throttle_queries_not_using_indexes**: ten parametr ogranicza liczbę zapytań, które nie są indeksami, które można zapisać w dzienniku wolnych zapytań. Ten parametr zacznie obowiązywać, gdy log_queries_not_using_indexes jest ustawiona na wartość włączone.
- **log_output**: Jeśli "plik" umożliwia zapisanie dziennika wolnych zapytań do lokalnego magazynu serwera i Azure monitor dzienników diagnostycznych. W przypadku braku "Dziennik wolnych zapytań zostanie zapisany tylko w przypadku dzienników diagnostyki Azure Monitor. 

Zapoznaj się z [dokumentacją dziennika wolnych zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/) MariaDB w celu uzyskania pełnych opisów parametrów dziennika wolnych zapytań.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Azure Database for MariaDB jest zintegrowana z Azure Monitor dzienników diagnostycznych. Po włączeniu wolnych dzienników zapytań na serwerze MariaDB można je wyemitować do dzienników Azure Monitor, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz sekcję jak [znaleźć w dokumentacji dzienników diagnostycznych](../azure-monitor/platform/platform-logs-overview.md).

> [!IMPORTANT]
> Ta funkcja diagnostyczna dla dzienników serwera jest dostępna tylko w [warstwach cenowych](concepts-pricing-tiers.md)ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

W poniższej tabeli opisano zawartość poszczególnych dzienników. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t` [UTC] | Godzina rozpoczęcia zapytania |
| `query_time_s` | Łączny czas wykonywania zapytania |
| `lock_time_s` | Łączny czas zablokowania zapytania |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wysłanych wierszy |
| `rows_examined_s` | Liczba badanych wierszy |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Wstaw identyfikator |
| `sql_text_s` | Pełne zapytanie |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować i uzyskać dostęp do dzienników serwera z Azure Portal](howto-configure-server-logs-portal.md).
