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
ms.openlocfilehash: dda5df58a83ddd3ce42fa887c3c32a3e23954920
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946650"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Łączności SSL w usłudze Azure Database dla serwera MariaDB
Azure Database dla serwera MariaDB obsługuje łączenie z serwerem bazy danych dla aplikacji klienckich przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych powinny być konfigurowane do Wymagaj połączeń SSL, podczas nawiązywania połączenia MariaDB.  Firma Microsoft zaleca, aby uniknąć wyłączenia opcji SSL, jeśli to możliwe.

Podczas aprowizacji nowej bazy danych Azure dla serwera MariaDB za pośrednictwem witryny Azure portal i interfejs wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączona.

Parametry połączenia dla różnych języków programowania są wyświetlane w witrynie Azure portal. Te parametry połączenia zawierają wymaganych parametrów protokołu SSL do łączenia z bazą danych. W witrynie Azure portal wybierz serwer. W obszarze **ustawienia** nagłówka, wybierz **parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i inne.

<!-- To learn how to enable or disable SSL connection when developing application, refer to [How to configure SSL](howto-configure-ssl.md).-->

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [reguły zapory serwera](concepts-firewall-rules.md)
- Dowiedz się, jak [Konfigurowanie certyfikatu SSL](howto-configure-ssl.md).
