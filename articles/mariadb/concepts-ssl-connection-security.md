---
title: Łączność SSL — usługa Azure Database dla mariadb
description: Informacje dotyczące konfigurowania usługi Azure Database dla mariadb i skojarzonych aplikacji do prawidłowego korzystania z połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477072"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Łączność SSL w usłudze Azure Database dla MariaDB
Usługa Azure Database for MariaDB obsługuje łączenie serwera bazy danych z aplikacjami klienckimi przy użyciu warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych powinna być skonfigurowana tak, aby wymagała połączeń SSL podczas łączenia się z MariaDB.  Zalecamy, aby uniknąć wyłączenia opcji SSL, gdy tylko jest to możliwe.

Podczas inicjowania obsługi administracyjnej nowej bazy danych platformy Azure dla serwera MariaDB za pośrednictwem portalu Azure i interfejsu wiersza polecenia wymuszanie połączeń SSL jest domyślnie włączone.

Parametry połączenia dla różnych języków programowania są wyświetlane w witrynie Azure portal. Te parametry połączenia zawierają wymagane parametry SSL do łączenia się z bazą danych. W witrynie Azure portal wybierz serwer. W obszarze pozycji **Ustawienia** wybierz **pozycję Parametry połączenia**. Parametr SSL różni się w zależności od złącza, na przykład "ssl=true" lub "sslmode=require" lub "sslmode=required" i innych odmian.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenie SSL podczas tworzenia aplikacji, zobacz [Jak skonfigurować SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [regułach zapory serwera](concepts-firewall-rules.md)
- Dowiedz się, jak [skonfigurować ssl](howto-configure-ssl.md).
