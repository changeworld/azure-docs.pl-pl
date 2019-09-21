---
title: Azure Database for MariaDB procedury składowane
description: W tym artykule przedstawiono procedury składowane specyficzne dla Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: d9daaf619a19c0f4e4a591d4bbb4925679fd1fcb
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174905"
---
# <a name="azure-database-for-mariadb-stored-procedures"></a>Azure Database for MariaDB procedury składowane

Procedury składowane są dostępne na serwerach Azure Database for MariaDB, aby ułatwić zarządzanie serwerem MariaDB. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między serwerem głównym a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*MySQL. AZ _replication _start*|ND|ND|Uruchamia replikację.|
|*MySQL. AZ _replication _stop*|ND|ND|Kończy replikację.|
|*mysql.az_replication _remove_master*|ND|ND|Usuwa relację replikacji między serwerem głównym a repliką.|
|*mysql.az_replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między wzorcem a repliką w Azure Database for MariaDB, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MariaDB zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|ND|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Równoważne polecenie. Nastąpi przerwanie połączenia skojarzonego z podaną processlist_id po zakończeniu instrukcji wykonywanych przez połączenie.|
|*MySQL. AZ _kill_query*|processlist_id|ND|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Równoważne polecenie. Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*MySQL. AZ _load_timezone*|ND|ND|Ładuje tabele strefy czasowej, `time_zone` aby zezwolić na ustawienie wartości nazwanych (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)