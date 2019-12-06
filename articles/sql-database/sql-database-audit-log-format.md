---
title: Format dziennika inspekcji
description: Zapoznaj się ze strukturą dzienników inspekcji SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 3b7a3c295d2edd60c70f47ea155a5d747a3bfb03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873764"
---
# <a name="sql-database-audit-log-format"></a>SQL Database format dziennika inspekcji

[Inspekcja Azure SQL Database](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage lub wysyła do centrum zdarzeń lub log Analytics na potrzeby przetwarzania i analizy podrzędnej.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

### <a name="blob-audit"></a>Inspekcja obiektów BLOB

Dzienniki inspekcji przechowywane w magazynie obiektów BLOB są przechowywane w kontenerze o nazwie `sqldbauditlogs` na koncie usługi Azure Storage. Hierarchia katalogów w kontenerze ma postać `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Format nazwy pliku obiektu BLOB to `<CreationTime>_<FileNumberInSession>.xel`, gdzie `CreationTime` jest w formacie UTC `hh_mm_ss_ms`, a `FileNumberInSession` jest uruchomionym indeksem w przypadku dzienników sesji przypadku obejmujących wiele plików obiektów BLOB.

Na przykład dla `Database1` bazy danych na `Server1` poniżej jest możliwe prawidłowe ścieżki:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Repliki tylko do odczytu](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-read-scale-out) Dzienniki inspekcji są przechowywane w tym samym kontenerze. Hierarchia katalogów w kontenerze ma postać `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`. Nazwa pliku obiektu BLOB ma ten sam format. Dzienniki inspekcji replik tylko do odczytu są przechowywane w tym samym kontenerze.


### <a name="event-hub"></a>Centrum zdarzeń

Zdarzenia inspekcji są zapisywane w przestrzeni nazw i centrum zdarzeń, które zostało zdefiniowane podczas konfiguracji inspekcji i są przechwytywane w treści Avro zdarzeń platformy [Apache](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON przy użyciu kodowania UTF-8. Aby odczytać dzienniki inspekcji, można użyć [narzędzi Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

### <a name="log-analytics"></a>Log Analytics

Zdarzenia inspekcji są zapisywane w obszarze roboczym Log Analytics zdefiniowanym podczas konfigurowania inspekcji do tabeli `AzureDiagnostics` z kategorią `SQLSecurityAuditEvents`. Aby uzyskać dodatkowe przydatne informacje na temat języka i poleceń wyszukiwania Log Analytics, zobacz [log Analytics informacje o wyszukiwaniu](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Pola dziennika inspekcji

| Nazwa (BLOB) | Nazwa (Event Hubs/Log Analytics) | Opis | Typ obiektu blob | Typ Event Hubs/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Identyfikator akcji | varchar (4) | string |
| action_name | action_name_s | Nazwa akcji | ND | string |
| additional_information | additional_information_s | Wszelkie dodatkowe informacje o zdarzeniu, przechowywane jako XML | nvarchar (4000) | string |
| affected_rows | affected_rows_d | Liczba wierszy, na które ma wpływ zapytanie | bigint | int |
| application_name | application_name_s| Nazwa aplikacji klienckiej | nvarchar (128) | string |
| audit_schema_version | audit_schema_version_d | Zawsze 1 | int | int |
| class_type | class_type_s | Typ jednostki objętej inspekcją, w której występuje Inspekcja | varchar (2) | string |
| class_type_desc | class_type_description_s | Opis jednostki objętej inspekcją, w której występuje Inspekcja | ND | string |
| client_ip | client_ip_s | Źródłowy adres IP aplikacji klienckiej | nvarchar (128) | string |
| connection_id | ND | Identyfikator połączenia na serwerze | GUID | ND |
| data_sensitivity_information | data_sensitivity_information_s | Typy informacji i etykiety czułości zwracane przez zapytania poddane inspekcji na podstawie sklasyfikowanych kolumn w bazie danych. Dowiedz się więcej na temat [odnajdywania i klasyfikowania danych Azure SQL Database](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | string |
| database_name | database_name_s | Kontekst bazy danych, w którym wystąpiła akcja | bazy | string |
| database_principal_id | database_principal_id_d | Identyfikator kontekstu użytkownika bazy danych, w którym wykonywana jest akcja | int | int |
| database_principal_name | database_principal_name_s | Nazwa kontekstu użytkownika bazy danych, w którym wykonywana jest akcja | bazy | string |
| duration_milliseconds | duration_milliseconds_d | Czas trwania wykonywania zapytania w milisekundach | bigint | int |
| event_time | event_time_t | Data i godzina uruchomienia akcji objętej inspekcją | datetime2 | datetime |
| host_name | ND | Nazwa hosta klienta | string | ND |
| is_column_permission | is_column_permission_s | Flaga oznaczająca, czy jest to uprawnienie na poziomie kolumny. 1 = true, 0 = FAŁSZ | bit | string |
| ND | is_server_level_audit_s | Flaga oznaczająca, czy ta inspekcja jest na poziomie serwera | ND | string |
| Identyfikator object_ | object_id_d | Identyfikator jednostki, w której wystąpiła Inspekcja. Obejmuje to: obiekty serwera, bazy danych, obiekty bazy danych i obiekty schematu. 0, jeśli jednostka jest serwerem lub jeśli Inspekcja nie jest przeprowadzana na poziomie obiektu | int | int |
| object_name | object_name_s | Nazwa jednostki, w której wystąpiła Inspekcja. Obejmuje to: obiekty serwera, bazy danych, obiekty bazy danych i obiekty schematu. 0, jeśli jednostka jest serwerem lub jeśli Inspekcja nie jest przeprowadzana na poziomie obiektu | bazy | string |
| permission_bitmask | permission_bitmask_s | Jeśli ma to zastosowanie, pokazuje uprawnienia, które zostały przyznane, odrzucone lub odwołane | varbinary (16) | string |
| response_rows | response_rows_d | Liczba wierszy zwróconych w zestawie wyników | bigint | int |
| schema_name | schema_name_s | Kontekst schematu, w którym wystąpiła akcja. Wartość zerowa dla inspekcji występujących poza schematem | bazy | string |
| ND | securable_class_type_s | Zabezpieczany obiekt, który jest mapowany na class_type poddawane inspekcji | ND | string |
| sequence_group_id | sequence_group_id_g | Unikatowy identyfikator | varbinary | GUID |
| sequence_number | sequence_number_d | Śledzi sekwencję rekordów w jednym rekordzie inspekcji, który był zbyt duży, aby zmieścił się w buforze zapisu dla inspekcji | int | int |
| server_instance_name | server_instance_name_s | Nazwa wystąpienia serwera, na którym wystąpiła Inspekcja | bazy | string |
| server_principal_id | server_principal_id_d | Identyfikator kontekstu logowania, w którym jest wykonywana akcja | int | int |
| server_principal_name | server_principal_name_s | Bieżące logowanie | bazy | string |
| server_principal_sid | server_principal_sid_s | Bieżący identyfikator SID logowania | varbinary | string |
| session_id | session_id_d | Identyfikator sesji, w której wystąpiło zdarzenie | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Podmiot zabezpieczeń serwera dla sesji | bazy | string |
| instrukcja | statement_s | Instrukcja T-SQL, która została wykonana (jeśli istnieje) | nvarchar (4000) | string |
| Sukces | succeeded_s | Wskazuje, czy akcja, która wywołała zdarzenie, zakończyła się pomyślnie. W przypadku zdarzeń innych niż logowanie i Partia zadań ten raport dotyczy tylko tego, czy sprawdzenie uprawnień zakończyło się powodzeniem czy niepowodzeniem, a nie operacji. 1 = sukces, 0 = niepowodzenie | bit | string |
| target_database_principal_id | target_database_principal_id_d | Podmiot zabezpieczeń bazy danych jest operacją GRANT/DENY/REVOKE. 0, jeśli nie dotyczy | int | int |
| target_database_principal_name | target_database_principal_name_s | Użytkownik docelowy akcji. Wartość NULL, jeśli nie dotyczy | string | string |
| target_server_principal_id | target_server_principal_id_d | Podmiot zabezpieczeń serwera, na którym jest wykonywana operacja GRANT/DENY/REVOKE. Zwraca wartość 0, jeśli nie dotyczy | int | int |
| target_server_principal_name | target_server_principal_name_s | Docelowa nazwa logowania akcji. Wartość NULL, jeśli nie dotyczy | bazy | string |
| target_server_principal_sid | target_server_principal_sid_s | Identyfikator SID docelowej nazwy logowania. Wartość NULL, jeśli nie dotyczy | varbinary | string |
| transaction_id | transaction_id_d | Tylko SQL Server (począwszy od 2016)-0 dla bazy danych SQL Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Identyfikator zdarzenia zdefiniowany przez użytkownika przeszedł jako argument do sp_audit_write. Wartość zerowa dla zdarzeń systemowych (wartość domyślna) i wartość niezerowa dla zdarzenia zdefiniowanego przez użytkownika. Aby uzyskać więcej informacji, zobacz [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Informacje zdefiniowane przez użytkownika przekazały jako argument do sp_audit_write. Wartość zerowa dla zdarzeń systemowych (wartość domyślna) i wartość niezerowa dla zdarzenia zdefiniowanego przez użytkownika. Aby uzyskać więcej informacji, zobacz [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | string |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [inspekcji Azure SQL Database](sql-database-auditing.md).
