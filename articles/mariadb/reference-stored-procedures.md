---
title: Procedury przechowywane do zarządzania — usługa Azure Database for MariaDB
description: Dowiedz się, które procedury przechowywane w usłudze Azure Database for MariaDB są przydatne do konfigurowania replikacji danych, ustawiania strefy czasowej i zabić kwerendy.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529994"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Procedury przechowywane w usłudze Azure Database for MariaDB

Procedury przechowywane są dostępne na serwerach usługi Azure Database dla mariadb, aby ułatwić zarządzanie serwerem MariaDB. Obejmuje to zarządzanie połączeniami serwera, zapytaniami i konfigurowanie replikacji danych.  

## <a name="data-in-replication-stored-procedures"></a>Procedury przechowywane replikacji danych

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB.

Poniższe procedury przechowywane są używane do konfigurowania lub usuwania replikacji danych między wzorcem a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Nie dotyczy|Aby przesłać dane w trybie SSL, przekaż w kontekście certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przenieść dane bez SSL, przekaż pusty ciąg do parametru master_ssl_ca.|
|*_start mysql.az_replication*|Nie dotyczy|Nie dotyczy|Rozpoczyna replikację.|
|*_stop mysql.az_replication*|Nie dotyczy|Nie dotyczy|Zatrzymuje replikację.|
|*_remove_master mysql.az_replication*|Nie dotyczy|Nie dotyczy|Usuwa relację replikacji między wzorcem a repliką.|
|*mysql.az_replication_skip_counter*|Nie dotyczy|Nie dotyczy|Pomija jeden błąd replikacji.|

Aby skonfigurować replikację między wzorcem a repliką w bazie danych Platformy Azure dla mariadb, zapoznaj się z [instrukcjami konfigurowania replikacji danych.](howto-data-in-replication.md)

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury przechowywane są dostępne w usłudze Azure Database for MariaDB do zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|Nie dotyczy|Odpowiednik [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenia. Zakończy połączenie skojarzone z podanym processlist_id po zakończeniu instrukcji, które wykonuje połączenie.|
|*mysql.az_kill_query*|processlist_id|Nie dotyczy|Odpowiednik [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenia. Spowoduje zakończenie instrukcji, które połączenie jest aktualnie wykonywane. Pozostawia samo połączenie przy życiu.|
|*mysql.az_load_timezone*|Nie dotyczy|Nie dotyczy|Ładuje tabele stref `time_zone` czasowych, aby umożliwić ustawienie parametru na nazwane wartości (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikację danych](howto-data-in-replication.md)
- Dowiedz się, jak korzystać z [tabel stref czasowych](howto-server-parameters.md#working-with-the-time-zone-parameter)