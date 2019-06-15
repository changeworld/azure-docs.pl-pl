---
title: Połączenia SSL dla usługi Azure Database dla serwera MariaDB
description: Informacje dotyczące konfigurowania usługi Azure Database for MariaDB oraz skojarzonych aplikacji, aby prawidłowo używać połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 15bb28846b3409dd31bcdf8d42990facc94fd06d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332690"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Łączności SSL w usłudze Azure Database dla serwera MariaDB
Azure Database dla serwera MariaDB obsługuje łączenie z serwerem bazy danych dla aplikacji klienckich przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych powinny być konfigurowane do Wymagaj połączeń SSL, podczas nawiązywania połączenia MariaDB.  Firma Microsoft zaleca, aby uniknąć wyłączenia opcji SSL, jeśli to możliwe.

Podczas aprowizacji nowej bazy danych Azure dla serwera MariaDB za pośrednictwem witryny Azure portal i interfejs wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączona.

Parametry połączenia dla różnych języków programowania są wyświetlane w witrynie Azure portal. Te parametry połączenia zawierają wymaganych parametrów protokołu SSL do łączenia z bazą danych. W witrynie Azure portal wybierz serwer. W obszarze **ustawienia** nagłówka, wybierz **parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i inne.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenia SSL, podczas tworzenia aplikacji, zapoznaj się [jak skonfigurować protokół SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [reguły zapory serwera](concepts-firewall-rules.md)
- Dowiedz się, jak [Konfigurowanie certyfikatu SSL](howto-configure-ssl.md).
