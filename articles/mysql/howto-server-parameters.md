---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów serwera MySQL w usłudze Azure Database for MySQL za pomocą witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 8ec6f32d7db0161cef00330aa38601ba9bdb309d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893148"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Jak konfigurować parametry serwera w usłudze Azure Database for MySQL za pomocą witryny Azure portal

Usługa Azure Database for MySQL obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu witryny Azure portal. Nie wszystkie parametry serwera można dostosować.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera w witrynie Azure portal

1. Zaloguj się do witryny Azure portal, a następnie zlokalizuj usługi Azure Database for MySQL server.
2. W obszarze **ustawienia** kliknij **parametrów serwera** aby otworzyć stronę parametrów serwera usługi Azure Database dla serwera MySQL.
![Strona parametrów serwera portalu Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dopasować. Przegląd **opis** kolumny zrozumienie przeznaczenia i dozwolonych wartości.
![Wyliczanie listy w dół](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk **Zapisz** Aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli masz zapisane nowe wartości dla parametrów, zawsze możesz przywrócić wszystko, co do wartości domyślnych, wybierając **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista parametrów można skonfigurować serwera

Obsługiwany serwer z parametrów stale rośnie. Umożliwia karta parametry serwera w witrynie Azure portal Pobierz definicję i konfigurowanie parametrów serwera, w zależności od wymagań aplikacji.

## <a name="non-configurable-server-parameters"></a>Parametry można skonfigurować bez serwera

Nie można skonfigurować rozmiaru puli buforów InnoDB i powiązana z Twoją [warstwą cenową](concepts-service-tiers.md).

|**Warstwa cenowa**|**rdzenie wirtualne:**|**Rozmiar puli buforów InnoDB w MB <br>(serwery obsługujące do 4 TB magazynu)**| **Rozmiar puli buforów InnoDB w MB <br>(serwery obsługujące do 16 TB magazynu)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Ogólne zastosowanie| 2| 3584| 7168|
|Ogólne zastosowanie| 4| 7680| 15360|
|Ogólne zastosowanie| 8| 15360| 30720|
|Ogólne zastosowanie| 16| 31232| 62464|
|Ogólne zastosowanie| 32| 62976| 125952|
|Ogólne zastosowanie| 64| 125952| 251904|
|Zoptymalizowane pod kątem pamięci| 2| 7168| 14336|
|Zoptymalizowane pod kątem pamięci| 4| 15360| 30720|
|Zoptymalizowane pod kątem pamięci| 8| 30720| 61440|
|Zoptymalizowane pod kątem pamięci| 16| 62464| 124928|
|Zoptymalizowane pod kątem pamięci| 32| 125952| 251904|

Te parametry dodatkowy serwer nie są konfigurowane w systemie:

|**Parametr**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie podstawowa|WYŁĄCZONE|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Inne parametry serwera, które nie są wymienione w tym miejscu są ustawione na wartości domyślne out-of-box MySQL w wersji [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) i [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametr strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefę czasową na serwerze można wypełnić przez wywołanie metody `az_load_timezone` przechowywane procedury przy użyciu narzędzia wiersza polecenia MySQL lub połączenia aplikacji MySQL Workbench.

> [!NOTE]
> Jeśli używasz `az_load_timezone` polecenia za pomocą aplikacji MySQL Workbench, konieczne może być pierwszym Wyłącz tryb awaryjny aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele strefy czasowej są prawidłowo wypełnione. Aby ponownie uruchomić serwer, użyj [Azure Portal](howto-restart-server-portal.md) lub [interfejsu wiersza polecenia](howto-restart-server-cli.md).

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

Zapoznaj się z dokumentacją MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla usługi Azure Database for MySQL](concepts-connection-libraries.md).
