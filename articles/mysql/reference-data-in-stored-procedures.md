---
title: Azure Database for MySQL — dane replikacji procedur składowanych
description: W tym artykule przedstawiono wszystkie procedury składowane używane do replikacji danych.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665950"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL — dane replikacji procedur składowanych

Replikacji danych umożliwia synchronizowanie danych z serwera MySQL, działające w środowisku lokalnym, w maszyny wirtualne lub usługi bazy danych obsługiwanych przez innych dostawców chmury do usługi Azure Database for MySQL usługi.

Następujące procedury przechowywane są używane do skonfigurować lub usunąć replikacji danych między głównego i repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Informacje dotyczące użycia**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Transfer danych w trybie SSL, należy przekazać w kontekście certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Na przesyłanie danych bez zastosowania protokołu SSL, należy przekazać w postaci pustego ciągu do parametru master_ssl_ca.|
|*_uruchom MySQL.az_replication*|ND|ND|Uruchamia replikację.|
|*_zatrzymaj MySQL.az_replication*|ND|ND|Zatrzymanie replikacji.|
|*MySQL.az_replication _remove_master*|ND|ND|Usunięcie relacji replikacji między głównej maszynie wirtualnej i repliki.|
|*MySQL.az_replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować dane w replikacji między serwerem wzorca i repliki w usłudze Azure Database for MySQL, zapoznaj się [sposobu konfigurowania replikacji danych](howto-data-in-replication.md).
