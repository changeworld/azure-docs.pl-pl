---
title: Konfiguracja połączenia SSL bezpiecznie łączyć się z bazą danych Azure dla programu MySQL
description: Instrukcje poprawnie skonfigurować bazy danych Azure MySQL i skojarzonych aplikacji, aby poprawnie używać połączeń SSL
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ea4fd90670c2137fbe6127ba5c0b7641d846f407
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265431"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfiguracja połączenia SSL w aplikacji w celu bezpiecznego łączenia z bazą danych Azure dla programu MySQL
Bazy danych platformy Azure dla programu MySQL obsługuje połączenie bazy danych Azure, aby serwer MySQL aplikacji klienta przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

## <a name="step-1-obtain-ssl-certificate"></a>Krok 1: Uzyskiwanie certyfikatu SSL
Pobierz certyfikat potrzebne do komunikowania się za pośrednictwem protokołu SSL z bazy danych Azure, aby serwer MySQL z [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) i Zapisz plik certyfikatu na dysk lokalny (w tym samouczku korzysta z c:\ssl na przykład).
**Programu Microsoft Internet Explorer oraz Microsoft Edge:** po zakończeniu pobierania, Zmień nazwę certyfikatu na BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Krok 2: Powiązanie SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Łączenie z serwerem przy użyciu narzędzia MySQL Workbench za pośrednictwem protokołu SSL
Konfigurowanie środowiska roboczego MySQL, aby bezpieczne łączenie się za pośrednictwem protokołu SSL. Z dialogu instalacji nowego połączenia, przejdź do **SSL** kartę. W **pliku urząd certyfikacji SSL:** wprowadź lokalizację pliku **BaltimoreCyberTrustRoot.crt.pem**. 
![Zapisz niestandardowe kafelka](./media/howto-configure-ssl/mysql-workbench-ssl.png) dla istniejących połączeń, powiązania SSL, klikając prawym przyciskiem myszy ikonę połączenia i wybierz polecenie Edycja. Następnie przejdź do **SSL** karcie i powiąż pliku certyfikatu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Łączenie z serwerem przy użyciu interfejsu wiersza polecenia MySQL za pośrednictwem protokołu SSL
Innym sposobem powiązać certyfikatu SSL jest przy użyciu interfejsu wiersza polecenia MySQL, wykonując następujące polecenie:
```dos
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Krok 3: Wymuszanie połączeń SSL na platformie Azure 
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Przy użyciu portalu Azure, odwiedź bazy danych Azure, aby serwer MySQL, a następnie kliknij przycisk **zabezpieczenia połączeń**. Użyj przycisku przełączania, aby włączyć lub wyłączyć **połączenia SSL wymusić** ustawienia, a następnie kliknij przycisk **zapisać**. Firma Microsoft zaleca, aby włączyć zawsze **połączenia SSL wymusić** ustawienie w celu zwiększenia bezpieczeństwa.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Można włączyć lub wyłączyć **wymuszania ssl** parametr przy użyciu wartości włączone lub wyłączone odpowiednio w wiersza polecenia platformy Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Krok 4: Sprawdź połączenia SSL
Wykonanie mysql **stan** polecenie, aby zweryfikować, że nawiązano połączenie z serwerem MySQL przy użyciu protokołu SSL:
```dos
mysql> status
```
Upewnij się, połączenie jest szyfrowane, przeglądając dane wyjściowe, które powinny być widoczne: **SSL: szyfrowania używany jest algorytm SHA AES256** 

## <a name="sample-code"></a>Przykładowy kod
Można ustanowić bezpiecznego połączenia do bazy danych Azure dla programu MySQL za pośrednictwem protokołu SSL z aplikacji, można znaleźć w następujących przykładach kodu:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
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
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
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
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
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
### <a name="javamariadb"></a>Java(MariaDB)
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

## <a name="next-steps"></a>Kolejne kroki
Przegląd różnych opcji łączności aplikacji po [biblioteki połączeń dla bazy danych Azure dla programu MySQL](concepts-connection-libraries.md)
