---
title: Format dziennika inspekcji
description: Dowiedz się, jak mają strukturę dzienników inspekcji bazy danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722088"
---
# <a name="sql-database-audit-log-format"></a>Format dziennika inspekcji bazy danych SQL

[Inspekcja usługi Azure SQL Database](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure lub wysyła je do usługi Event Hub lub usługi Log Analytics w celu przetwarzania i analizy podrzędnej.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

### <a name="blob-audit"></a>Audyt obiektów blob

Dzienniki inspekcji przechowywane w magazynie obiektów Blob są przechowywane w kontenerze o nazwie `sqldbauditlogs` na koncie usługi Azure Storage. Hierarchia katalogów w kontenerze jest `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`formularzem . Format nazwy pliku obiektu `<CreationTime>_<FileNumberInSession>.xel`Blob `CreationTime` jest , `hh_mm_ss_ms` gdzie `FileNumberInSession` jest w formacie UTC i jest uruchomiony indeks w przypadku dzienników sesji obejmuje wiele plików obiektów blob.

Na przykład dla `Database1` `Server1` bazy danych na następującej jest możliwe prawidłowe ścieżki:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Repliki tylko do odczytu](sql-database-read-scale-out.md) Dzienniki inspekcji są przechowywane w tym samym kontenerze. Hierarchia katalogów w kontenerze jest `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`formularzem . Nazwa pliku obiektu Blob ma ten sam format. Dzienniki inspekcji replik tylko do odczytu są przechowywane w tym samym kontenerze.


### <a name="event-hub"></a>Centrum zdarzeń

Zdarzenia inspekcji są zapisywane w obszarze nazw i centrum zdarzeń, które zostały zdefiniowane podczas inspekcji konfiguracji i są przechwytywane w treści zdarzeń [Apache Avro](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON z kodowaniem UTF-8. Aby odczytać dzienniki inspekcji, można użyć [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

### <a name="log-analytics"></a>Log Analytics

Zdarzenia inspekcji są zapisywane w obszarze roboczym usługi `AzureDiagnostics` Log Analytics zdefiniowanym podczas inspekcji konfiguracji, do tabeli z kategorią `SQLSecurityAuditEvents`. Aby uzyskać dodatkowe przydatne informacje na temat języka wyszukiwania i poleceń usługi Log Analytics, zobacz [Odwołanie do wyszukiwania usługi Log Analytics](../log-analytics/log-analytics-log-search.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Pola dziennika inspekcji

| Nazwa (obiekt blob) | Nazwa (Centra zdarzeń/analiza dzienników) | Opis | Typ obiektu blob | Typ usługi Centrum zdarzeń/analiza dzienników |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Identyfikator akcji | varchar(4) | ciąg |
| action_name | action_name_s | Nazwa akcji | Nie dotyczy | ciąg |
| additional_information | additional_information_s | Wszelkie dodatkowe informacje o zdarzeniu przechowywane jako XML | nvarchar(4000) | ciąg |
| affected_rows | affected_rows_d | Liczba wierszy, których dotyczy kwerenda | bigint | int |
| Nazwa_aplikacji | application_name_s| Nazwa aplikacji klienckiej | nvarchar(128) | ciąg |
| audit_schema_version | audit_schema_version_d | Zawsze 1 | int | int |
| class_type | class_type_s | Rodzaj jednostki podlegającej kontroli, która przeprowadza badanie | varchar[2] | ciąg |
| class_type_desc | class_type_description_s | Opis jednostki podlegającej kontroli, którą przeprowadza się audyt w dniu | Nie dotyczy | ciąg |
| client_ip | client_ip_s | Źródłowy adres IP aplikacji klienckiej | nvarchar(128) | ciąg |
| connection_id | Nie dotyczy | Identyfikator połączenia na serwerze | Identyfikator GUID | Nie dotyczy |
| data_sensitivity_information | data_sensitivity_information_s | Typy informacji i etykiety czułości zwracane przez kwerendę poddane inspekcji na podstawie kolumn sklasyfikowanych w bazie danych. Dowiedz się więcej o [odnajdywanie i klasyfikacji danych usługi Azure SQL Database](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | ciąg |
| Nazwa_bazy_danych | database_name_s | Kontekst bazy danych, w którym wystąpiła akcja | Sysname | ciąg |
| database_principal_id | database_principal_id_d | Identyfikator kontekstu użytkownika bazy danych, w których akcja jest wykonywana w | int | int |
| database_principal_name | database_principal_name_s | Nazwa kontekstu użytkownika bazy danych, w którym wykonywana jest akcja | Sysname | ciąg |
| duration_milliseconds | duration_milliseconds_d | Czas trwania wykonywania kwerendy w milisekundach | bigint | int |
| event_time | event_time_t | Data i godzina odtworzania działania podlegania inspekcji | datetime2 | datetime |
| Host_name | Nie dotyczy | Nazwa hosta klienta | ciąg | Nie dotyczy |
| is_column_permission | is_column_permission_s | Flaga wskazująca, czy jest to uprawnienie na poziomie kolumny. 1 = prawda, 0 = fałsz | bit | ciąg |
| Nie dotyczy | is_server_level_audit_s | Flaga wskazująca, czy ta inspekcja jest na poziomie serwera | Nie dotyczy | ciąg |
| object_ identyfikator | object_id_d | Identyfikator jednostki, na której nastąpił audyt. Obejmuje to : obiekty serwera, bazy danych, obiekty bazy danych i obiekty schematu. 0 jeśli jednostka jest serwerem lub jeśli audyt nie jest wykonywany na poziomie obiektu | int | int |
| Object_name | object_name_s | Nazwa jednostki, na której nastąpił audyt. Obejmuje to : obiekty serwera, bazy danych, obiekty bazy danych i obiekty schematu. 0 jeśli jednostka jest serwerem lub jeśli audyt nie jest wykonywany na poziomie obiektu | Sysname | ciąg |
| permission_bitmask | permission_bitmask_s | W stosownych przypadkach pokazuje uprawnienia, które zostały przyznane, odrzucone lub odwołane | varbinary(16) | ciąg |
| response_rows | response_rows_d | Liczba wierszy zwróconych w zestawie wyników | bigint | int |
| Schema_name | schema_name_s | Kontekst schematu, w którym wystąpiła akcja. NULL dla inspekcji występujących poza schematem | Sysname | ciąg |
| Nie dotyczy | securable_class_type_s | Zabezpieczany obiekt, który mapuje class_type poddawanych audytowi | Nie dotyczy | ciąg |
| sequence_group_id | sequence_group_id_g | Unikatowy identyfikator | varbinary | Identyfikator GUID |
| sequence_number | sequence_number_d | Śledzi sekwencję rekordów w ramach jednego rekordu inspekcji, który był zbyt duży, aby zmieścić się w buforze zapisu dla inspekcji | int | int |
| server_instance_name | server_instance_name_s | Nazwa wystąpienia serwera, w którym wystąpiła inspekcja | Sysname | ciąg |
| server_principal_id | server_principal_id_d | Identyfikator kontekstu logowania, w którym wykonywana jest akcja | int | int |
| server_principal_name | server_principal_name_s | Bieżące logowanie | Sysname | ciąg |
| server_principal_sid | server_principal_sid_s | Bieżący identyfikator SID logowania | varbinary | ciąg |
| Session_id | session_id_d | Identyfikator sesji, w której wystąpiło zdarzenie | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Podmiot zabezpieczeń serwera dla sesji | Sysname | ciąg |
| Instrukcja | statement_s | Instrukcja T-SQL, która została wykonana (jeśli istnieje) | nvarchar(4000) | ciąg |
| powodzenie | succeeded_s | Wskazuje, czy akcja wyzwalana zdarzenie powiodła się. W przypadku zdarzeń innych niż logowania i partii, to tylko raporty, czy sprawdzanie uprawnień powiodło się lub nie powiodło się, a nie operacji. 1 = sukces, 0 = niepowodzenie | bit | ciąg |
| target_database_principal_id | target_database_principal_id_d | Główny administrator bazy danych grant/DENY/REVOKE operacji jest wykonywana na. 0, jeśli nie ma zastosowania | int | int |
| target_database_principal_name | target_database_principal_name_s | Docelowy użytkownik akcji. NULL, jeśli nie ma zastosowania | ciąg | ciąg |
| target_server_principal_id | target_server_principal_id_d | Podmiot zabezpieczeń serwera, na którym wykonywana jest operacja GRANT/DENY/REVOKE. Zwraca 0, jeśli nie ma zastosowania | int | int |
| target_server_principal_name | target_server_principal_name_s | Docelowy login akcji. NULL, jeśli nie ma zastosowania | Sysname | ciąg |
| target_server_principal_sid | target_server_principal_sid_s | Identyfikator SID logowania docelowego. NULL, jeśli nie ma zastosowania | varbinary | ciąg |
| transaction_id | transaction_id_d | Tylko program SQL Server (począwszy od 2016) — 0 dla usługi Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Identyfikator zdarzenia zdefiniowany przez użytkownika przekazany jako argument do sp_audit_write. NULL dla zdarzeń systemowych (domyślnie) i niezerowych dla zdarzeń zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Informacje zdefiniowane przez użytkownika przekazywane jako argument do sp_audit_write. NULL dla zdarzeń systemowych (domyślnie) i niezerowych dla zdarzeń zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | ciąg |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [inspekcji bazy danych SQL platformy Azure](sql-database-auditing.md).
