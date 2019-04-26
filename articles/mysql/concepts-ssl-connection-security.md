---
title: Połączenia SSL dla usługi Azure Database for MySQL
description: Informacje dotyczące konfigurowania usługi Azure Database for MySQL i skojarzonych aplikacji, aby prawidłowo używać połączeń SSL
author: JasonMAnderson
ms.author: janders
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 129f90d495627edb25dfafdeb1b274aa2c4c71cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525813"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Łączności SSL w usłudze Azure Database for MySQL
Usługa Azure Database for MySQL obsługuje łączenie z serwerem bazy danych dla aplikacji klienckich przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych powinny być konfigurowane do Wymagaj połączeń SSL, podczas nawiązywania połączenia z bazą danych MySQL.  Firma Microsoft zaleca, aby uniknąć wyłączenia opcji SSL, jeśli to możliwe. 

Podczas aprowizowania nowego serwera Azure Database for MySQL za pośrednictwem witryny Azure portal i interfejs wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączona. 

Parametry połączenia dla różnych języków programowania są wyświetlane w witrynie Azure portal. Te parametry połączenia zawierają wymaganych parametrów protokołu SSL do łączenia z bazą danych. W witrynie Azure portal wybierz serwer. W obszarze **ustawienia** nagłówka, wybierz **parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i inne.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenia SSL, podczas tworzenia aplikacji, zapoznaj się [jak skonfigurować protokół SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Kolejne kroki
[Biblioteki połączeń dla usługi Azure Database for MySQL](concepts-connection-libraries.md)
