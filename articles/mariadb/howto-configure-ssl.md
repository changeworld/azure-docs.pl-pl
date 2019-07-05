---
title: Konfigurowanie łączności SSL w celu nawiązania bezpiecznego — Azure Database dla serwera MariaDB
description: Instrukcje dotyczące sposobu poprawnie skonfigurować — Azure Database for MariaDB oraz skojarzonych aplikacji, aby prawidłowo używać połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/02/2019
ms.openlocfilehash: 2c1b7e8f777f1975a20bbf63919a3dbfe543e683
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537743"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Konfigurowanie łączności SSL w aplikacji w celu nawiązania bezpiecznego połączenia usługi Azure Database dla serwera MariaDB
Azure Database dla serwera MariaDB obsługuje łączenie usługi Azure Database dla serwera MariaDB aplikacjom klienckim przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="obtain-ssl-certificate"></a>Uzyskaj certyfikat protokołu SSL
Pobierz certyfikat umożliwia komunikację za pośrednictwem protokołu SSL za pomocą usługi Azure Database dla serwera MariaDB z [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) i Zapisz plik certyfikatu na dysk lokalny (w tym samouczku używa c:\ssl na przykład).
**W przypadku programu Microsoft Internet Explorer i Microsoft Edge:** Po zakończeniu pobierania, Zmień nazwę certyfikatu BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>Powiązanie SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Nawiązywanie połączenia z serwerem za pośrednictwem protokołu SSL przy użyciu aplikacji MySQL Workbench
Konfigurowanie połączenia aplikacji MySQL Workbench nawiązać bezpiecznego połączenia za pośrednictwem protokołu SSL. 

1. Z okna dialogowego Konfigurowanie nowego połączenia, przejdź do **SSL** kartę. 

1. Aktualizacja **SSL użyj** pole "Wymagaj".

1. W **pliku urzędu certyfikacji SSL:** wprowadź lokalizację plików **BaltimoreCyberTrustRoot.crt.pem**. 
    
    ![Zapisz konfigurację protokołu SSL](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Dla istniejących połączeń można powiązania protokołu SSL, klikając prawym przyciskiem myszy ikonę połączenia i wybierz pozycję Edytuj. Następnie przejdź do **SSL** kartę i powiąż pliku certyfikatu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Nawiązywanie połączenia z serwerem za pośrednictwem protokołu SSL przy użyciu interfejsu wiersza polecenia MySQL
Innym sposobem, aby powiązać certyfikat protokołu SSL jest przy użyciu interfejsu wiersza polecenia MySQL, wykonując następujące polecenia. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> W przypadku korzystania z interfejsu wiersza polecenia MySQL na Windows, może wystąpić błąd `SSL connection error: Certificate signature check failed`. Jeśli ten problem wystąpi, należy zastąpić `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametrów za pomocą `--ssl`.

## <a name="enforcing-ssl-connections-in-azure"></a>Wymuszanie połączeń SSL na platformie Azure 
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
W witrynie Azure portal, odwiedź stronę usługi Azure Database dla serwera MariaDB, a następnie kliknij przycisk **zabezpieczenia połączeń**. Użyj przycisku przełącznika, aby włączyć lub wyłączyć **połączenia Wymuszaj połączenie SSL** ustawienia, a następnie kliknij przycisk **Zapisz**. Firma Microsoft zaleca, aby umożliwić zawsze **połączenia Wymuszaj połączenie SSL** ustawienie w celu uzyskania zwiększonych zabezpieczeń.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Można włączać lub wyłączać **Wymuszanie protokołu ssl** parametru za pomocą wartości włączone lub wyłączone odpowiednio w interfejsie wiersza polecenia platformy Azure.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Weryfikowanie połączenia SSL
Wykonaj mysql **stan** polecenie, aby sprawdzić, czy nawiązano połączenie serwera MariaDB przy użyciu protokołu SSL:
```sql
status
```
Upewnij się, że połączenie jest zaszyfrowany, sprawdzając się dane wyjściowe powinny być widoczne:  **SSL: Szyfrowania używany jest algorytm SHA AES256** 

## <a name="sample-code"></a>Przykładowy kod
Aby nawiązać bezpiecznego połączenia do usługi Azure Database dla serwera MariaDB za pośrednictwem protokołu SSL z aplikacji, zapoznaj się z poniższego przykładu kodu:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mariadb.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mariadb.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
