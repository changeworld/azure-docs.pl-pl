---
title: Procedury składowane zarządzania — Azure Database for MySQL
description: Dowiedz się, które procedury składowane w Azure Database for MySQL są przydatne, aby ułatwić Konfigurowanie replikacji danych, ustawianie strefy czasowej i zabicia zapytań.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7ab77f822ace61ccb023dffe6d79fb1d08278d11
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774944"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Procedury składowane zarządzania Azure Database for MySQL

Procedury składowane są dostępne na serwerach Azure Database for MySQL, aby ułatwić zarządzanie serwerem MySQL. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MySQL uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MySQL.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między serwerem głównym a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*Baza danych MySQL. az_replication _start*|ND|ND|Uruchamia replikację.|
|*Baza danych MySQL. az_replication _stop*|ND|ND|Kończy replikację.|
|*Baza danych MySQL. az_replication _remove_master*|ND|ND|Usuwa relację replikacji między serwerem głównym a repliką.|
|*MySQL. az_replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między wzorcem a repliką w Azure Database for MySQL, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MySQL zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|ND|Odpowiednik polecenia [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Nastąpi przerwanie połączenia skojarzonego z podanym processlist_id po zakończeniu wszelkich instrukcji wykonywanych przez połączenie.|
|*MySQL. az_kill_query*|processlist_id|ND|Odpowiednik polecenia [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*MySQL. az_load_timezone*|ND|ND|Ładuje tabele strefy czasowej, aby zezwolić, aby parametr `time_zone` miał wartość nazwanych wartości (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)
