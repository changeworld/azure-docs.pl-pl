---
title: Konfigurowanie łączności SSL w celu bezpiecznego połączenia z usługą Azure Database for MySQL
description: Instrukcje dotyczące sposobu poprawnie skonfigurować — Azure Database for MySQL i skojarzonych aplikacji, aby prawidłowo używać połączeń SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/02/2019
ms.openlocfilehash: 46aca2c1a7d40df69b89e15917ff07b983f5ff5f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561465"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego połączenia z usługą Azure Database for MySQL
Usługa Azure Database for MySQL obsługuje łączenie usługi Azure Database for MySQL server z aplikacji klienckich za pomocą protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="step-1-obtain-ssl-certificate"></a>Krok 1: Uzyskaj certyfikat protokołu SSL
Pobierz certyfikat umożliwia komunikację za pośrednictwem protokołu SSL za pomocą usługi Azure Database for MySQL serwera z [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) i Zapisz plik certyfikatu na dysk lokalny (w tym samouczku używa c:\ssl na przykład).
**W przypadku programu Microsoft Internet Explorer i Microsoft Edge:** Po zakończeniu pobierania, Zmień nazwę certyfikatu BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Krok 2: Powiązanie SSL

Określone programowania języka parametrów połączenia, można znaleźć na stronie [przykładowego kodu](howto-configure-ssl.md#sample-code) poniżej.

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
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> W przypadku korzystania z interfejsu wiersza polecenia MySQL na Windows, może wystąpić błąd `SSL connection error: Certificate signature check failed`. Jeśli ten problem wystąpi, należy zastąpić `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametrów za pomocą `--ssl`.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Krok 3:  Wymuszanie połączeń SSL na platformie Azure 
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Przy użyciu witryny Azure portal, odwiedź stronę usługi Azure Database for MySQL server, a następnie kliknij przycisk **zabezpieczenia połączeń**. Użyj przycisku przełącznika, aby włączyć lub wyłączyć **połączenia Wymuszaj połączenie SSL** ustawienia, a następnie kliknij przycisk **Zapisz**. Firma Microsoft zaleca, aby umożliwić zawsze **połączenia Wymuszaj połączenie SSL** ustawienie w celu uzyskania zwiększonych zabezpieczeń.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Można włączać lub wyłączać **Wymuszanie protokołu ssl** parametru za pomocą wartości włączone lub wyłączone odpowiednio w interfejsie wiersza polecenia platformy Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Krok 4: Weryfikowanie połączenia SSL
Wykonaj mysql **stan** polecenie, aby sprawdzić, czy nawiązano połączenie z serwerem MySQL przy użyciu protokołu SSL:
```dos
mysql> status
```
Upewnij się, że połączenie jest zaszyfrowany, sprawdzając się dane wyjściowe powinny być widoczne:  **SSL: Szyfrowania używany jest algorytm SHA AES256** 

## <a name="sample-code"></a>Przykładowy kod
Aby nawiązać bezpiecznego połączenia do usługi Azure Database for MySQL za pośrednictwem protokołu SSL z aplikacji, zapoznaj się z poniższego przykładu kodu:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (przy użyciu PDO)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-mysql-connector-for-java"></a>Java (łącznik programu MySQL dla języka Java)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB łącznik dla języka Java)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
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

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj różne opcje łączności aplikacji, zgodnie z [biblioteki połączeń dla usługi Azure Database for MySQL](concepts-connection-libraries.md)
