---
title: Zapytanie Apache Hive za pośrednictwem sterownika JDBC — Azure HDInsight
description: Użyj sterownik JDBC z poziomu aplikacji Java, aby przesłać zapytania usługi Apache Hive z usługą Hadoop w HDInsight. Połącz programowo i klient SQuirrel SQL.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: hrasheed
ms.openlocfilehash: 225cb3d2f78f41bdb17763d13644c1d95bc62710
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014705"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Zapytanie Apache Hive za pośrednictwem sterownika JDBC w HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Dowiedz się, jak przesyłać zapytania usługi Apache Hive z usługą Apache Hadoop w usłudze Azure HDInsight przy użyciu sterownika JDBC z poziomu aplikacji Java. Informacje przedstawione w tym dokumencie pokazano, jak połączyć programowo i klient SQuirrel SQL.

Aby uzyskać więcej informacji na temat interfejsu JDBC usługi Hive, zobacz [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Wymagania wstępne

* Usługi Hadoop w klastrze HDInsight.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [wycofanie HDInsight 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](https://squirrel-sql.sourceforge.net/). SQuirreL jest aplikacją kliencką JDBC.

* [Java Developer Kit (JDK) w wersji 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) lub nowszej.

* [Apache Maven](https://maven.apache.org). Maven to projekt system kompilacji dla projektów Java używanym przez projektu skojarzonego z tym artykułem.

## <a name="jdbc-connection-string"></a>Parametry połączenia sterownika JDBC

Połączenia sterownika JDBC z klastrem usługi HDInsight na platformie Azure są nawiązywane ponad 443, a ruch jest zabezpieczony przy użyciu protokołu SSL. Publicznej bramy, który klastry znajdują się za zaporą przekierowuje ruch do portu nasłuchiwania serwera HiveServer2 jest faktycznie na. Następujące parametry połączenia pokazuje format używany dla HDInsight:

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

1. Utwórz katalog, który zawiera pliki. Na przykład `mkdir hivedriver`.

2. Z poziomu wiersza polecenia użyj następujących poleceń, aby skopiować pliki z klastra HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    ```

    Zastąp `USERNAME` nazwą konta użytkownika SSH dla klastra. Zastąp `CLUSTERNAME` o nazwie klastra HDInsight.

3. Uruchom aplikację SQuirreL SQL. W lewej części okna, wybierz **sterowniki**.

    ![Sterowniki karty w lewej części okna](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Z ikon w górnej części **sterowniki** okno dialogowe, wybierz opcję **+** ikonę, aby utworzyć sterownika.

    ![Ikony sterowników](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. W oknie dialogowym dodawania sterowników Dodaj następujące informacje:

    * **Nazwa**: Hive
    * **Przykładowy adres URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Dodatkowe ścieżki klas**: Dodaj wszystkie pliki jar, pobrane wcześniej za pomocą przycisku Dodaj
    * **Nazwa klasy**: org.apache.hive.jdbc.HiveDriver

   ![Dodaj okno dialogowe sterownika](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Kliknij przycisk **OK** można zapisać tych ustawień.

6. W lewej części okna SQuirreL SQL zaznaczyć **aliasy**. Następnie kliknij przycisk **+** ikonę, aby utworzyć alias połączenia.

    ![Dodaj nowy alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Użyj następujących wartości dla **Dodaj Alias** okna dialogowego.

    * **Nazwa**: programu Hive HDInsight

    * **Sterownik**: Użyj listy rozwijanej, aby wybrać **Hive** sterownika

    * **ADRES URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

    * **Nazwa użytkownika**: Nazwa konta logowania klastra dla klastra usługi HDInsight. Wartość domyślna to `admin`.

    * **Hasło**: hasło dla konta logowania klastra.

 ![Dodaj alias okno dialogowe](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Użyj **testu** przycisk, aby sprawdzić, czy połączenie działa. Gdy **nawiązać połączenie: programu Hive HDInsight** zostanie wyświetlone okno dialogowe, wybierz **Connect** do wykonania testu. Jeśli test zakończy się pomyślnie, zobaczysz **Łączenie przebiegło pomyślnie** okna dialogowego. Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów](#troubleshooting).

    Aby zapisać alias połączenie, użyj **Ok** znajdujący się u dołu **Dodaj Alias** okna dialogowego.

8. Z **nawiązać** listy rozwijanej w górnej części SQuirreL SQL zaznaczyć **programu Hive HDInsight**. Po wyświetleniu monitu wybierz **Connect**.

    ![okno dialogowe połączenia](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Po nawiązaniu połączenia, wprowadź następujące zapytanie do okna dialogowego zapytania SQL, a następnie wybierz **Uruchom** ikony. W obszarze Wyniki powinny pokazywać wyniki zapytania.

        select * from hivesampletable limit 10;

    ![okno dialogowe kwerendy SQL, w tym wyniki](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Połącz się z przykładem aplikacji Java

Przykładem przy użyciu klienta języka Java do wykonywania zapytań programu Hive na HDInsight znajduje się w temacie [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Postępuj zgodnie z instrukcjami w repozytorium, aby skompilować i uruchomić próbki.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Wystąpił nieoczekiwany błąd podczas próby otwarcia połączenia SQL

**Objawy**: podczas łączenia z klastra usługi HDInsight, który jest w wersji 3.3 lub większą, może wystąpić błąd, który wystąpił nieoczekiwany błąd. Ślad stosu dla tego błędu rozpoczyna się z następującymi wierszami:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Przyczyna**: starszy plik commons codec.jar wersji dołączonej SQuirreL przyczyną tego błędu.

**Rozpoznawanie**: Aby naprawić ten błąd, wykonaj następujące czynności:

1. Pobranie pliku jar koder commons z klastra usługi HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Zamknij SQuirreL, a następnie przejdź do katalogu, którym SQuirreL jest zainstalowany w systemie. W katalogu SquirreL w obszarze `lib` katalog, Zastąp istniejące codec.jar commons z wersją pobrane z klastra HDInsight.

3. Uruchom ponownie SQuirreL. Ten błąd nie jest już powinien wystąpić podczas nawiązywania połączenia z programu Hive na HDInsight.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy zawarto informacje dotyczące pracy z technologią Hive przy użyciu sterownika JDBC, użyj następujących linków, aby poznać inne sposoby pracy z usługi Azure HDInsight.

* [Wizualizuj dane programu Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z usługą Hadoop przy użyciu dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md)
* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
