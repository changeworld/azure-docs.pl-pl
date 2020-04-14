---
title: Powolne dzienniki zapytań — usługa Azure Database for MariaDB
description: W tym artykule opisano dzienniki dostępne w usłudze Azure Database dla mariadb i dostępne parametry włączania różnych poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272087"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Powolne dzienniki zapytań w bazie danych platformy Azure dla mariadb
W usłudze Azure Database for MariaDB dziennik wolnych zapytań jest dostępny dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwany. Dziennik wolnych zapytań może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat dziennika wolnych zapytań, zobacz dokumentację MariaDB dla [dziennika kwerend powolnych](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="configure-slow-query-logging"></a>Konfigurowanie powolnego rejestrowania zapytań
Domyślnie powolny dziennik kwerend jest wyłączony. Aby ją włączyć, ustaw na `slow_query_log` ON. Można to włączyć za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. 

Inne parametry, które można dostosować, obejmują:

- **long_query_time**: jeśli kwerenda trwa dłużej niż long_query_time (w sekundach), to kwerenda jest rejestrowana. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: jeśli ON zawiera oświadczenia administracyjne, takie jak ALTER_TABLE i ANALYZE_TABLE w oświadczeniach napisanych do slow_query_log.
- **log_queries_not_using_indexes**: określa, czy kwerendy, które nie używają indeksów są rejestrowane w slow_query_log
- **log_throttle_queries_not_using_indexes:** Ten parametr ogranicza liczbę kwerend innych niż indeks, które mogą być zapisywane w dzienniku kwerend powolnych. Ten parametr staje się skuteczny, gdy log_queries_not_using_indexes jest ustawiona na ON.
- **log_output:** jeśli "Plik", umożliwia powolne dziennik kwerendy mają być zapisywane zarówno do magazynu serwera lokalnego i do dziennika diagnostycznego usługi Azure Monitor. Jeśli "Brak", dziennik kwerend powolny zostanie zapisany tylko w dziennikach diagnostyki usługi Azure Monitor. 

> [!IMPORTANT]
> Jeśli tabele nie są indeksowane, ustawienie `log_queries_not_using_indexes` i `log_throttle_queries_not_using_indexes` parametry na ON może mieć wpływ na wydajność MariaDB, ponieważ wszystkie kwerendy uruchomione względem tych tabel nieindeksowanych zostaną zapisane w dzienniku kwerend powolnych.<br><br>
> Jeśli planujesz rejestrowanie powolnych zapytań przez dłuższy okres czasu, `log_output` zaleca się ustawienie "Brak". Jeśli ustawiona na "Plik", te dzienniki są zapisywane w magazynie serwera lokalnego i mogą mieć wpływ na wydajność MariaDB. 

Zobacz [dokumentację dziennika powolnych zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/) MariaDB, aby uzyskać pełne opisy parametrów dziennika kwerend powolnych.

## <a name="access-slow-query-logs"></a>Dostęp do wolnych dzienników kwerend
Istnieją dwie opcje uzyskiwania dostępu do dzienników wolnych zapytań w usłudze Azure Database dla MariaDB: magazyn serwera lokalnego lub dzienniki diagnostyczne usługi Azure Monitor. Jest to ustawiane przy użyciu parametru. `log_output`

W przypadku magazynu serwera lokalnego można wyświetlić listę i pobrać dzienniki wolnych zapytań przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. W witrynie Azure portal przejdź do serwera w witrynie Azure portal. W nagłówku **Monitorowanie** wybierz stronę **Dzienniki serwera.** Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie i uzyskiwanie dostępu do dzienników serwera przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-cli.md). 

Dzienniki diagnostyczne usługi Azure Monitor umożliwiają potok powolne dzienniki zapytań do dzienników usługi Azure Monitor Log Analytics, usługi Azure Storage lub Centrum zdarzeń. Zobacz [poniżej,](concepts-server-logs.md#diagnostic-logs) aby uzyskać więcej informacji.

## <a name="local-server-storage-log-retention"></a>Przechowywanie dziennika magazynu serwera lokalnego
Podczas rejestrowania do magazynu lokalnego serwera dzienniki są dostępne przez okres do siedmiu dni od ich utworzenia. Jeśli całkowity rozmiar dostępnych dzienników przekracza 7 GB, najstarsze pliki są usuwane, dopóki nie będzie dostępne miejsce.

Dzienniki są obracane co 24 godziny lub 7 GB, w zależności od tego, co nastąpi wcześniej.

> [!Note]
> Powyższe przechowywanie dziennika nie ma zastosowania do dzienników, które są potokowe przy użyciu dzienników diagnostycznych usługi Azure Monitor. Można zmienić okres przechowywania dla pochłaniaczy danych emitowanych do (np. Azure Storage).

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Usługa Azure Database for MariaDB jest zintegrowana z dziennikami diagnostycznymi usługi Azure Monitor. Po włączeniu powolnych dzienników zapytań na serwerze MariaDB można wybrać opcję ich emisji do dzienników usługi Azure Monitor, centrów zdarzeń lub usługi Azure Storage. Aby dowiedzieć się więcej o włączaniu dzienników diagnostycznych, zobacz sekcję [dokumentacji dzienników diagnostycznych](../azure-monitor/platform/platform-logs-overview.md).

W poniższej tabeli opisano, co jest w każdym dzienniku. W zależności od metody danych wyjściowych uwzględnione pola i kolejność, w jakiej się pojawiają, mogą się różnić.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w utc |
| `Type` | Typ dziennika. Zawsze`AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nazwa serwera |
| `start_time_t`[UTC] | Czas rozpoczęcia kwerendy |
| `query_time_s` | Całkowity czas wykonania kwerendy |
| `lock_time_s` | Całkowity czas zablokowania kwerendy |
| `user_host_s` | Nazwa użytkownika |
| `rows_sent_s` | Liczba wysłanych wierszy |
| `rows_examined_s` | Liczba badanych wierszy |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Identyfikator wstawiania |
| `sql_text_s` | Pełna kwerenda |
| `server_id_s` | Identyfikator serwera |
| `thread_id_s` | Identyfikator wątku |
| `\_ResourceId` | Identyfikator URI zasobu |

> [!Note]
> Dla `sql_text`, dziennik zostanie obcięty, jeśli przekracza 2048 znaków.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizowanie dzienników w dziennikach monitora platformy Azure

Gdy dzienniki kwerend powolny są potoku do dzienników usługi Azure Monitor za pośrednictwem dzienników diagnostycznych, można wykonać dalszą analizę powolnych zapytań. Poniżej znajduje się kilka przykładowych zapytań, które pomogą Ci rozpocząć pracę. Pamiętaj, aby zaktualizować poniższą nazwę serwera.

- Kwerendy dłuższe niż 10 sekund na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lista 5 najdłuższych zapytań na danym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Podsumowywanie powolnych zapytań według minimalnego, maksymalnego, średniego i czasu kwerendy odchylenia standardowego na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Wykres powolnego rozkładu zapytań na określonym serwerze

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Wyświetlanie zapytań dłuższych niż 10 sekund na wszystkich serwerach MariaDB z włączonymi dziennikami diagnostycznymi

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować dzienniki wolnych zapytań z witryny Azure Portal](howto-configure-server-logs-portal.md)
- [Jak skonfigurować powolne dzienniki zapytań z interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-cli.md)
