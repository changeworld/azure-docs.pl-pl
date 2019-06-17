---
title: Zapytanie Apache Hive za pośrednictwem sterownika JDBC — Azure HDInsight
description: Użyj sterownik JDBC z poziomu aplikacji Java, aby przesłać zapytania usługi Apache Hive z usługą Hadoop w HDInsight. Połącz programowo i klient SQuirrel SQL.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: hrasheed
ms.openlocfilehash: 56a2b89277cbf8866c1992a6738bd80106ef3313
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480011"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Zapytanie Apache Hive za pośrednictwem sterownika JDBC w HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Dowiedz się, jak przesyłać zapytania usługi Apache Hive z usługą Apache Hadoop w usłudze Azure HDInsight przy użyciu sterownika JDBC z poziomu aplikacji Java. Informacje przedstawione w tym dokumencie pokazano, jak połączyć programowo, a klient SQuirreL SQL.

Aby uzyskać więcej informacji na temat interfejsu JDBC usługi Hive, zobacz [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi HDInsight Hadoop. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) w wersji 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) lub nowszej.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL jest aplikacją kliencką JDBC.

## <a name="jdbc-connection-string"></a>Parametry połączenia sterownika JDBC

Połączenia sterownika JDBC z klastrem usługi HDInsight na platformie Azure są nawiązywane za pośrednictwem portu 443, a ruch jest zabezpieczony przy użyciu protokołu SSL. Publicznej bramy, który klastry znajdują się za zaporą przekierowuje ruch do portu nasłuchiwania serwera HiveServer2 jest faktycznie na. Następujące parametry połączenia pokazuje format używany dla HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Element `CLUSTERNAME` należy zastąpić nazwą klastra usługi HDInsight.

## <a name="authentication"></a>Authentication

Podczas ustanawiania połączenia, należy użyć nazwy administratora klastra HDInsight i hasło do uwierzytelniania w klastrze bramy. Podczas nawiązywania połączenia z klientami JDBC, takich jak SQuirreL SQL, należy wprowadzić nazwę administratora i hasła w ustawieniach klienta.

Z poziomu aplikacji Java należy użyć nazwy i hasła podczas nawiązywania połączenia. Na przykład poniższy kod Java otwiera nowe połączenie przy użyciu parametrów połączenia, nazwę administratora i hasła:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Połącz się z klient SQuirreL SQL

SQuirreL SQL jest klient JDBC, który może służyć do zdalne uruchamianie zapytań Hive przy użyciu klastra usługi HDInsight. W następujących krokach założono, że już zainstalowano SQuirreL SQL.

1. Utwórz katalog zawiera określone pliki do skopiowania z klastrem.

2. W poniższym skrypcie Zamień `sshuser` nazwą konta użytkownika SSH dla klastra.  Zastąp `CLUSTERNAME` o nazwie klastra HDInsight.  W wierszu polecenia Zmień katalog roboczy utworzonym w poprzednim kroku, a następnie wprowadź następujące polecenie, aby skopiować pliki z klastra usługi HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-1.2*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Uruchom aplikację SQuirreL SQL. W lewej części okna, wybierz **sterowniki**.

    ![Sterowniki karty w lewej części okna](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Z ikon w górnej części **sterowniki** okno dialogowe, wybierz opcję **+** ikonę, aby utworzyć sterownika.

    ![Ikony sterowników](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. W oknie dialogowym dodawania sterowników Dodaj następujące informacje:

    * **Nazwa**: Hive
    * **Przykładowy adres URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Bardzo klasy ścieżki**: Użyj **Dodaj** przycisk, aby dodać wszystkie pliki JAR używane wcześniej pobrany
    * **Nazwa klasy**: org.apache.hive.jdbc.HiveDriver

   ![Dodaj okno dialogowe sterownika](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Wybierz **OK** można zapisać tych ustawień.

6. W lewej części okna SQuirreL SQL zaznaczyć **aliasy**. Następnie wybierz pozycję **+** ikonę, aby utworzyć alias połączenia.

    ![Dodaj nowy alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Użyj następujących wartości dla **Dodaj Alias** okna dialogowego.

    * **Nazwa**: Hive on HDInsight

    * **Sterownik**: Użyj listy rozwijanej, aby wybrać **Hive** sterownika

    * **ADRES URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

    * **Nazwa użytkownika**: Nazwa konta logowania klastra dla klastra usługi HDInsight. Wartość domyślna to `admin`.

    * **Hasło**: Hasło dla konta logowania klastra.

   ![Dodaj alias okno dialogowe](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Użyj **testu** przycisk, aby sprawdzić, czy połączenie działa. Gdy **nawiązać połączenie: Programu hive HDInsight** zostanie wyświetlone okno dialogowe, wybierz **Connect** do wykonania testu. Jeśli test zakończy się pomyślnie, zobaczysz **Łączenie przebiegło pomyślnie** okna dialogowego. Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów](#troubleshooting).

    Aby zapisać alias połączenie, użyj **Ok** znajdujący się u dołu **Dodaj Alias** okna dialogowego.

8. Z **nawiązać** listy rozwijanej w górnej części SQuirreL SQL zaznaczyć **programu Hive HDInsight**. Po wyświetleniu monitu wybierz **Connect**.

    ![okno dialogowe połączenia](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Po nawiązaniu połączenia, wprowadź następujące zapytanie do okna dialogowego zapytania SQL, a następnie wybierz **Uruchom** ikonę (uruchamianie osoby). W obszarze Wyniki powinny pokazywać wyniki zapytania.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![okno dialogowe kwerendy SQL, w tym wyniki](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Połącz się z przykładem aplikacji Java

Przykładem przy użyciu klienta języka Java do wykonywania zapytań programu Hive na HDInsight znajduje się w temacie [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Postępuj zgodnie z instrukcjami w repozytorium, aby skompilować i uruchomić próbki.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Wystąpił nieoczekiwany błąd podczas próby otwarcia połączenia SQL

**Objawy**: W przypadku nawiązywania połączenia z klastra usługi HDInsight, który jest w wersji 3.3 lub większą, może pojawić się błąd, który wystąpił nieoczekiwany błąd. Ślad stosu dla tego błędu rozpoczyna się z następującymi wierszami:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Przyczyna**: Ten błąd jest spowodowany przez starsze pliku commons codec.jar wersji dołączonej SQuirreL.

**Rozpoznawanie**: Aby naprawić ten błąd, wykonaj następujące kroki:

1. Zamknij SQuirreL, a następnie przejdź do katalogu, którym SQuirreL jest zainstalowany w systemie. W katalogu SquirreL w obszarze `lib` katalog, Zastąp istniejące codec.jar commons z wersją pobrane z klastra HDInsight.

2. Uruchom ponownie SQuirreL. Ten błąd nie jest już powinien wystąpić podczas nawiązywania połączenia z programu Hive na HDInsight.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy zawarto informacje dotyczące pracy z technologią Hive przy użyciu sterownika JDBC, użyj następujących linków, aby poznać inne sposoby pracy z usługi Azure HDInsight.

* [Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md)
* [Use Apache Hive z HDInsight](hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
