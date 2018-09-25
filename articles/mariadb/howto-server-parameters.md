---
title: Jak konfigurować parametry serwera w bazie danych platformy Azure dla serwera MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów serwera MariaDB w usłudze Azure Database dla serwera MariaDB przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c992783db3b63c73feb18c8c493ca1b43f9ea370
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975211"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Jak konfigurować parametry serwera w usłudze Azure Database dla serwera MariaDB przy użyciu witryny Azure portal

Azure Database dla serwera MariaDB obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu witryny Azure portal. Nie wszystkie parametry serwera można dostosować.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera w witrynie Azure portal

1. Zaloguj się do witryny Azure portal, a następnie zlokalizuj usługi Azure Database dla serwera MariaDB.
2. W obszarze **ustawienia** kliknij **parametrów serwera** aby otworzyć stronę parametrów serwera usługi Azure Database dla serwera MariaDB.
![Strona parametrów serwera portalu Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dopasować. Przegląd **opis** kolumny zrozumienie przeznaczenia i dozwolonych wartości.
![Wyliczanie listy w dół](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk **Zapisz** Aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli masz zapisane nowe wartości dla parametrów, zawsze możesz przywrócić wszystko, co do wartości domyślnych, wybierając **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista parametrów można skonfigurować serwera

Obsługiwany serwer z parametrów stale rośnie. Umożliwia karta parametry serwera w witrynie Azure portal Pobierz definicję i konfigurowanie parametrów serwera, w zależności od wymagań aplikacji.

## <a name="non-configurable-server-parameters"></a>Parametry można skonfigurować bez serwera

Pula buforów aparatu InnoDB i maksymalna liczba połączeń nie są konfigurowalne i związane z [warstwy cenowej](concepts-pricing-tiers.md).

|**Warstwa cenowa**| **rdzenie wirtualne:**|**Pula buforów aparatu InnoDB (MB)**| **Maksymalna liczba połączeń**|
|---|---|---|---|--|
|Podstawowa| 1| 1024| 50|
|Podstawowa| 2| 2560| 100|
|Ogólne zastosowanie| 2| 3584| 300|
|Ogólne zastosowanie| 4| 7680| 625|
|Ogólne zastosowanie| 8| 15360| 1250|
|Ogólne zastosowanie| 16| 31232| 2500|
|Ogólne zastosowanie| 32| 62976| 5000|
|Pamięć| 2| 7168| 600|
|Pamięć| 4| 15360| 1250|
|Pamięć| 8| 30720| 2500|
|Pamięć| 16| 62464| 5000|

<!-- 
|**Pricing Tier**| **Compute Generation**|**vCore(s)**|**InnoDB Buffer Pool (MB)**| **Max Connections**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 1024| 50|
|Basic| Gen 4| 2| 2560| 100|
|Basic| Gen 5| 1| 1024| 50|
|Basic| Gen 5| 2| 2560| 100|
|General Purpose| Gen 4| 2| 3584| 300|
|General Purpose| Gen 4| 4| 7680| 625|
|General Purpose| Gen 4| 8| 15360| 1250|
|General Purpose| Gen 4| 16| 31232| 2500|
|General Purpose| Gen 4| 32| 62976| 5000|
|General Purpose| Gen 5| 2| 3584| 300|
|General Purpose| Gen 5| 4| 7680| 625|
|General Purpose| Gen 5| 8| 15360| 1250|
|General Purpose| Gen 5| 16| 31232| 2500|
|General Purpose| Gen 5| 32| 62976| 5000|
|Memory Optimized| Gen 5| 2| 7168| 600|
|Memory Optimized| Gen 5| 4| 15360| 1250|
|Memory Optimized| Gen 5| 8| 30720| 2500|
|Memory Optimized| Gen 5| 16| 62464| 5000|
-->

Te parametry dodatkowy serwer nie są konfigurowane w systemie:

|**Parametr**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie podstawowa|WYŁ.|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Inne parametry serwera, które nie są wymienione w tym miejscu są ustawione na ich wartości domyślne out-of-box MariaDB [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametr strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefę czasową na serwerze można wypełnić przez wywołanie metody `az_load_timezone` przechowywane procedury przy użyciu narzędzia wiersza polecenia MySQL lub połączenia aplikacji MySQL Workbench.

> [!NOTE]
> Jeśli używasz `az_load_timezone` polecenia za pomocą aplikacji MySQL Workbench, konieczne może być pierwszym Wyłącz tryb awaryjny aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Aby wyświetlić wartości dostępne strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawienia globalne poziomu strefy czasowej

To ustawienie globalne poziomu strefy czasowej **parametrów serwera** strony w witrynie Azure portal. Poniżej ustawia globalne strefy czasowej na wartość "US / Pacyfik".

![Ustaw parametr strefy czasowej](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ustawienie strefy czasowej z poziomu sesji

Sesji można ustawić poziomu strefy czasowej, uruchamiając `SET time_zone` polecenia za pomocą narzędzia wiersza polecenia MySQL lub połączenia aplikacji MySQL Workbench. Poniższy przykład ustawia strefę czasową **USA / Pacyfik** strefy czasowej.

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->