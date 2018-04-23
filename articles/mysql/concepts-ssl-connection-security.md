---
title: Połączenie SSL w bazie danych Azure dla programu MySQL
description: Informacje dotyczące konfiguracji bazy danych Azure MySQL i skojarzone aplikacje prawidłowe korzystanie z połączeń SSL
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f59d5eab9772515a3c59f887a48d597d27bab135
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Połączenie SSL w bazie danych Azure dla programu MySQL
Bazy danych platformy Azure dla programu MySQL obsługuje łączenie serwer bazy danych dla aplikacji klienckich przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych należy skonfigurować do Wymagaj połączeń SSL, podczas nawiązywania połączenia MySQL.  Firma Microsoft zaleca, aby uniknąć wyłączenie opcji SSL, jeśli to możliwe. 

Podczas inicjowania obsługi administracyjnej nowej bazy danych Azure MySQL serwerem za pośrednictwem portalu Azure i interfejsu wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączona. 

Parametry połączenia dla różnych języków programowania są wyświetlane w portalu Azure. Te parametry połączenia zawierają parametry SSL wymagane do połączenia z bazą danych. W portalu Azure wybierz serwer. W obszarze **ustawienia** nagłówek, wybierz **parametry połączenia**. Parametr SSL w zależności od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i innych zmian.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenia SSL, podczas tworzenia aplikacji, zapoznaj się [Konfigurowanie SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Kolejne kroki
[Biblioteki połączeń dla bazy danych Azure dla programu MySQL](concepts-connection-libraries.md)
