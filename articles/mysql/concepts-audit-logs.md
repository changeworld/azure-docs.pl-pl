---
title: Dzienniki inspekcji dla usługi Azure Database for MySQL
description: W tym artykule opisano dzienników inspekcji dostępnych w usłudze Azure Database for MySQL i dostępne parametry umożliwiające poziomów rejestrowania.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 35cbe04380e2113f986d1e7adf3f7fdf89cb9326
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078874"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Dzienniki inspekcji w usłudze Azure Database for MySQL

W usłudze Azure Database for MySQL z dziennika inspekcji jest dostępna dla użytkowników. Dziennik inspekcji może służyć do śledzenia działań na poziomie bazy danych i jest często używany pod kątem zgodności.

> [!IMPORTANT]
> Funkcja dziennika inspekcji jest obecnie w wersji zapoznawczej.

## <a name="configure-audit-logging"></a>Skonfiguruj rejestrowanie inspekcji

Domyślnie dziennik inspekcji jest wyłączona. Aby ją włączyć, ustaw `audit_log_enabled` na wartość ON.

Inne parametry, które można dostosować obejmują:

- `audit_log_events`: Określa zdarzenia, które mają być rejestrowane. Zobacz w poniższej tabeli dla określonych zdarzeń inspekcji.
- `audit_log_exclude_users`: Użytkownicy programu MySQL do wykluczenia z rejestrowania. Zezwala na maksymalnie czterech użytkowników. Maksymalna długość parametru wynosi 256 znaków.

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
| `TABLE_ACCESS` | — Dostępne tylko w przypadku MySQL 5.7 <br> -Table przeczytaj instrukcje, na przykład wybierz pozycję "lub" INSERT INTO... SELECT <br> -Table delete instrukcje, na przykład DELETE lub TRUNCATE TABLE <br> -Instrukcji insert table, takich jak wstawianie lub ZASTĘPOWANIE <br> -Instrukcje update table, na przykład aktualizacji |

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Dzienniki inspekcji są zintegrowane z dzienników diagnostycznych usługi Azure Monitor. Po włączeniu dzienniki inspekcji na Twoim serwerze MySQL, może emitować je do dzienników usługi Azure Monitor, usługa Event Hubs lub usługi Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych w witrynie Azure portal, zobacz [artykułu portalu dziennika inspekcji](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="schemas"></a>Schematy

W poniższych sekcjach opisano, co to jest danymi wyjściowymi MySQL dzienniki inspekcji na podstawie typu zdarzenia. W zależności od danych wyjściowych metody, pola, znajdujące się i kolejność, w jakiej są wyświetlane mogą się różnić.

### <a name="connection"></a>Połączenie

| **Property** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (Ta funkcja jest dostępna tylko dla MySQL 5.7) |
| `connection_id` | Identyfikator unikatowy połączenia wygenerowane przez MySQL |
| `host` | Puste |
| `ip` | Adres IP klienta łączenie z bazą danych MySQL |
| `user` | Nazwa użytkownika wykonującego zapytanie |
| `db` | Nazwa bazy danych połączone |
| `\_ResourceId` | Identyfikator URI zasobu |

### <a name="general"></a>Ogólne

Schemat poniżej dotyczy ogólne, DML_SELECT, DML_NONSELECT, DML, DDL, DCL i administratora typów zdarzeń.

| **Property** | **Opis** |
|---|---|
| `TenantId` | Identyfikator dzierżawy |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| `Type` | Typ dziennika. zawsze `AzureDiagnostics` |
| `SubscriptionId` | Identyfikator GUID dla subskrypcji, do której należy serwer |
| `ResourceGroup` | Nazwa grupy zasobów, do której należy serwer |
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` (Ta funkcja jest dostępna tylko dla MySQL 5.6) |
| `event_time` | Zapytanie start sekund w sygnatura czasowa systemu UNIX |
| `error_code` | Kod błędu, jeśli zapytanie nie powiodło się. `0` oznacza, że błąd nie |
| `thread_id` | Identyfikator wątku, który jest wykonywane zapytanie |
| `host` | Puste |
| `ip` | Adres IP klienta łączenie z bazą danych MySQL |
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
| `ResourceProvider` | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identyfikator URI zasobu |
| `Resource` | Nazwa serwera |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, lub `DELETE` |
| `connection_id` | Identyfikator unikatowy połączenia wygenerowane przez MySQL |
| `db` | Nazwa bazy danych |
| `table` | Nazwa tabeli dostępne |
| `sql_text` | Tekst pełnej kwerendy |
| `\_ResourceId` | Identyfikator URI zasobu |

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować dzienniki inspekcji w witrynie Azure portal](howto-configure-audit-logs-portal.md)