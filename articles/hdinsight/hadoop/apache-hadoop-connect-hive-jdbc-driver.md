---
title: Zapytanie Apache Hive za pomocą sterownika JDBC — Azure HDInsight
description: Użyj sterownika JDBC z aplikacji Java, aby przesłać Apache Hive zapytania do usługi Hadoop w usłudze HDInsight. Połącz programowo i z klienta SQL SQuirrel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 016107248399e84b7a82a656c9d590c3cbe0cdbe
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77466930"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Wysyłanie zapytań do Apache Hive za pomocą sterownika JDBC w usłudze HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Dowiedz się, w jaki sposób używać sterownika JDBC z poziomu aplikacji Java do przesyłania Apache Hive zapytań do Apache Hadoop w usłudze Azure HDInsight. Informacje przedstawione w tym dokumencie przedstawiają sposób nawiązywania połączenia programowo i z klienta SQL SQuirreL.

Aby uzyskać więcej informacji na temat interfejsu JDBC programu Hive, zobacz [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Wymagania wstępne

* HDInsight An klastra Hadoop. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Upewnij się, że usługa serwera hiveserver2 jest uruchomiona.
* [Zestaw Java developer Kit (JDK) w wersji 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) lub nowszej.
* [Squirrel SQL](http://squirrel-sql.sourceforge.net/). SQuirreL jest aplikacją kliencką JDBC.

## <a name="jdbc-connection-string"></a>Parametry połączenia sterownika JDBC

Połączenia JDBC z klastrem usługi HDInsight na platformie Azure są realizowane za pośrednictwem portu 443, a ruch jest zabezpieczony przy użyciu protokołu SSL. Brama publiczna, w której znajdują się klastry, przekierowuje ruch do portu, na którym nasłuchuje serwera hiveserver2. Poniższe parametry połączenia przedstawiają format do użycia w usłudze HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Element `CLUSTERNAME` należy zastąpić nazwą klastra usługi HDInsight.

## <a name="authentication"></a>Uwierzytelnianie

Podczas ustanawiania połączenia należy użyć nazwy i hasła administratora klastra usługi HDInsight w celu uwierzytelnienia na bramę klastra. Podczas nawiązywania połączenia od klientów JDBC, takich jak SQuirreL SQL, należy wprowadzić nazwę i hasło administratora w ustawieniach klienta.

W aplikacji Java należy użyć nazwy i hasła podczas ustanawiania połączenia. Na przykład poniższy kod Java otwiera nowe połączenie przy użyciu parametrów połączenia, nazwy administratora i hasła:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Łączenie z klientem SQL SQuirreL

SQuirreL SQL to klient JDBC, za pomocą którego można zdalnie uruchamiać zapytania Hive w klastrze usługi HDInsight. W poniższych krokach przyjęto założenie, że już zainstalowano program SQuirreL SQL.

1. Utwórz katalog zawierający pliki do skopiowania z klastra.

2. W poniższym skrypcie Zastąp `sshuser` nazwą konta użytkownika SSH dla klastra.  Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight.  W wierszu polecenia Zmień katalog roboczy na taki, który został utworzony w poprzednim kroku, a następnie wprowadź następujące polecenie, aby skopiować pliki z klastra usługi HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Uruchom aplikację SQL SQuirreL. Z lewej strony okna wybierz pozycję **sterowniki**.

    ![Karta Sterowniki po lewej stronie okna](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Z ikon w górnej części okna dialogowego **sterowniki** wybierz ikonę **+** , aby utworzyć sterownik.

    ![Ikona sterowników aplikacji SQL SQuirreL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. W oknie dialogowym Dodawanie sterownika Dodaj następujące informacje:

    |Właściwość | Wartość |
    |---|---|
    |Name (Nazwa)|Hive|
    |Przykładowy adres URL|JDBC: hive2://localhost: 443/domyślnie; transportmode = HTTP; SSL = true; httpPath =/hive2|
    |Dodatkowa ścieżka klasy|Użyj przycisku **Dodaj** , aby dodać wszystkie pobrane wcześniej pliki jar.|
    |Nazwa klasy|org. Apache. Hive. JDBC. HiveDriver|

   ![okno dialogowe Dodawanie sterownika z parametrami](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Wybierz **przycisk OK** , aby zapisać te ustawienia.

6. Po lewej stronie okna SQuirreL SQL Wybierz pozycję **aliasy**. Następnie wybierz ikonę **+** , aby utworzyć alias połączenia.

    ![Okno dialogowe Dodawanie nowego aliasu w programie SQL SQuirreL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. W oknie dialogowym **Dodawanie aliasu** należy użyć następujących wartości:

    |Właściwość |Wartość |
    |---|---|
    |Name (Nazwa)|Usługa Hive w usłudze HDInsight|
    |Sterownik|Użyj listy rozwijanej, aby wybrać sterownik **Hive** .|
    |Adres URL|JDBC: hive2://CLUSTERNAME.azurehdinsight.net: 443/domyślnie; transportmode = HTTP; SSL = true; httpPath =/hive2. Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:|
    |User Name (Nazwa użytkownika)|Nazwa konta logowania klastra dla klastra usługi HDInsight. Wartość domyślna to **admin**.|
    |Hasło|Hasło do konta logowania do klastra.|

    ![Dodaj okno dialogowe aliasu z parametrami](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Użyj przycisku **Testuj** , aby sprawdzić, czy połączenie działa. Po **nawiązaniu połączenia z: zostanie wyświetlone okno dialogowe Hive w usłudze HDInsight** , a następnie wybierz pozycję **Połącz** , aby wykonać test. Jeśli test zakończy się pomyślnie, zostanie wyświetlone okno dialogowe **pomyślne połączenie** . Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów](#troubleshooting).

    Aby zapisać alias połączenia, użyj przycisku **OK** u dołu okna dialogowego **Dodawanie aliasu** .

8. Z listy rozwijanej **Połącz** z na początku SQuirreL SQL Wybierz pozycję **Hive w usłudze HDInsight**. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    ![okno dialogowe połączenia z parametrami](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Po nawiązaniu połączenia wprowadź następujące zapytanie do okna dialogowego zapytania SQL, a następnie wybierz ikonę **Run** (uruchomiona osoba). Obszar wyników powinien zawierać wyniki zapytania.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![okno dialogowe kwerendy SQL, w tym wyniki](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Nawiązywanie połączenia z przykładową aplikacją Java

Przykład użycia klienta Java do wykonywania zapytań w usłudze HDInsight jest dostępny w [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Postępuj zgodnie z instrukcjami w repozytorium, aby skompilować i uruchomić przykład.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Wystąpił nieoczekiwany błąd podczas próby otwarcia połączenia SQL

**Objawy**: podczas nawiązywania połączenia z klastrem usługi HDInsight w wersji 3,3 lub nowszej może pojawić się komunikat o błędzie, który wystąpił nieoczekiwany błąd. Ślad stosu dla tego błędu rozpoczyna się od następujących wierszy:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Przyczyna**: ten błąd jest spowodowany przez starszą wersję pliku Commons-Codec. jar dołączoną do SQuirreL.

**Rozwiązanie**: Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Zamknij SQuirreL, a następnie przejdź do katalogu, w którym SQuirreL jest zainstalowany w systemie, prawdopodobnie `C:\Program Files\squirrel-sql-4.0.0\lib`. W katalogu SquirreL w katalogu `lib` Zastąp istniejący plik Commons-Codec. jar, który został pobrany z klastra usługi HDInsight.

1. Uruchom ponownie SQuirreL. Błąd nie powinien już występować podczas nawiązywania połączenia z usługą Hive w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać JDBC do pracy z usługą Hive, Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Wizualizuj Apache Hive dane za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizowanie interakcyjnych zapytań danych Hive za pomocą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Użyj programu Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z usługą HDInsight przy użyciu Sterownik Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z Apache Hadoop przy użyciu Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight do Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md)
* [Korzystanie z Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z Apache świni z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
