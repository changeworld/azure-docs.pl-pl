---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów serwera MariaDB w Azure Database for MariaDB przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3477820cb20d856c2e979cdfbe5528113bf4b562
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769408"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Jak skonfigurować parametry serwera w Azure Database for MariaDB przy użyciu Azure Portal

Azure Database for MariaDB obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu Azure Portal. Nie wszystkie parametry serwera można dostosować.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera na Azure Portal

1. Zaloguj się do Azure Portal, a następnie zlokalizuj serwer Azure Database for MariaDB.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MariaDB.
![strony parametrów serwera Azure Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dostosować. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
Lista rozwijana ![wyliczania](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk **Zapisz** , aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
![Resetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista konfigurowalnych parametrów serwera

Lista obsługiwanych parametrów serwera stale rośnie. Karta parametry serwera w Azure Portal służy do uzyskiwania definicji i konfigurowania parametrów serwera na podstawie wymagań aplikacji.

## <a name="non-configurable-server-parameters"></a>Parametry serwera, które nie zostały konfigurowalne

Pula buforów InnoDB i Maksymalna liczba połączeń nie są konfigurowane i powiązane z [warstwą cenową](concepts-pricing-tiers.md).

|**Warstwa cenowa**| **Rdzeń wirtualny**|**Pula buforów InnoDB (MB)**| **Maksymalna liczba połączeń**|
|---|---|---|---|
|Basic| 1| 1024| 50|
|Basic| 2| 2560| 100|
|Ogólne zastosowanie| 2| 3584| 300|
|Ogólne zastosowanie| 4| 7680| 625|
|Ogólne zastosowanie| 8| 15360| 1250|
|Ogólne zastosowanie| 16| 31232| 2500|
|Ogólne zastosowanie| 32| 62976| 5000|
|Ogólne zastosowanie| 64| 125952| 10 000|
|Zoptymalizowane pod kątem pamięci| 2| 7168| 600|
|Zoptymalizowane pod kątem pamięci| 4| 15360| 1250|
|Zoptymalizowane pod kątem pamięci| 8| 30720| 2500|
|Zoptymalizowane pod kątem pamięci| 16| 62464| 5000|
|Zoptymalizowane pod kątem pamięci| 32| 125952| 10 000|

Te dodatkowe parametry serwera nie są konfigurowane w systemie:

|**Konstruktora**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie Podstawowa|Logowanie|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Inne parametry serwera, które nie są wymienione w tym miejscu, są ustawione na wartości domyślne MariaDB dla [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie procedury składowanej `az_load_timezone` z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli uruchamiasz polecenie `az_load_timezone` z Workbench MySQL, może być konieczne wyłączenie bezpiecznego trybu aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić na stronie **parametrów serwera** w Azure Portal. Poniżej ustawia globalną strefę czasową na wartość "US/Pacyfik".

![Ustaw parametr strefy czasowej](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając polecenie `SET time_zone` za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
