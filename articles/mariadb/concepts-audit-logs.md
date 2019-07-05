---
title: Dzienniki inspekcji dla usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano dzienników inspekcji dostępnych w usłudze Azure Database dla MariaDB oraz dostępne parametry umożliwiające poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 13ea60c62283db35ce4bf9fde6c3b36ba7f88013
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439217"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Dzienniki inspekcji w usłudze Azure Database dla serwera MariaDB

W usłudze Azure Database dla serwera MariaDB dziennika inspekcji jest dostępna dla użytkowników. Dziennik inspekcji może służyć do śledzenia działań na poziomie bazy danych i jest często używany pod kątem zgodności.

> [!IMPORTANT]
> Funkcja dziennika inspekcji jest obecnie w wersji zapoznawczej.

## <a name="configure-audit-logging"></a>Skonfiguruj rejestrowanie inspekcji

Domyślnie dziennik inspekcji jest wyłączona. Aby ją włączyć, ustaw `audit_log_enabled` na wartość ON.

Inne parametry, które można dostosować obejmują:

- `audit_log_events`: Określa zdarzenia, które mają być rejestrowane. Zobacz w poniższej tabeli dla określonych zdarzeń inspekcji.
- `audit_log_exclude_users`: MariaDB użytkownicy mają być wykluczone z rejestrowania. Zezwala na maksymalnie czterech użytkowników. Maksymalna długość parametru wynosi 256 znaków.

| **Event** | **Opis** |
|---|---|
| `CONNECTION` | — Inicjowanie połączenia (powodzeniem lub niepowodzeniem) <br> — Ponowne uwierzytelnianie użytkownika przy użyciu innego użytkownika/hasła podczas sesji <br> — Zakończenie połączenie |
| `DML_SELECT`| Zapytań SELECT |
| `DML_NONSELECT` | Zapytania DELETE/INSERT/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Kwerend, takich jak "DROP DATABASE" |
| `DCL` | Kwerend, takich jak "UDZIEL uprawnień" |
| `ADMIN` | Kwerend, takich jak "Pokaż stan" |
| `GENERAL` | Wszystko to w DML_SELECT DML_NONSELECT, DML, DDL, DCL i administratora |

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Dzienniki inspekcji są zintegrowane z dzienników diagnostycznych usługi Azure Monitor. Po włączeniu dzienniki inspekcji na serwerze MariaDB, może emitować je do dzienników usługi Azure Monitor, usługa Event Hubs lub usługi Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych w witrynie Azure portal, zobacz [artykułu portalu dziennika inspekcji](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schematy dla dzienników diagnostycznych

W poniższych sekcjach opisano, co to jest danymi wyjściowymi MariaDB dzienniki inspekcji na podstawie typu zdarzenia. W zależności od danych wyjściowych metody, pola, znajdujące się i kolejność, w jakiej są wyświetlane mogą się różnić.

### <a name="connection"></a>Połączenie

| **Property** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Identyfikator unikatowy połączenia wygenerowane przez MariaDB |
| `host_s` | Puste |
| `ip_s` | Adres IP klienta, nawiązywania połączenia z MariaDB |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `db_s` | Nazwa bazy danych połączone |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Schemat poniżej dotyczy ogólne, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administratora typów zdarzeń.

| **Property** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Zapytanie start sekund w sygnatura czasowa systemu UNIX |
| `error_code_d` | Kod błędu, jeśli zapytanie nie powiodło się. `0` oznacza, że błąd nie |
| `thread_id_d` | Identyfikator wątku, który jest wykonywane zapytanie |
| `host_s` | Puste |
| `ip_s` | Adres IP klienta, nawiązywania połączenia z MariaDB |
| `user_s` | Nazwa użytkownika wykonującego zapytanie |
| `sql_text_s` | Tekst pełnej kwerendy |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="table-access"></a>Dostępu do tabel

| **Property** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nazwa serwera |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, lub `DELETE` |
| `connection_id_d` | Identyfikator unikatowy połączenia wygenerowane przez MariaDB |
| `db_s` | Nazwa bazy danych |
| `table_s` | Nazwa tabeli dostępne |
| `sql_text_s` | Tekst pełnej kwerendy |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować dzienniki inspekcji w witrynie Azure portal](howto-configure-audit-logs-portal.md)