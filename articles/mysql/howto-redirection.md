---
title: Połącz się z przekierowaniem — usługa Azure Database for MySQL
description: W tym artykule opisano, jak skonfigurować aplikację do łączenia się z usługą Azure Database for MySQL z przekierowaniem.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246339"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Łączenie się z usługą Azure Database dla mysql z przekierowaniem

W tym temacie wyjaśniono, jak połączyć aplikację usługi Azure Database dla serwera MySQL z trybem przekierowania. Przekierowanie ma na celu zmniejszenie opóźnienia sieci między aplikacjami klienckimi a serwerami MySQL, umożliwiając aplikacjom bezpośrednie łączenie się z węzłami serwera wewnętrznej bazy danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się do [Portalu Azure](https://portal.azure.com). Utwórz usługę Azure Database dla serwera MySQL z aparatem w wersji 5.6, 5.7 lub 8.0. Aby uzyskać szczegółowe informacje, zobacz [Jak utworzyć usługę Azure Database dla serwera MySQL z portalu](quickstart-create-mysql-server-database-using-azure-portal.md) lub Jak utworzyć usługę Azure Database dla serwera [MySQL przy użyciu interfejsu WIERSZA POLECENIA.](quickstart-create-mysql-server-database-using-azure-cli.md)

Przekierowanie jest obecnie obsługiwane tylko wtedy, gdy **protokół SSL jest włączony** w bazie danych usługi Azure dla serwera MySQL. Aby uzyskać szczegółowe informacje na temat konfigurowania ssl, zobacz [Korzystanie z SSL z usługą Azure Database for MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Wsparcie dla przekierowania w aplikacjach PHP jest dostępne za pośrednictwem rozszerzenia [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) opracowanego przez Microsoft. 

Rozszerzenie mysqlnd_azure jest dostępne do dodania do aplikacji PHP za pośrednictwem PECL i zdecydowanie zaleca się zainstalowanie i skonfigurowanie rozszerzenia za pośrednictwem oficjalnie opublikowanego [pakietu PECL.](https://pecl.php.net/package/mysqlnd_azure)

> [!IMPORTANT]
> Obsługa przekierowania w rozszerzeniu [PHP mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) jest obecnie w wersji zapoznawczej.

### <a name="redirection-logic"></a>Logika przekierowania

>[!IMPORTANT]
> Logika przekierowania/zachowanie począwszy od wersji 1.1.0 została zaktualizowana i **zaleca się użycie wersji 1.1.0+**.

Zachowanie przekierowania zależy od wartości `mysqlnd_azure.enableRedirect`. W poniższej tabeli przedstawiono zachowanie przekierowania na podstawie wartości tego parametru rozpoczynającego się w **wersji 1.1.0+**.

Jeśli używasz starszej wersji rozszerzenia mysqlnd_azure (wersja 1.0.0-1.0.3), zachowanie przekierowania zależy od `mysqlnd_azure.enabled`wartości programu . Prawidłowe wartości `off` są (działa podobnie jak zachowanie opisane w `on` poniższej `preferred` tabeli) i (działa jak w poniższej tabeli).  

|**mysqlnd_azure.enableWarta 2018**| **Zachowanie**|
|----------------------------------------|-------------|
|`off` lub `0`|Przekierowanie nie będzie używane. |
|`on` lub `1`|- Jeśli protokół SSL nie jest włączony na serwerze usługi Azure Database for MySQL, nie zostanie nawiązane żadne połączenie. Zostanie zwrócony następujący błąd: *"mysqlnd_azure.enableRedirect jest włączony, ale opcja SSL nie jest ustawiona w ciągu połączenia. Przekierowanie jest możliwe tylko za pomocą SSL."*<br>- Jeśli protokół SSL jest włączony na serwerze MySQL, ale przekierowanie nie jest obsługiwane na serwerze, pierwsze połączenie zostanie przerwane i zwracany jest następujący błąd: *"Połączenie przerwane, ponieważ przekierowanie nie jest włączone na serwerze MySQL lub pakiet sieciowy nie spełnia protokołu przekierowania."*<br>- Jeśli serwer MySQL obsługuje przekierowanie, ale przekierowane połączenie nie powiodło się z jakiegokolwiek powodu, również przerwać pierwsze połączenie proxy. Zwraca błąd przekierowanego połączenia.|
|`preferred` lub `2`<br> (wartość domyślna)|- mysqlnd_azure użyje przekierowania, jeśli to możliwe.<br>- Jeśli połączenie nie korzysta z SSL, serwer nie obsługuje przekierowania lub przekierowane połączenie nie może połączyć się z jakiegokolwiek powodu nieśmiertelnego, podczas gdy połączenie proxy jest nadal prawidłowe, powróci do pierwszego połączenia proxy.|

W kolejnych sekcjach dokumentu opisano sposób `mysqlnd_azure` instalowania rozszerzenia przy użyciu listy PECL i ustawiać wartość tego parametru.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Wymagania wstępne 
- Wersje PHP 7.2.15+ i 7.3.2+
- GRUSZKA PHP 
- php-mysql
- Usługa Azure Database dla serwera MySQL z włączonym ssl

1. Zainstaluj [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) z [PECL](https://pecl.php.net/package/mysqlnd_azure). Zaleca się stosowanie wersji 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Znajdź katalog rozszerzeń (`extension_dir`) uruchamiając poniższe:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Zmień katalogi na zwrócony `mysqlnd_azure.so` folder i upewnij się, że znajduje się w tym folderze. 

4. Znajdź folder dla plików .ini, uruchamiając poniższe: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Zmień katalogi na ten zwrócony folder. 

6. Utwórz nowy plik .ini dla `mysqlnd_azure`pliku . Upewnij się, że kolejność alfabetu nazwy jest po mysqnld, ponieważ moduły są ładowane zgodnie z kolejnością nazw plików ini. Na przykład, `mysqlnd` jeśli nazwa `10-mysqlnd.ini`.ini jest nazwą , nazwa `20-mysqlnd-azure.ini`mysqlnd ini jako .

7. W nowym pliku .ini dodaj następujące wiersze, aby włączyć przekierowanie.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Wymagania wstępne 
- Wersje PHP 7.2.15+ i 7.3.2+
- php-mysql
- Usługa Azure Database dla serwera MySQL z włączonym ssl

1. Określ, czy korzystasz z wersji PHP x64 lub x86, uruchamiając następujące polecenie:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Pobierz odpowiednią wersję biblioteki [DLL mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) x64 lub x86 z [PECL,](https://pecl.php.net/package/mysqlnd_azure) która pasuje do Twojej wersji PHP. Zaleca się stosowanie wersji 1.1.0+.

3. Wyodrębnij plik zip i znajdź `php_mysqlnd_azure.dll`bibliotekę DLL o nazwie .

4. Znajdź katalog rozszerzeń (`extension_dir`) uruchamiając polecenie poniżej:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Skopiuj `php_mysqlnd_azure.dll` plik do katalogu zwróconego w kroku 4. 

6. Znajdź folder PHP zawierający plik za `php.ini` pomocą następującego polecenia:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Zmodyfikuj `php.ini` plik i dodaj następujące dodatkowe wiersze, aby włączyć przekierowanie. 

    W sekcji Rozszerzenia dynamiczne: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    W sekcji Ustawienia modułu:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Potwierdź przekierowanie

Możesz również potwierdzić, że przekierowanie jest skonfigurowane za pomocą poniższego przykładowego kodu PHP. Utwórz plik PHP o nazwie `mysqlConnect.php` i wklej poniższy kod. Zaktualizuj nazwę serwera, nazwę użytkownika i hasło za pomocą własnych. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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
Aby uzyskać więcej informacji na temat ciągów połączeń, zobacz [Parametry połączenia](howto-connection-string.md).
