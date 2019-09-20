---
title: Azure Database for MySQL procedury składowane
description: W tym artykule przedstawiono procedury składowane specyficzne dla Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: f01a0bf68e510133058dc0075f27cfcf6241c7a8
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156178"
---
# <a name="azure-database-for-mysql-stored-procedures"></a>Azure Database for MySQL procedury składowane

Procedury składowane są dostępne na serwerach Azure Database for MySQL, aby ułatwić zarządzanie serwerem MySQL. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MySQL uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MySQL.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między serwerem głównym a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*MySQL. AZ _replication _start*|ND|ND|Uruchamia replikację.|
|*MySQL. AZ _replication _stop*|ND|ND|Kończy replikację.|
|*mysql.az_replication _remove_master*|ND|ND|Usuwa relację replikacji między serwerem głównym a repliką.|
|*mysql.az_replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między wzorcem a repliką w Azure Database for MySQL, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MySQL zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|ND|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Równoważne polecenie. Nastąpi przerwanie połączenia skojarzonego z podaną processlist_id po zakończeniu instrukcji wykonywanych przez połączenie.|
|*MySQL. AZ _kill_query*|processlist_id|ND|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Równoważne polecenie. Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*MySQL. AZ _load_timezone*|ND|ND|Ładuje tabele strefy czasowej, `time_zone` aby zezwolić na ustawienie wartości nazwanych (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)