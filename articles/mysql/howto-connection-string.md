---
title: Parametry połączenia — usługa Azure Database for MySQL
description: W tym dokumencie wymieniono aktualnie obsługiwane parametry połączenia dla aplikacji do łączenia się z usługą Azure Database for MySQL, w tym ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python i Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ff257e101ca925a554f2343d357e2042d2930b45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062392"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Jak połączyć aplikacje z usługą Azure Database for MySQL
W tym temacie wymieniono typy ciągów połączeń, które są obsługiwane przez usługę Azure Database for MySQL, wraz z szablonami i przykładami. W ciągu połączenia mogą znajdować się różne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [Jak skonfigurować SSL](./howto-configure-ssl.md).
- {your_host} = \<nazwa_serwera>.mysql.database.azure.com
- {your_user}@{nazwa_serwera} = format identyfikatora użytkownika do poprawnego uwierzytelniania.  Jeśli używasz tylko identyfikatora użytkownika, uwierzytelnianie zakończy się niepowodzeniem.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

W tym przykładzie nazwa `mydemoserver`serwera to `wpdb`, nazwa bazy `WPAdmin`danych jest `mypassword!2`, nazwa użytkownika jest , a hasło jest . W rezultacie parametry połączenia powinny być:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły ciągu połączenia z witryny Azure Portal
W [witrynie Azure portal](https://portal.azure.com)przejdź do bazy danych platformy Azure dla serwera MySQL, a ![następnie kliknij pozycję Parametry połączenia, aby uzyskać listę **ciągów** dla twojego wystąpienia: Okienko Parametry połączenia w witrynie Azure portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Ciąg zawiera szczegóły, takie jak sterownik, serwer i inne parametry połączenia bazy danych. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Następnie można użyć tego ciągu, aby połączyć się z serwerem z kodu i aplikacji.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat bibliotek połączeń, zobacz [Pojęcia — Biblioteki połączeń](./concepts-connection-libraries.md).
