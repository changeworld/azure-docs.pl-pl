---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów serwera MariaDB w Azure Database for MariaDB przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: ba091d05aa243fab08138c96827d2f657d9755de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251507"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Jak skonfigurować parametry serwera w Azure Database for MariaDB przy użyciu Azure Portal

Azure Database for MariaDB obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu witryny Azure portal. Nie wszystkie parametry serwera można dostosować.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera w witrynie Azure portal

1. Zaloguj się do Azure Portal, a następnie zlokalizuj serwer Azure Database for MariaDB.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MariaDB.
![strony parametrów serwera Azure Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dopasować. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
Lista rozwijana ![wyliczania](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk **Zapisz** , aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
![Resetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista parametrów można skonfigurować serwera

Obsługiwany serwer z parametrów stale rośnie. Umożliwia karta parametry serwera w witrynie Azure portal Pobierz definicję i konfigurowanie parametrów serwera, w zależności od wymagań aplikacji.

## <a name="non-configurable-server-parameters"></a>Parametry można skonfigurować bez serwera

Pula buforów InnoDB i Maksymalna liczba połączeń nie są konfigurowane i powiązane z [warstwą cenową](concepts-pricing-tiers.md).

|**Warstwa cenowa**| **Rdzeń wirtualny**|**Pula buforów InnoDB (MB)**|
|---|---|---|
|Podstawowa| 1| 1024|
|Podstawowa| 2| 2560|
|Ogólnego przeznaczenia| 2| 3584|
|Ogólnego przeznaczenia| 4| 7680|
|Ogólnego przeznaczenia| 8| 15360|
|Ogólnego przeznaczenia| 16| 31232|
|Ogólnego przeznaczenia| 32| 62976|
|Ogólnego przeznaczenia| 64| 125952|
|Pamięć| 2| 7168|
|Pamięć| 4| 15360|
|Pamięć| 8| 30720|
|Pamięć| 16| 62464|
|Pamięć| 32| 125952|

Te parametry dodatkowy serwer nie są konfigurowane w systemie:

|**Konstruktora**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie podstawowa|WYŁĄCZONE|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Inne parametry serwera, które nie są wymienione w tym miejscu, są ustawione na wartości domyślne MariaDB dla [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametr strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie procedury składowanej `az_load_timezone` z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli uruchamiasz polecenie `az_load_timezone` z Workbench MySQL, może być konieczne wyłączenie bezpiecznego trybu aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

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

Strefę czasową na poziomie globalnym można ustawić na stronie **parametrów serwera** w Azure Portal. Poniżej ustawia globalne strefy czasowej na wartość "US / Pacyfik".

![Ustaw parametr strefy czasowej](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ustawienie strefy czasowej z poziomu sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając polecenie `SET time_zone` za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
