---
title: Dzienniki inspekcji — Azure Database for MariaDB
description: Opisuje dzienniki inspekcji dostępne w Azure Database for MariaDB i dostępne parametry umożliwiające włączenie poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 64662499b4ee782bbf04e9e706cd659e84c90eec
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773079"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Inspekcja dzienników w Azure Database for MariaDB

W Azure Database for MariaDB dziennik inspekcji jest dostępny dla użytkowników. Dziennik inspekcji może służyć do śledzenia aktywności na poziomie bazy danych i jest często używany w celu zapewnienia zgodności.

> [!IMPORTANT]
> Funkcje dziennika inspekcji są obecnie dostępne w wersji zapoznawczej.

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

Domyślnie dziennik inspekcji jest wyłączony. Aby ją włączyć, ustaw wartość `audit_log_enabled` na włączone.

Inne parametry, które można dostosować, obejmują:

- `audit_log_events`: kontroluje zdarzenia, które mają być rejestrowane. Szczegółowe zdarzenia inspekcji można znaleźć w poniższej tabeli.
- `audit_log_exclude_users`: MariaDB użytkowników do wykluczenia z rejestrowania. Zezwala na maksymalnie czterech użytkowników. Maksymalna długość parametru to 256 znaków.

| **Zdarzenie** | **Opis** |
|---|---|
| `CONNECTION` | -Inicjacja połączenia (powodzenie lub niepowodzenie) <br> -Uwierzytelnianie użytkownika przy użyciu innego użytkownika/hasła podczas sesji <br> -Zakończenie połączenia |
| `DML_SELECT`| Wybieranie zapytań |
| `DML_NONSELECT` | Wstawianie/usuwanie/aktualizowanie zapytań |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Zapytania, takie jak "DROP DATABASE" |
| `DCL` | Zapytania, takie jak "Udziel uprawnień" |
| `ADMIN` | Zapytania, takie jak "Pokaż stan" |
| `GENERAL` | Wszystkie w DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administrator |

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Dzienniki inspekcji są zintegrowane z Azure Monitor dziennikami diagnostycznymi. Po włączeniu dzienników inspekcji na serwerze MariaDB można je emitować do dzienników Azure Monitor, Event Hubs lub Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych w Azure Portal, zobacz [artykuł Portal dziennika inspekcji](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schematy dzienników diagnostycznych

W poniższych sekcjach opisano dane wyjściowe przez dzienniki inspekcji MariaDB na podstawie typu zdarzenia. W zależności od metody Output pola uwzględnione i kolejność ich wyświetlania mogą się różnić.

### <a name="connection"></a>Połączenie

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Unikatowy identyfikator połączenia wygenerowany przez MariaDB |
| `host_s` | Puste |
| `ip_s` | Adres IP klienta łączącego się z usługą MariaDB |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `db_s` | Nazwa bazy danych połączonej z |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Poniższy schemat dotyczy typów zdarzeń GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administrator.

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Liczba sekund rozpoczęcia zapytania w znaczniku czasu systemu UNIX |
| `error_code_d` | Kod błędu, jeśli zapytanie nie powiodło się. `0` oznacza brak błędu |
| `thread_id_d` | Identyfikator wątku, który wykonał zapytanie |
| `host_s` | Puste |
| `ip_s` | Adres IP klienta łączącego się z usługą MariaDB |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `sql_text_s` | Pełny tekst zapytania |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="table-access"></a>Dostęp do tabeli

| **Właściwość** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| `Type` | Typ dziennika. Zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. Zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`lub `DELETE` |
| `connection_id_d` | Unikatowy identyfikator połączenia wygenerowany przez MariaDB |
| `db_s` | Nazwa bazy danych, do której uzyskano dostęp |
| `table_s` | Nazwa tabeli, do której można uzyskać dostęp |
| `sql_text_s` | Pełny tekst zapytania |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować dzienniki inspekcji w Azure Portal](howto-configure-audit-logs-portal.md)