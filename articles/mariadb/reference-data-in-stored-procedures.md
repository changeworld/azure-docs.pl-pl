---
title: Azure Database for replikacji danych MariaDB procedur składowanych
description: W tym artykule przedstawiono wszystkie procedury składowane używane do replikacji danych.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125869"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Azure Database for replikacji danych MariaDB procedur składowanych

Dane replikacji umożliwia synchronizowanie danych z serwera MariaDB, działających w środowisku lokalnym, w maszynach wirtualnych lub bazy danych usług hostowanych przez innych dostawców chmury do usługi Azure Database dla usługi MariaDB.

Następujące procedury przechowywane są używane do skonfigurować lub usunąć replikacji danych między głównego i repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Informacje dotyczące użycia**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Transfer danych w trybie SSL, należy przekazać w kontekście certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Na przesyłanie danych bez zastosowania protokołu SSL, należy przekazać w postaci pustego ciągu do parametru master_ssl_ca.|
|*mysql.az_replication _start*|ND|ND|Uruchamia replikację.|
|*mysql.az_replication _stop*|ND|ND|Zatrzymanie replikacji.|
|*mysql.az_replication _remove_master*|ND|ND|Usunięcie relacji replikacji między głównej maszynie wirtualnej i repliki.|
|*mysql.az_replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować dane w replikacji między serwerem wzorca i repliki w usłudze Azure Database dla serwera MariaDB, zapoznaj się [sposobu konfigurowania replikacji danych](howto-data-in-replication.md).
