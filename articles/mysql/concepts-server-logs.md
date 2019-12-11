---
title: Dzienniki wolnych zapytań — Azure Database for MySQL
description: Zawiera opis wolnych dzienników zapytań dostępnych w Azure Database for MySQL i dostępnych parametrów do włączania różnych poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 6bd99a200a8f9e6be6d155a334b9b06ac05eacc3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972187"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Wolne dzienniki zapytań w Azure Database for MySQL
W Azure Database for MySQL dziennik wolnych zapytań jest dostępny dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwany. Dziennik wolnych zapytań może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat dziennika wolnych zapytań programu MySQL, zobacz [sekcję dziennik wolnych zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)dotyczących usługi MySQL Reference.

## <a name="access-slow-query-logs"></a>Dostęp do dzienników wolnych zapytań
Można wyświetlać i pobierać Azure Database for MySQL wolnych dzienników zapytań przy użyciu Azure Portal i interfejsu wiersza polecenia platformy Azure.

W witrynie Azure portal wybierz usługi Azure Database for MySQL server. W obszarze nagłówek **monitorowania** wybierz stronę **Dzienniki serwera** .

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie i dostęp do dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md)

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
- **log_output**: Jeśli "plik" umożliwia zapisanie dziennika wolnych zapytań do lokalnego magazynu serwera i Azure monitor dzienników diagnostycznych. Jeśli "Brak", dziennik wolnych zapytań zostanie zapisany tylko w magazynie serwera lokalnego. 

> [!Note]
> W przypadku `sql_text`Dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

Więcej opisów parametrów dziennika wolnych zapytań można znaleźć w [dokumentacji dziennika wolnych zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) programu MySQL.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Azure Database for MySQL jest zintegrowana z Azure Monitor dzienników diagnostycznych. Po włączeniu wolnych dzienników zapytań na serwerze MySQL można wybrać opcję ich emisji do Azure Monitor dzienników, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz sekcję jak [znaleźć w dokumentacji dzienników diagnostycznych](../azure-monitor/platform/resource-logs-overview.md).

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
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t` [UTC] | Godzina rozpoczęcia zapytania |
| `query_time_s` | Łączny czas (w sekundach) wykonywania zapytania |
| `lock_time_s` | Łączny czas w sekundach, przez który zapytanie zostało zablokowane |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wysłanych wierszy |
| `rows_examined_s` | Liczba badanych wierszy |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Wstaw identyfikator |
| `sql_text_s` | Pełne zapytanie |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować i uzyskać dostęp do dzienników serwera za pomocą interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md).
