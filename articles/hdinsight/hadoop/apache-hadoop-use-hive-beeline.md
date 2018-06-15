---
title: Beeline za pomocą Apache Hive — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć klienta Beeline do uruchamiania zapytań Hive z usługą Hadoop w usłudze HDInsight. Narzędzie do pracy z serwera HiveServer2 za pośrednictwem JDBC jest beeline.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: gałąź beeline, beeline gałęzi
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: larryfr
ms.openlocfilehash: 970ccf19b5668bd57118fcabc5018c60352ebde7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795198"
---
# <a name="use-the-beeline-client-with-apache-hive"></a>Apache Hive za pomocą klienta Beeline

Dowiedz się, jak używać [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) do uruchamiania zapytań Hive w usłudze HDInsight.

Beeline jest klientem Hive, który znajduje się na head węzłów w klastrze usługi HDInsight. Beeline używa JDBC do łączenia się serwera HiveServer2, z usługą hostowaną w klastrze usługi HDInsight. Umożliwia także Beeline do zdalnego dostępu Hive w usłudze HDInsight w Internecie. Poniższe przykłady zapewniają najbardziej typowe parametry połączenia używane do nawiązania połączenia z Beeline HDInsight:

* __Przy użyciu Beeline z połączenia SSH do węzła headnode lub krawędzi__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Na komputerze klienckim, połączenie z usługi HDInsight za pośrednictwem sieci wirtualnej platformy Azure przy użyciu Beeline__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Na komputerze klienckim, połączenie z usługi HDInsight za pośrednictwem publicznego Internetu przy użyciu Beeline__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Zastąp `admin` przy użyciu konta logowania klastra dla klastra.
>
> Zastąp `password` przy użyciu hasła konta logowania klastra.
>
> Element `clustername` należy zastąpić nazwą klastra usługi HDInsight.
>
> Podczas nawiązywania połączenia z klastrem za pośrednictwem sieci wirtualnej, Zastąp `<headnode-FQDN>` z w pełni kwalifikowaną nazwę domeny z headnode klastra.

## <a id="prereq"></a>Wymagania wstępne

* Opartych na systemie Linux platformą Hadoop w klastrze usługi HDInsight w wersji 3.4 lub większą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient SSH lub lokalnego klienta Beeline. Większość czynności w tym dokumencie przyjęto założenie, używanym Beeline w sesji SSH do klastra. Informacje o działaniu Beeline z poza klastrem, zobacz [zdalnie użyć Beeline](#remote) sekcji.

    Aby uzyskać więcej informacji o korzystaniu z protokołu SSH, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Uruchamianie zapytań Hive

1. Podczas uruchamiania Beeline, musisz podać parametry połączenia dla serwera HiveServer2 w klastrze usługi HDInsight:

    * Podczas nawiązywania połączenia za pośrednictwem publicznego Internetu, należy podać nazwę konta logowania klastra (domyślna `admin`) i hasło. Na przykład za pomocą Beeline z systemu klienta do nawiązania połączenia `<clustername>.azurehdinsight.net` adres. To połączenie jest nawiązywane za pośrednictwem portu `443`i jest szyfrowana przy użyciu protokołu SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Podczas nawiązywania połączenia z sesji SSH headnode klastra, możesz nawiązać połączenie `headnodehost` adres portu `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Podczas nawiązywania połączenia za pośrednictwem sieci wirtualnej platformy Azure, należy podać pełną nazwę domeny (FQDN) węzła głównego klastra. Ponieważ to połączenie jest nawiązywane bezpośrednio do węzłów klastra, połączenie korzysta z portu `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Polecenia beeline rozpoczynać się od `!` znaków, na przykład `!help` Wyświetla Pomoc. Jednak `!` można pominąć w przypadku niektórych poleceń. Na przykład `help` działa.

    Brak `!sql`, które jest używane do wykonywania instrukcji HiveQL. Jednak HiveQL używa się tak najczęściej czy można pominąć w poprzednim `!sql`. Dwa poniższe instrukcje są równoważne:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    W nowym klastrze, znajduje się tylko jedną tabelę: **hivesampletable**.

3. Aby wyświetlić schemat hivesampletable, użyj następującego polecenia:

    ```hiveql
    describe hivesampletable;
    ```

    To polecenie zwraca następujące informacje:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Te informacje w tym artykule opisano kolumn w tabeli.

4. Wprowadź poniższe instrukcje, aby utworzyć tabelę o nazwie **log4jLogs** przy użyciu przykładowych danych dostarczone z klastrem usługi HDInsight:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Te instrukcje, wykonaj następujące czynności:

    * `DROP TABLE` — Jeśli tabela istnieje, jest usunięta.

    * `CREATE EXTERNAL TABLE` -Tworzy **zewnętrznych** tabeli w gałęzi rejestru. Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi. Dane pozostaną w oryginalnej lokalizacji.

    * `ROW FORMAT` — Sposób formatowania danych. W takim przypadku pól w każdym dzienniku są oddzielone spacją.

    * `STORED AS TEXTFILE LOCATION` — W przypadku gdy dane są przechowywane i w jaki format pliku.

    * `SELECT` -Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. To zapytanie zwraca wartość **3** są trzy wiersze, które zawierają tę wartość.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive próbuje stosowane do wszystkich plików w katalogu schematu. W takim przypadku katalog zawiera pliki, które nie pasuje do schematu. Aby zapobiec odzyskiwanie danych w wynikach, tej instrukcji informuje Hive, czy go powinno zwrócić dane tylko z plików w. dziennika.

  > [!NOTE]
  > Jeśli oczekujesz zaktualizowania za pomocą zewnętrznego źródła danych, należy użyć tabel zewnętrznych. Na przykład procesu przekazywania danych lub operacja MapReduce.
  >
  > Usunięcie tabeli zewnętrznej jest **nie** Usuń dane, definicję tabeli.

    Dane wyjściowe tego polecenia jest podobny do następującego tekstu:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Aby zakończyć Beeline, należy użyć `!exit`.

### <a id="file"></a>Uruchom plik HiveQL za pomocą Beeline

Wykonaj następujące kroki, aby utworzyć plik, a następnie uruchom go za pomocą Beeline.

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **query.hql**:

    ```bash
    nano query.hql
    ```

2. Użyj następującego tekstu jako zawartość pliku. To zapytanie tworzy nową tabelę "internal" o nazwie **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje, wykonaj następujące czynności:

    * **Utwórz Tabela Jeśli nie ISTNIEJE** — Jeśli tabela nie istnieje, jest tworzony. Ponieważ **zewnętrznych** — słowo kluczowe nie jest używany, ta instrukcja tworzy wewnętrznej tabeli. Wewnętrzny tabele są przechowywane w magazynie danych programu Hive i całkowicie zarządza Hive.
    * **ORC AS PRZECHOWYWANE** -przechowuje dane w formacie zoptymalizowanych pod kątem wiersza kolumnowy (ORC). ORC format jest wysoce zoptymalizowane i wydajne formatu do przechowywania danych Hive.
    * **ZASTĄP INSERT... Wybierz** -wybiera wiersze z **log4jLogs** tabeli, która zawiera **[Błąd]**, następnie wstawia dane do **errorLogs** tabeli.

    > [!NOTE]
    > W przeciwieństwie do tabel zewnętrznych porzucenie wewnętrznej tabeli powoduje usunięcie danych źródłowych.

3. Aby zapisać plik, użyj **Ctrl**+**_X**, wprowadź **Y**, a na końcu **Enter**.

4. Aby uruchomić go za pomocą Beeline, skorzystaj z następujących:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > `-i` Parametr uruchamia Beeline i uruchamia instrukcje w `query.hql` pliku. Po wykonaniu kwerendy przyjeździe `jdbc:hive2://headnodehost:10001/>` wiersza. Można również uruchomić pliku przy użyciu `-f` parametr, który zamyka Beeline po wykonaniu kwerendy.

5. Aby sprawdzić, czy **errorLogs** tabela została utworzona, użyj poniższych instrukcji, aby zwracanie wszystkich wierszy z **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Trzy wiersze danych ma zostać zwrócony, zawierający wszystkie **[Błąd]** w kolumnie t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Użyj Beeline zdalnie

Jeśli masz Beeline zainstalowane lokalnie, a połączenie za pośrednictwem publicznej sieci internet, należy użyć poniższych parametrów:

* __Parametry połączenia__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Nazwa logowania klastra__: `-n admin`

* __Hasło logowania klastra__ `-p 'password'`

Zastąp `clustername` w parametrach połączenia z nazwą klastra usługi HDInsight.

Zastąp `admin` z nazwą logowania do klastra i Zastąp `password` hasłem do logowania do klastra.

Jeśli masz Beeline zainstalowane lokalnie, a połączenie za pośrednictwem sieci wirtualnej platformy Azure, należy użyć poniższych parametrów:

* __Parametry połączenia__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Aby znaleźć nazwę FQDN headnode, skorzystaj z informacji w [Zarządzanie HDInsight przy użyciu interfejsu API REST Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentu.

## <a id="sparksql"></a>Beeline za pomocą Spark

Platforma Spark zawiera własną implementację serwera HiveServer2, która czasami jest nazywany serwera Spark Thrift. Ta usługa używa programu Spark SQL do rozpoznania zapytania zamiast gałęzi i może zapewnić lepszą wydajność, w zależności od tego zapytania.

__Ciąg połączenia__ używane podczas łączenia się za pośrednictwem Internetu różni się nieznacznie. Zamiast zawierający `httpPath=/hive2` jest `httpPath/sparkhive2`. Poniżej przedstawiono przykład łączenie za pośrednictwem Internetu:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Gdy połączenie bezpośrednio z głównym węzłem klastra lub zasobów w tej samej sieci wirtualnej platformy Azure jako klastra usługi HDInsight, port `10002` powinna być używana do serwera Spark Thrift zamiast `10001`. Poniżej przedstawiono przykład nawiązywania bezpośrednio do węzła głównego:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Następne kroki

Aby uzyskać więcej ogólnych informacji o Hive w usłudze HDInsight zobacz następujący dokument:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Więcej informacji na inne sposoby, które można pracować z platformą Hadoop w usłudze HDInsight na ten temat można znaleźć w następujących dokumentach:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Jeśli używasz aplikacji Tez przy użyciu Hive, można znaleźć w następujących dokumentach:

* [Użyj interfejsu użytkownika aplikacji Tez w usłudze HDInsight z systemu Windows](../hdinsight-debug-tez-ui.md)
* [Użyj widoku Ambari Tez w HDInsight opartych na systemie Linux](../hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
