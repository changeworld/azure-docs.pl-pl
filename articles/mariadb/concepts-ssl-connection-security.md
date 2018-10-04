---
title: Połączenia SSL dla usługi Azure Database dla serwera MariaDB
description: Informacje dotyczące konfigurowania usługi Azure Database for MariaDB oraz skojarzonych aplikacji, aby prawidłowo używać połączeń SSL
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1d0b27a8fd7e3882a73624fa1b668ac602a85e6b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249506"
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
