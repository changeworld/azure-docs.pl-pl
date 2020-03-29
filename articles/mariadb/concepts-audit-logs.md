---
title: Dzienniki inspekcji — usługa Azure Database for MariaDB
description: W tym artykule opisano dzienniki inspekcji dostępne w usłudze Azure Database dla mariadb i dostępne parametry włączania poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063828"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Dzienniki inspekcji w bazie danych platformy Azure dla mariadb

W usłudze Azure Database for MariaDB dziennik inspekcji jest dostępny dla użytkowników. Dziennik inspekcji może służyć do śledzenia aktywności na poziomie bazy danych i jest powszechnie używany do zgodności.

> [!IMPORTANT]
> Funkcja dziennika inspekcji jest obecnie w wersji zapoznawczej.

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

Domyślnie dziennik inspekcji jest wyłączony. Aby ją włączyć, ustaw na `audit_log_enabled` ON.

Inne parametry, które można dostosować, obejmują:

- `audit_log_events`: steruje zdarzeniami, które mają być rejestrowane. Zobacz poniższą tabelę dla konkretnych zdarzeń inspekcji.
- `audit_log_include_users`: Użytkownicy MariaDB, którzy mają zostać uwzględnieni do rejestrowania. Wartość domyślna dla tego parametru jest pusta, co obejmuje wszystkich użytkowników do rejestrowania. Ma to wyższy `audit_log_exclude_users`priorytet w stosunku do . Maksymalna długość parametru wynosi 512 znaków.
> [!Note]
> `audit_log_include_users`ma wyższy `audit_log_exclude_users`priorytet nad . Na przykład `audit_log_include_users`  =  `demouser` if `audit_log_exclude_users`  =  `demouser`i , użytkownik zostanie uwzględniony w `audit_log_include_users` dziennikach inspekcji, ponieważ ma wyższy priorytet.
- `audit_log_exclude_users`: Użytkownicy MariaDB mają zostać wykluczeni z rejestrowania. Pozwala co najwyżej czterem użytkownikom. Maksymalna długość parametru wynosi 256 znaków.

| **Zdarzenie** | **Opis** |
|---|---|
| `CONNECTION` | - Rozpoczęcie połączenia (udane lub nieudane) <br> - Ponowne uwierzytelnianie użytkownika z innym użytkownikiem / hasłem podczas sesji <br> - Zakończenie połączenia |
| `DML_SELECT`| Kwerendy SELECT |
| `DML_NONSELECT` | KWERENDY WSTAWIANIA/USUWANIA/AKTUALIZACJI |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Zapytania takie jak "DROP DATABASE" |
| `DCL` | Zapytania takie jak "UDZIELENIE zgody" |
| `ADMIN` | Zapytania takie jak "POKAŻ STATUS" |
| `GENERAL` | Wszystko w DML_SELECT, DML_NONSELECT, DML, DDL, DCL i ADMIN |

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Dzienniki inspekcji są zintegrowane z dziennikami diagnostycznymi usługi Azure Monitor. Po włączeniu dzienników inspekcji na serwerze MariaDB można je emitować do dzienników usługi Azure Monitor, centrów zdarzeń lub usługi Azure Storage. Aby dowiedzieć się więcej o tym, jak włączyć dzienniki diagnostyczne w witrynie Azure portal, zobacz [artykuł portalu dziennika inspekcji](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schematy dzienników diagnostycznych

W poniższych sekcjach opisano, co jest dane wyjściowe przez dzienniki inspekcji MariaDB na podstawie typu zdarzenia. W zależności od metody danych wyjściowych uwzględnione pola i kolejność, w jakiej się pojawiają, mogą się różnić.

### <a name="connection"></a>Połączenie

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w utc |
| `Type` | Typ dziennika. Zawsze`AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Unikatowy identyfikator połączenia wygenerowany przez MariaDB |
| `host_s` | Pusty |
| `ip_s` | Adres IP klienta łączącego się z MariaDB |
| `user_s` | Nazwa użytkownika wykonującego kwerendę |
| `db_s` | Nazwa bazy danych połączonej z |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Schemat poniżej dotyczy typów zdarzeń OGÓLNE, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i ADMIN.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w utc |
| `Type` | Typ dziennika. Zawsze`AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Sekundy rozpoczęcia kwerendy w sygnatury czasjednak systemu UNIX |
| `error_code_d` | Kod błędu, jeśli kwerenda nie powiodła się. `0`oznacza brak błędu |
| `thread_id_d` | Identyfikator wątku, który wykonał kwerendę |
| `host_s` | Pusty |
| `ip_s` | Adres IP klienta łączącego się z MariaDB |
| `user_s` | Nazwa użytkownika wykonującego kwerendę |
| `sql_text_s` | Pełny tekst kwerendy |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizowanie dzienników w dziennikach monitora platformy Azure

Gdy dzienniki inspekcji są potokami do dzienników usługi Azure Monitor za pośrednictwem dzienników diagnostycznych, można wykonać dalszą analizę zdarzeń inspekcji. Poniżej znajduje się kilka przykładowych zapytań, które pomogą Ci rozpocząć pracę. Pamiętaj, aby zaktualizować poniższą nazwę serwera.

- Wyświetlanie zdarzeń ogólne na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Wyświetlanie listy zdarzeń CONNECTION na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Podsumowywanie zdarzeń inspekcji na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Wykres dystrybucji typu zdarzenia inspekcji na określonym serwerze

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Lista zdarzeń inspekcji na wszystkich serwerach MariaDB z włączonymi dziennikami diagnostycznymi dla dzienników inspekcji

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować dzienniki inspekcji w witrynie Azure portal](howto-configure-audit-logs-portal.md)