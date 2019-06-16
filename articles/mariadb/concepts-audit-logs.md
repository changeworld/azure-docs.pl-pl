---
title: Dzienniki inspekcji dla usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano dzienników inspekcji dostępnych w usłudze Azure Database dla MariaDB oraz dostępne parametry umożliwiające poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 765db8461465b74ac068782c1b91d3c68b73f7d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079524"
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

## <a name="schemas"></a>Schematy

W poniższych sekcjach opisano, co to jest danymi wyjściowymi MariaDB dzienniki inspekcji na podstawie typu zdarzenia. W zależności od danych wyjściowych metody, pola, znajdujące się i kolejność, w jakiej są wyświetlane mogą się różnić.

### <a name="connection"></a>Połączenie

| **Property** | **Opis** |
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
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT` |
| `connection_id` | Identyfikator unikatowy połączenia wygenerowane przez MariaDB |
| `host` | Puste |
| `ip` | Adres IP klienta, nawiązywania połączenia z MariaDB |
| `user` | Nazwa użytkownika wykonującego zapytanie |
| `db` | Nazwa bazy danych połączone |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Schemat poniżej dotyczy ogólne, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administratora typów zdarzeń.

| **Property** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Sygnatura czasowa podczas rejestrowania tshe w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Zapytanie start sekund w sygnatura czasowa systemu UNIX |
| `error_code` | Kod błędu, jeśli zapytanie nie powiodło się. `0` oznacza, że błąd nie |
| `thread_id` | Identyfikator wątku, który jest wykonywane zapytanie |
| `host` | Puste |
| `ip` | Adres IP klienta, nawiązywania połączenia z MariaDB |
| `user` | Nazwa użytkownika wykonującego zapytanie |
| `sql_text` | Tekst pełnej kwerendy |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="table-access"></a>Dostępu do tabel

| **Property** | **Opis** |
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
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, lub `DELETE` |
| `connection_id` | Identyfikator unikatowy połączenia wygenerowane przez MariaDB |
| `db` | Nazwa bazy danych |
| `table` | Nazwa tabeli dostępne |
| `sql_text` | Tekst pełnej kwerendy |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować dzienniki inspekcji w witrynie Azure portal](howto-configure-audit-logs-portal.md)