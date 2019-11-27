---
title: Nawiązywanie połączenia z usługą Azure Database for MySQL przy użyciu przekierowania
description: W tym artykule opisano, jak można skonfigurować aplikację do łączenia się z Azure Database for MySQL przy użyciu przekierowania.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233742"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Nawiązywanie połączenia z usługą Azure Database for MySQL przy użyciu przekierowania

W tym temacie opisano sposób łączenia aplikacji Azure Database for MySQL serwerze z trybem przekierowywania. Przekierowywanie ma na celu zmniejszenie opóźnienia sieci między aplikacjami klienckimi a serwerami MySQL, umożliwiając aplikacjom bezpośrednie łączenie się z węzłami serwera zaplecza.

> [!IMPORTANT]
> Obsługa przekierowania w [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) php jest obecnie w wersji zapoznawczej.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Utwórz serwer Azure Database for MySQL przy użyciu aparatu w wersji 5,6, 5,7 lub 8,0. Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć serwer Azure Database for MySQL z poziomu portalu](quickstart-create-mysql-server-database-using-azure-portal.md) lub [jak utworzyć serwer Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](quickstart-create-mysql-server-database-using-azure-cli.md).

Przekierowanie jest obecnie obsługiwane tylko wtedy, gdy jest włączony protokół SSL. Aby uzyskać szczegółowe informacje na temat konfigurowania protokołu SSL, zobacz [Używanie protokołu SSL z Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Wymagania wstępne 
- Wersje PHP 7.2.15 + i 7.3.2 +
- PHP 
- PHP — MySQL
- Serwer Azure Database for MySQL z włączonym protokołem SSL

1. Zainstaluj [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) z [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Znajdź katalog rozszerzeń (`extension_dir`), uruchamiając następujące czynności:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Zmień katalog na zwrócony folder i upewnij się, że `mysqlnd_azure.so` znajduje się w tym folderze. 

4. Zlokalizuj folder dla plików. ini, uruchamiając następujące czynności: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Zmień katalogi na ten zwrócony folder. 

6. Utwórz nowy plik ini dla `mysqlnd_azure`. Upewnij się, że kolejność alfabetyczna nazwy jest po mysqnld, ponieważ moduły są ładowane zgodnie z kolejnością nazw plików ini. Jeśli na przykład plik `mysqlnd`. ini ma nazwę `10-mysqlnd.ini`, nazwij mysqlnd ini jako `20-mysqlnd-azure.ini`.

7. W nowym pliku ini Dodaj następujące wiersze, aby włączyć przekierowywanie.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Wymagania wstępne 
- Wersje PHP 7.2.15 + i 7.3.2 +
- PHP — MySQL
- Serwer Azure Database for MySQL z włączonym protokołem SSL

1. Sprawdź, czy korzystasz z programu PHP w wersji x64 lub x86, uruchamiając następujące polecenie:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Pobierz odpowiednią wersję x64 lub x86 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll z [PECL](https://pecl.php.net/package/mysqlnd_azure) , która pasuje do używanej wersji języka PHP. 

3. Wyodrębnij plik zip i Znajdź bibliotekę DLL o nazwie `php_mysqlnd_azure.dll`.

4. Znajdź katalog rozszerzeń (`extension_dir`), uruchamiając następujące polecenie:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Skopiuj plik `php_mysqlnd_azure.dll` do katalogu zwróconego w kroku 4. 

6. Zlokalizuj folder PHP zawierający plik `php.ini` za pomocą następującego polecenia:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Zmodyfikuj plik `php.ini` i Dodaj następujące dodatkowe wiersze, aby włączyć przekierowywanie. 

    W sekcji rozszerzenia dynamiczne: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    W sekcji Ustawienia modułu:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Potwierdź przekierowanie

Możesz również potwierdzić, że przekierowanie jest skonfigurowane przy użyciu poniższego przykładowego kodu w języku PHP. Utwórz plik PHP o nazwie `mysqlConnect.php` i wklej poniższy kod. Zaktualizuj nazwę serwera, nazwę użytkownika i hasło. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Parametry połączenia](howto-connection-string.md).

