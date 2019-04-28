---
title: Łączenie aplikacji do usługi Azure Database dla serwera MariaDB
description: Ten dokument zawiera listę aktualnie obsługiwanych parametrów połączenia dla aplikacji, aby połączyć się z usługą Azure Database dla serwera MariaDB, w tym ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python i Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61039686"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Jak połączyć aplikacje do usługi Azure Database dla serwera MariaDB
Ten temat zawiera listę typów ciągów połączeń obsługiwanych przez usługę Azure Database dla serwera MariaDB, wraz z szablony i przykłady. W ciągu połączenia, może mieć różne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [jak skonfigurować protokół SSL](./howto-configure-ssl.md).
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} poprawnie = format identyfikatora użytkownika do uwierzytelniania.  Jeśli używasz tylko identyfikator userID, uwierzytelnianie nie powiedzie się.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

W tym przykładzie nazwa serwera jest `mydemoserver`, nazwa bazy danych jest `wpdb`, nazwa użytkownika jest `WPAdmin`, a hasło to `mypassword!2`. W rezultacie parametry połączenia powinny być:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły parametrów połączenia w witrynie Azure portal
W [witryny Azure portal](https://portal.azure.com), przejdź do usługi Azure Database dla serwera MariaDB, a następnie kliknij przycisk **parametry połączenia** można pobrać listy parametrów dla swojego wystąpienia: ![W okienku parametry połączenia w witrynie Azure portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Ciąg zawiera szczegółowe informacje, takie jak sterownik, serwera i innej bazy danych parametry połączenia. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Ten ciąg można następnie użyć nawiązać połączenie z serwerem z kodu oraz aplikacji.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
