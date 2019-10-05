---
title: Procedury składowane zarządzania Azure Database for MariaDB
description: Dowiedz się, które procedury składowane w Azure Database for MySQL są przydatne, aby ułatwić Konfigurowanie replikacji danych, ustawianie strefy czasowej i zabicia zapytań.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 0a33edeac735502964427ddc3b05076fb9fac969
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973465"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Procedury składowane zarządzania Azure Database for MariaDB

Procedury składowane są dostępne na serwerach Azure Database for MariaDB, aby ułatwić zarządzanie serwerem MariaDB. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między serwerem głównym a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. AZ _replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*MySQL. AZ _replication _start*|ND|ND|Uruchamia replikację.|
|*MySQL. AZ _replication _stop*|ND|ND|Kończy replikację.|
|*MySQL. AZ _replication _remove_master*|ND|ND|Usuwa relację replikacji między serwerem głównym a repliką.|
|*MySQL. AZ _replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między wzorcem a repliką w Azure Database for MariaDB, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MariaDB zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|ND|Odpowiednik [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenia. Nastąpi przerwanie połączenia skojarzonego z podaną processlist_id po zakończeniu instrukcji wykonywanych przez połączenie.|
|*MySQL. AZ _kill_query*|processlist_id|ND|Odpowiednik [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenia. Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*MySQL. AZ _load_timezone*|ND|ND|Ładuje tabele strefy czasowej, aby zezwolić na ustawienie wartości parametrów `time_zone` (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)