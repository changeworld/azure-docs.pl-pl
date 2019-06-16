---
title: Format dziennika inspekcji bazy danych SQL | Dokumentacja firmy Microsoft
description: Informacje o strukturze dzienniki inspekcji bazy danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61417392"
---
# <a name="sql-database-audit-log-format"></a>Format dziennika inspekcji bazy danych SQL

[Funkcja inspekcji usługi Azure SQL Database](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w inspekcji dzienniku na Twoim koncie usługi Azure storage lub wysyła je do Centrum zdarzeń lub usługi Log Analytics do przetwarzania transmisji dla klientów i analizy.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

### <a name="blob-audit"></a>Inspekcja obiektów blob

Dzienniki inspekcji, przechowywane w magazynie obiektów Blob są przechowywane w kontenerze o nazwie `sqldbauditlogs` w ramach konta usługi Azure Storage. Hierarchii katalogów w kontenerze ma postać `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Format nazwy pliku obiektu Blob jest `<CreationTime>_<FileNumberInSession>.xel`, gdzie `CreationTime` jest w formacie UTC `hh_mm_ss_ms` formacie, a `FileNumberInSession` jest uruchomione indeksu w przypadku, gdy w sesji logowania zakresy na wiele plików obiektów Blob.

Na przykład dla bazy danych `Database1` na `Server1` poniżej przedstawiono możliwe prawidłowej ścieżki:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Centrum zdarzeń

Zdarzenia inspekcji są zapisywane w przestrzeni nazw i Centrum zdarzeń, która została zdefiniowana podczas konfigurowania inspekcji, są przechwytywane w treści [Apache Avro](https://avro.apache.org/) zdarzeń i zmagazynowane przy użyciu formatu JSON formatowanie przy użyciu kodowania UTF-8. Aby odczytać dzienniki inspekcji, możesz użyć [narzędzia Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) lub podobne narzędzia, które przetwarzają tego formatu.

### <a name="log-analytics"></a>Log Analytics

Zdarzenia inspekcji są zapisywane do obszaru roboczego usługi Log Analytics zdefiniowane podczas konfigurowania inspekcji do `AzureDiagnostics` tabeli z kategorią `SQLSecurityAuditEvents`. Aby uzyskać dodatkowe przydatne informacje dotyczące języka wyszukiwania usługi Log Analytics i poleceń, zobacz [usługi Log Analytics Wyszukaj odwołanie](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Pola dziennika inspekcji

| Nazwy (obiektów Blob) | Nazwa (Event Hubs/Log Analytics) | Opis | Typ obiektu blob | Typ Analytics koncentratory/dziennika zdarzeń |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Identyfikator działania | varchar(4) | string |
| action_name | action_name_s | Nazwa akcji | ND | string |
| additional_information | additional_information_s | Wszelkie dodatkowe informacje na temat zdarzeń, przechowywane w formacie XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Liczba wierszy dotyczy zapytanie | bigint | int |
| nazwa_aplikacji | application_name_s| Nazwa aplikacji klienta | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Zawsze 1 | int | int |
| class_type | class_type_s | Typ jednostki inspekcji, wykonywanego w wybranym inspekcji | varchar(2) | string |
| class_type_desc | class_type_description_s | Opis jednostki inspekcji, wykonywanego w wybranym inspekcji | ND | string |
| client_ip | client_ip_s | Źródłowy adres IP aplikacji klienta | nvarchar(128) | string |
| connection_id | ND | Identyfikator połączenia na serwerze | Identyfikator GUID | ND |
| data_sensitivity_information | data_sensitivity_information_s | Typy informacji i etykiety ważności zwróconych przez zapytanie inspekcji, na podstawie sklasyfikowanych kolumn w bazie danych. Dowiedz się więcej o [usługi Azure SQL Database, danych, odnajdowanie i klasyfikacja](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | Kontekst bazy danych, w którym wystąpiła Akcja | sysname | string |
| database_principal_id | database_principal_id_d | Identyfikator kontekstu użytkownika bazy danych, która akcja jest wykonywana w | int | int |
| database_principal_name | database_principal_name_s | Nazwa kontekstu użytkownika bazy danych, wykonywania akcji | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Czas trwania wykonywania kwerendy w milisekundach | bigint | int |
| event_time | event_time_t | Data i godzina, po wyzwoleniu działania inspekcji | datetime2 | datetime |
| host_name | ND | Nazwa hosta klienta | string | ND |
| is_column_permission | is_column_permission_s | Flaga wskazująca, czy jest to uprawnienie poziomu kolumna. 1 = true, 0 = false | bit | string |
| ND | is_server_level_audit_s | Flaga wskazująca, czy ten inspekcji jest na poziomie serwera | ND | string |
| Identyfikator object_ — element | object_id_d | Identyfikator jednostki, w którym wystąpiło inspekcji. Obejmuje to: obiekty serwera, bazy danych, obiekty bazy danych i obiektów schematu. 0, jeśli jednostka jest serwerem lub jeśli inspekcji nie jest wykonywane na poziomie obiektu | int | int |
| object_name | object_name_s | Nazwa jednostki, w którym wystąpiło inspekcji. Obejmuje to: obiekty serwera, bazy danych, obiekty bazy danych i obiektów schematu. 0, jeśli jednostka jest serwerem lub jeśli inspekcji nie jest wykonywane na poziomie obiektu | sysname | string |
| permission_bitmask | permission_bitmask_s | Jeśli ma to zastosowanie, przedstawiono uprawnienia, które zostały przyznane, odmowa lub odwołane | varbinary(16) | string |
| response_rows | response_rows_d | Liczba wierszy zwracanych w zestawie wyników | bigint | int |
| schema_name | schema_name_s | Kontekst schematu, w którym wystąpiła Akcja. Wartość NULL w przypadku inspekcji pojawiają się poza schematu | sysname | string |
| ND | securable_class_type_s | Zabezpieczanego obiektu, który jest mapowany do class_type poddawanych inspekcji | ND | string |
| sequence_group_id | sequence_group_id_g | Unikatowy identyfikator | varbinary | Identyfikator GUID |
| sequence_number | sequence_number_d | Śledzi sekwencji rekordy w ramach rekord inspekcji jednego, który był zbyt duży, aby zmieścić ją w buforu zapisu dla inspekcji | int | int |
| server_instance_name | server_instance_name_s | Nazwa wystąpienia serwera, w którym wystąpił inspekcji | sysname | string |
| server_principal_id | server_principal_id_d | Identyfikator kontekstu logowania, w którym jest wykonywana akcja | int | int |
| server_principal_name | server_principal_name_s | Bieżąca nazwa logowania | sysname | string |
| server_principal_sid | server_principal_sid_s | Bieżąca nazwa logowania identyfikatora SID | varbinary | string |
| session_id | session_id_d | Identyfikator sesji, w którym wystąpiło zdarzenie | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Podmiot zabezpieczeń serwera dla sesji | sysname | string |
| Instrukcja | statement_s | Instrukcja języka T-SQL, który został wykonany (jeśli istnieje) | nvarchar(4000) | string |
| Powiodło się. | succeeded_s | Wskazuje, czy akcja, która wyzwoliła zdarzenie zakończyło się pomyślnie. Dla zdarzenia inne niż nazwa logowania i przetwarzania wsadowego to tylko raporty czy sprawdzenie uprawnień zakończonych powodzeniem lub niepowodzeniem, nie wykonać operację. 1 = Powodzenie. 0 = niepowodzenie | bit | string |
| target_database_principal_id | target_database_principal_id_d | Podmiot zabezpieczeń bazy danych operacji GRANT/DENY/REVOKE jest wykonywana na. 0, jeśli nie dotyczy | int | int |
| target_database_principal_name | target_database_principal_name_s | Użytkownik docelowy akcji. Wartość NULL, jeśli nie dotyczy | string | string |
| target_server_principal_id | target_server_principal_id_d | Podmiot zabezpieczeń serwera, który GRANT/DENY/REVOKE jest przeprowadzane na. Zwraca wartość 0, jeśli nie dotyczy | int | int |
| target_server_principal_name | target_server_principal_name_s | Logowanie w docelowym akcji. Wartość NULL, jeśli nie dotyczy | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | Identyfikator SID logowania docelowego. Wartość NULL, jeśli nie dotyczy | varbinary | string |
| transaction_id | transaction_id_d | Tylko programu SQL Server (począwszy od 2016) - 0 dla bazy danych SQL Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Identyfikator zdarzenia przekazywany jako argument do sp_audit_write zdefiniowane przez użytkownika. Wartość NULL w przypadku zdarzenia systemowe (ustawienie domyślne) i różna od zera dla zdarzeń zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Informacje przekazywane jako argument do sp_audit_write zdefiniowane przez użytkownika. Wartość NULL w przypadku zdarzenia systemowe (ustawienie domyślne) i różna od zera dla zdarzeń zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [inspekcji usługi Azure SQL Database](sql-database-auditing.md).