---
title: Procedury przechowywane bazy danych platformy Azure dla danych MySQL w replikacji
description: W tym artykule przedstawiono wszystkie procedury składowane używanych w przypadku danych w replikacji.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 8683aacb2ae41cb4c57aa70b375eca9db6106492
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655666"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Procedury przechowywane bazy danych platformy Azure dla danych MySQL w replikacji

W danych replikacji umożliwia synchronizowanie danych z MySQL serwerem lokalnym, na maszynach wirtualnych lub bazy danych usług hostowanych przez innych dostawców chmury do bazy danych Azure dla usługi MySQL.

Następujące procedury składowane są używane do konfigurowania lub usuwania danych w replikacji między podstawowym i repliki.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Dotyczące użycia**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|ND|Do transferu danych w trybie protokołu SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Do transferu danych bez zastosowania protokołu SSL, przekazać pusty ciąg do parametru master_ssl_ca.|
|*_uruchom MySQL.az_replication*|ND|ND|Uruchamia replikację.|
|*MySQL.az_replication _stop*|ND|ND|Zatrzymuje replikacji.|
|*MySQL.az_replication _remove_primary*|ND|ND|Spowoduje usunięcie relacji replikacji między serwerem podstawowym i repliki.|
|*MySQL.az_replication_skip_counter*|ND|ND|Pomija jeden błąd replikacji.|

Aby skonfigurować dane w replikacji między podstawowym i repliki w bazie danych Azure dla programu MySQL, zapoznaj się [Konfigurowanie replikacji danych w](howto-data-in-replication.md).