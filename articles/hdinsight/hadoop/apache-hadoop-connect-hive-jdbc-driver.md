---
title: Zapytanie Hive za pośrednictwem sterownik JDBC - Azure HDInsight | Dokumentacja firmy Microsoft
description: Sterownik JDBC z aplikacji Java umożliwia wysyłanie zapytań programu Hive do platformy Hadoop w usłudze HDInsight. Połącz programowo i z klienta SQuirrel SQL.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: larryfr
ms.openlocfilehash: 9700408dad36591fc26aa159524f33965b9d1324
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31399419"
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Zapytanie Hive za pośrednictwem sterownik JDBC w usłudze HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Dowiedz się, jak umożliwia wysyłanie zapytań programu Hive do platformy Hadoop w usłudze Azure HDInsight sterownik JDBC z aplikacji Java. Informacje przedstawione w tym dokumencie pokazano, jak połączyć programowo i z klienta SQuirrel SQL.

Aby uzyskać więcej informacji na interfejsie JDBC Hive, zobacz [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Wymagania wstępne

* Hadoop w klastrze usługi HDInsight.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [wycofanie HDInsight 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL jest aplikacją kliencką JDBC.

* [Java Developer Kit (JDK) w wersji 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) lub nowszej.

* [Apache Maven](https://maven.apache.org). Maven jest projektem systemu dla projektów języka Java, używanego przez projekt skojarzony z tym artykułem kompilacji.

## <a name="jdbc-connection-string"></a>Parametry połączenia sterownika JDBC

JDBC połączenia z klastrem usługi HDInsight na platformie Azure są nawiązywane ponad 443, a ruch jest zabezpieczony przy użyciu protokołu SSL. Publiczny bramy, która klastrów sit za przekierowuje ruch do portu, który faktycznie nasłuchiwanie serwera HiveServer2. Następujący ciąg połączenia pokazuje format używany dla usługi HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Element `CLUSTERNAME` należy zastąpić nazwą klastra usługi HDInsight.

## <a name="authentication"></a>Authentication

Podczas ustanawiania połączenia, należy użyć nazwy administratora klastra usługi HDInsight i hasła do uwierzytelnienia klastra bramy. Podczas nawiązywania połączenia z klientami JDBC, takich jak SQuirreL SQL, wprowadź nazwę administratora i hasła w ustawieniach klienta.

Z poziomu aplikacji Java należy użyć nazwy i hasła podczas nawiązywania połączenia. Na przykład następujący kod w języku Java otwiera nowe połączenie przy użyciu parametrów połączenia, nazwa administratora i hasła:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Połączenie z klientem SQuirreL SQL

SQuirreL SQL jest klienta JDBC, który umożliwia zdalne uruchamianie zapytań Hive z klastrem usługi HDInsight. W następujących krokach założono zainstalowano SQuirreL SQL.

1. Utwórz katalog zawierający pliki. Na przykład `mkdir hivedriver`.

2. Z wiersza polecenia użyj następujących poleceń, aby skopiować pliki z klastrem usługi HDInsight:

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

    Zastąp `USERNAME` o nazwie konta użytkownika SSH dla klastra. Zastąp `CLUSTERNAME` z nazwą klastra usługi HDInsight.

3. Uruchom aplikację SQuirreL SQL. Od lewej części okna wybierz **sterowniki**.

    ![Sterowniki karty po lewej stronie okna](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Z ikony w górnej części **sterowniki** okno dialogowe, wybierz opcję **+** ikonę, aby utworzyć sterownika.

    ![Ikony sterowniki](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. W oknie dialogowym dodawania sterowników Dodaj następujące informacje:

    * **Nazwa**: Hive
    * **Przykładowy adres URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Dodatkowe ścieżki klasy**: Dodaj wszystkie pliki jar pobrane wcześniej za pomocą przycisku Dodaj
    * **Nazwa klasy**: org.apache.hive.jdbc.HiveDriver

   ![Dodaj sterownik okna dialogowego](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Kliknij przycisk **OK** Aby zapisać te ustawienia.

6. Po lewej stronie okna SQuirreL SQL, wybierz **aliasy**. Następnie kliknij przycisk **+** ikonę, aby utworzyć alias połączenia.

    ![Dodaj nowy alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Użyj poniższych wartości **Dodaj Alias** okna dialogowego.

    * **Nazwa**: Hive w usłudze HDInsight

    * **Sterownik**: Użyj listy rozwijanej, aby wybrać **Hive** sterownika

    * **ADRES URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

    * **Nazwa użytkownika**: Nazwa konta logowania klastra dla klastra usługi HDInsight. Wartość domyślna to `admin`.

    * **Hasło**: hasło do konta logowania klastra.

 ![Dodaj alias okna dialogowego](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Użyj **testu** przycisk, aby sprawdzić, czy połączenie działa. Gdy **nawiązać: Hive w usłudze HDInsight** zostanie wyświetlone okno dialogowe, wybierz **Connect** do wykonania testu. Jeśli test zakończy się powodzeniem, zobacz **połączenie przebiegło pomyślnie** okna dialogowego. Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów](#troubleshooting).

    Aby zapisać alias połączenie, użyj **Ok** przycisk w dolnej części **Dodaj Alias** okna dialogowego.

8. Z **nawiązać** wybierz z listy rozwijanej w górnej części SQuirreL SQL **Hive w usłudze HDInsight**. Po wyświetleniu monitu wybierz **Connect**.

    ![w oknie dialogowym połączenia](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Po nawiązaniu połączenia, wpisz poniższe zapytanie w oknie dialogowym zapytania SQL, a następnie wybierz **Uruchom** ikony. Wyniki zapytania powinien być wyświetlony w obszarze wyniki.

        select * from hivesampletable limit 10;

    ![okno dialogowe kwerendy SQL, łącznie z wynikami](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Połącz się z przykładem aplikacji Java

Przykład użycia Java klientowi zapytania Hive w usłudze HDInsight jest dostępna w [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Postępuj zgodnie z instrukcjami w repozytorium, aby skompilować i uruchomić próbki.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Wystąpił nieoczekiwany błąd wystąpił podczas próby otwarcia połączenia SQL

**Objawy**: podczas nawiązywania połączenia z klastrem usługi HDInsight 3.3 lub nowszego, może zostać wyświetlony błąd, który wystąpił nieoczekiwany błąd. Ślad stosu dla tego błędu rozpoczyna się od następujące wiersze:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Przyczyna**: ten błąd jest spowodowany przez starszej wersji commons codec.jar pliku SQuirreL.

**Rozdzielczość**: Aby naprawić ten błąd, wykonaj następujące kroki:

1. Pobierz plik jar koder-dekoder commons z klastrem usługi HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Zamknij SQuirreL, a następnie przejdź do katalogu, którym SQuirreL jest zainstalowany w systemie. W katalogu SquirreL w obszarze `lib` katalogu, Zastąp istniejące codec.jar commons każdy z nich pobrane z klastrem usługi HDInsight.

3. Uruchom ponownie SQuirreL. Błąd nie powinien wystąpić podczas nawiązywania połączenia Hive w usłudze HDInsight.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak pracować z Hive za pomocą JDBC, użyj następujących linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Wizualizacja gałęzi przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane interakcyjne zapytań Hive z usługą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Hadoop za pomocą dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Łączenie do usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md)
* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
