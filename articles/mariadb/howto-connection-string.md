---
title: Parametry połączenia — usługa Azure Database for MariaDB
description: W tym dokumencie wymieniono aktualnie obsługiwane parametry połączenia dla aplikacji do łączenia się z usługą Azure Database dla mariadb, w tym ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python i Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 74574fb905ae4ebd2552ef97cd0b5430eea6363a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530227"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Jak połączyć aplikacje z usługą Azure Database for MariaDB
W tym temacie wymieniono typy ciągów połączeń, które są obsługiwane przez usługę Azure Database dla MariaDB, wraz z szablonami i przykładami. W ciągu połączenia mogą znajdować się różne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [Jak skonfigurować SSL](./howto-configure-ssl.md).
- {your_host} = [nazwa_serwera].mariadb.database.azure.com
- {your_user}@{nazwa_serwera} = format identyfikatora użytkownika do poprawnego uwierzytelniania.  Jeśli używasz tylko identyfikatora użytkownika, uwierzytelnianie zakończy się niepowodzeniem.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

W tym przykładzie nazwa `mydemoserver`serwera to `wpdb`, nazwa bazy `WPAdmin`danych jest `mypassword!2`, nazwa użytkownika jest , a hasło jest . W rezultacie parametry połączenia powinny być:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły ciągu połączenia z witryny Azure Portal
W [witrynie Azure portal](https://portal.azure.com)przejdź do usługi Azure Database dla serwera MariaDB, a następnie ![kliknij pozycję Parametry **połączenia,** aby uzyskać listę ciągów dla wystąpienia: Okienko Parametry połączenia w witrynie Azure portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Ciąg zawiera szczegóły, takie jak sterownik, serwer i inne parametry połączenia bazy danych. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Następnie można użyć tego ciągu, aby połączyć się z serwerem z kodu i aplikacji.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
