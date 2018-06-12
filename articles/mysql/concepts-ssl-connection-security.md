---
title: Połączenie SSL w bazie danych Azure dla programu MySQL
description: Informacje dotyczące konfiguracji bazy danych Azure MySQL i skojarzone aplikacje prawidłowe korzystanie z połączeń SSL
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee7e0ec8524d66ee89cf7b2c4d44b70efa784f8f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265065"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Połączenie SSL w bazie danych Azure dla programu MySQL
Bazy danych platformy Azure dla programu MySQL obsługuje łączenie serwer bazy danych dla aplikacji klienckich przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="default-settings"></a>Ustawienia domyślne
Domyślnie usługa bazy danych należy skonfigurować do Wymagaj połączeń SSL, podczas nawiązywania połączenia MySQL.  Firma Microsoft zaleca, aby uniknąć wyłączenie opcji SSL, jeśli to możliwe. 

Podczas inicjowania obsługi administracyjnej nowej bazy danych Azure MySQL serwerem za pośrednictwem portalu Azure i interfejsu wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączona. 

Parametry połączenia dla różnych języków programowania są wyświetlane w portalu Azure. Te parametry połączenia zawierają parametry SSL wymagane do połączenia z bazą danych. W portalu Azure wybierz serwer. W obszarze **ustawienia** nagłówek, wybierz **parametry połączenia**. Parametr SSL w zależności od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i innych zmian.

Aby dowiedzieć się, jak włączyć lub wyłączyć połączenia SSL, podczas tworzenia aplikacji, zapoznaj się [Konfigurowanie SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Następne kroki
[Biblioteki połączeń dla bazy danych Azure dla programu MySQL](concepts-connection-libraries.md)
