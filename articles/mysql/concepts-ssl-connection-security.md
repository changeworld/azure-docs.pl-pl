---
title: Łączność SSL — usługa Azure Database dla mysql
description: Informacje dotyczące konfigurowania usługi Azure Database dla mysql i skojarzonych aplikacji do prawidłowego korzystania z połączeń SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6a12ef851823ab5eff2b11905d05be1950c82ef0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474275"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Łączność SSL w usłudze Azure Database dla mysql

Usługa Azure Database for MySQL obsługuje łączenie serwera bazy danych z aplikacjami klienckimi przy użyciu warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="ssl-default-settings"></a>Ustawienia domyślne SSL

Domyślnie usługa bazy danych powinna być skonfigurowana tak, aby wymagała połączeń SSL podczas łączenia się z MySQL.  Zalecamy, aby uniknąć wyłączenia opcji SSL, gdy tylko jest to możliwe.

Podczas inicjowania obsługi administracyjnej nowej bazy danych platformy Azure dla serwera MySQL za pośrednictwem portalu Azure i interfejsu wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączone. 

Parametry połączenia dla różnych języków programowania są wyświetlane w witrynie Azure portal. Te parametry połączenia zawierają wymagane parametry SSL do łączenia się z bazą danych. W witrynie Azure portal wybierz serwer. W obszarze pozycji **Ustawienia** wybierz **pozycję Parametry połączenia**. Parametr SSL różni się w zależności od złącza, na przykład "ssl=true" lub "sslmode=require" lub "sslmode=required" i innych odmian.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenie SSL podczas tworzenia aplikacji, zobacz [Jak skonfigurować SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Następne kroki

[Biblioteki połączeń dla usługi Azure Database dla mysql](concepts-connection-libraries.md)
