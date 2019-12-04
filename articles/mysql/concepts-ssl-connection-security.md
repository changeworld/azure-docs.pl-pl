---
title: Połączenie SSL — Azure Database for MySQL
description: Informacje dotyczące konfigurowania Azure Database for MySQL i skojarzonych aplikacji w celu prawidłowego używania połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d677e7c80d98b15a638b00c5b8f4d390a492c7fa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770819"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Połączenie SSL w Azure Database for MySQL
Azure Database for MySQL obsługuje łączenie serwera bazy danych z aplikacjami klienckimi przy użyciu protokołu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych powinna być skonfigurowana w taki sposób, aby wymagała połączeń SSL podczas nawiązywania połączenia z usługą MySQL.  Zalecamy uniknięcie wyłączania opcji SSL, jeśli jest to możliwe. 

Podczas aprowizacji nowego serwera Azure Database for MySQL za pośrednictwem Azure Portal i interfejsu wiersza polecenia wymuszanie połączeń SSL jest domyślnie włączone. 

W Azure Portal przedstawiono parametry połączenia dla różnych języków programowania. Te parametry połączenia obejmują wymagania SSL wymagane do nawiązania połączenia z bazą danych. W Azure Portal wybierz serwer. W polu Nagłówek **ustawień** wybierz **Parametry połączenia**. Parametr SSL zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenie SSL podczas tworzenia aplikacji, zapoznaj się z [tematem Konfigurowanie protokołu SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Następne kroki
[Biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md)
