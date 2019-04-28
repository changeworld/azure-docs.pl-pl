---
title: Łączenie aplikacji do usługi Azure Database for MySQL
description: Ten dokument zawiera listę aktualnie obsługiwanych parametrów połączenia dla aplikacji, aby połączyć się z usługą Azure Database for MySQL, w tym ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python i Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 503b7764ba7958a5448903b217b00e204ffcbf61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459288"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Jak połączyć aplikacje do usługi Azure Database for MySQL
Ten temat zawiera listę typów ciągów połączeń, które są obsługiwane przez usługę Azure Database for MySQL, wraz z szablony i przykłady. W ciągu połączenia, może mieć różne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [jak skonfigurować protokół SSL](./howto-configure-ssl.md).
- {your_host} = \<servername>.mysql.database.azure.com
- {your_user}@{servername} poprawnie = format identyfikatora użytkownika do uwierzytelniania.  Jeśli używasz tylko identyfikator userID, uwierzytelnianie nie powiedzie się.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

W tym przykładzie nazwa serwera jest `mydemoserver`, nazwa bazy danych jest `wpdb`, nazwa użytkownika jest `WPAdmin`, a hasło to `mypassword!2`. W rezultacie parametry połączenia powinny być:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły parametrów połączenia w witrynie Azure portal
W [witryny Azure portal](https://portal.azure.com), przejdź do usługi Azure Database for MySQL server, a następnie kliknij **parametry połączenia** można pobrać listy parametrów dla swojego wystąpienia: ![W okienku parametry połączenia w witrynie Azure portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Ciąg zawiera szczegółowe informacje, takie jak sterownik, serwera i innej bazy danych parametry połączenia. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Ten ciąg można następnie użyć nawiązać połączenie z serwerem z kodu oraz aplikacji.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat biblioteki połączeń, zobacz [pojęcia — bibliotek połączeń](./concepts-connection-libraries.md).
