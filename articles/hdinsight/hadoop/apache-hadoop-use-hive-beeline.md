---
title: Korzystanie z platformy Apache Z usługi Beeline z usługą Apache Hive — Azure HDInsight
description: Dowiedz się, jak używać klienta Z usługi beeline do uruchamiania zapytań Hive z usługą Hadoop w usłudze HDInsight. Z usługi Beeline to narzędzie do pracy z serwera hiveserver2em przez JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d97470494af0d64cc20d78d69957d84a8acebc16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494910"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Korzystanie z klienta Apache Z usługi Beeline z usługą Apache Hive

Dowiedz się, jak używać oprogramowania [Apache z usługi Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) do uruchamiania zapytań Apache Hive w usłudze HDInsight.

Z usługi Beeline jest klientem programu Hive, który znajduje się w węzłach głównych klastra usługi HDInsight. Z usługi Beeline używa JDBC do nawiązywania połączenia z serwera hiveserver2, usługą hostowaną w klastrze usługi HDInsight. Możesz również użyć Z usługi Beeline, aby zdalnie uzyskiwać dostęp do usługi Hive w usłudze HDInsight za pośrednictwem Internetu. W poniższych przykładach przedstawiono najbardziej typowe parametry połączenia używane do nawiązywania połączenia z usługą HDInsight z Z usługi Beeline:

## <a name="types-of-connections"></a>Typy połączeń

### <a name="from-an-ssh-session"></a>Z sesji SSH

Podczas nawiązywania połączenia z sesji SSH z klastrem węzła głównego można połączyć się z adresem `headnodehost` na porcie `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Za pośrednictwem Virtual Network platformy Azure

Podczas nawiązywania połączenia z klientem z usługą HDInsight za pośrednictwem usługi Azure Virtual Network należy podać w pełni kwalifikowaną nazwę domeny (FQDN) węzła głównego klastra. Ponieważ to połączenie jest nawiązywane bezpośrednio z węzłami klastra, połączenie używa `10001`portów:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Zastąp `<headnode-FQDN>` z w pełni kwalifikowaną nazwą domeny klastra węzła głównego. Aby znaleźć w pełni kwalifikowaną nazwę domeny węzła głównego, Skorzystaj z informacji w temacie [Zarządzanie usługą HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) .

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Do klastra usługi HDInsight pakiet Enterprise Security (ESP) przy użyciu protokołu Kerberos

Podczas nawiązywania połączenia z klientem z klastrem pakiet Enterprise Security (ESP) przyłączonym do Azure Active Directory (AAD) — DS na komputerze w tym samym obszarze klastra należy również określić nazwę domeny `<AAD-Domain>` i nazwę konta użytkownika domeny z uprawnieniami Uzyskaj dostęp do `<username>`klastra:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Zastąp `<username>` nazwą konta w domenie uprawnieniami dostępu do klastra. Zastąp `<AAD-DOMAIN>` nazwą Azure Active Directory (AAD), do której jest dołączony klaster. Użyj ciągu wielką literą dla wartości `<AAD-DOMAIN>`, w przeciwnym razie nie zostanie znalezione poświadczenie. W razie potrzeby Sprawdź `/etc/krb5.conf` nazw obszarów.

---

### <a name="over-public-or-private-endpoints"></a>Za pośrednictwem publicznych lub prywatnych punktów końcowych

Podczas nawiązywania połączenia z klastrem przy użyciu publicznych lub prywatnych punktów końcowych należy podać nazwę konta logowania klastra (domyślnie `admin`) i hasło. Na przykład w celu nawiązania połączenia z adresem `<clustername>.azurehdinsight.net` przy użyciu Z usługi Beeline z systemu klienckiego. To połączenie jest nawiązywane za pośrednictwem `443`portów i szyfrowane przy użyciu protokołu SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zastąp `<username>` kontem logowania klastra dla klastra. Uwaga w przypadku klastrów ESP Użyj pełnej nazwy UPN (np. user@domain.com). Zastąp `password` hasłem dla konta logowania klastra.

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych komunikacji równorzędnej w tym samym regionie. Aby uzyskać więcej informacji [, zobacz ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Można użyć `curl` polecenie z opcją `-v`, aby rozwiązać problemy z łącznością z publicznymi lub prywatnymi punktami końcowymi przed użyciem z usługi Beeline.

---

### <a id="sparksql"></a>Użyj Z usługi Beeline z Apache Spark

Apache Spark udostępnia własną implementację serwera hiveserver2, która jest czasami określana jako serwer Spark Thrift. Ta usługa używa platformy Spark SQL do rozpoznawania zapytań zamiast Hive i może zapewnić lepszą wydajność w zależności od zapytania.

#### <a name="through-public-or-private-endpoints"></a>Za poorednictwem publicznych lub prywatnych punktów końcowych

Użyte parametry połączenia są nieco inne. Zamiast zawiera `httpPath=/hive2` `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

lub dla prywatnego punktu końcowego:

```bash 
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zastąp `<username>` kontem logowania klastra dla klastra. Uwaga w przypadku klastrów ESP Użyj pełnej nazwy UPN (np. user@domain.com). Zastąp `password` hasłem dla konta logowania klastra.

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych komunikacji równorzędnej w tym samym regionie. Aby uzyskać więcej informacji [, zobacz ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Można użyć `curl` polecenie z opcją `-v`, aby rozwiązać problemy z łącznością z publicznymi lub prywatnymi punktami końcowymi przed użyciem z usługi Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z poziomu węzła klastra lub wewnątrz Virtual Network platformy Azure z Apache Spark

W przypadku łączenia się bezpośrednio z węzłem głównym klastra lub z zasobu znajdującego się w ramach tego samego Virtual Network platformy Azure jako klastra usługi HDInsight należy używać `10002` portów zamiast `10001`. Poniższy przykład pokazuje, jak połączyć się bezpośrednio z węzłem głównym:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Wymagania wstępne

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Zwróć uwagę na [schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastra. Na przykład `wasb://` dla usługi Azure Storage, `abfs://` dla Azure Data Lake Storage Gen2 lub `adl://` dla Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI jest `wasbs://`. Aby uzyskać więcej informacji, zobacz [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Opcja 1: klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). W większości kroków przedstawionych w tym dokumencie przyjęto założenie, że używasz Z usługi Beeline z sesji SSH z klastrem.

* Opcja 2: lokalny klient Z usługi Beeline.

## <a id="beeline"></a>Uruchamianie zapytania programu Hive

Ten przykład jest oparty na użyciu klienta Z usługi Beeline z połączenia SSH.

1. Otwórz połączenie SSH z klastrem przy użyciu poniższego kodu. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Po wyświetleniu monitu wprowadź hasło dla konta użytkownika SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby nawiązać połączenie z usługą serwera hiveserver2 z Twoim klientem Z usługi Beeline, wprowadź następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Polecenia z usługi Beeline rozpoczynają się od znaku `!`, na przykład `!help` wyświetla pomoc. Jednakże `!` można pominąć dla niektórych poleceń. Na przykład `help` działa również.

    `!sql`, który jest używany do wykonywania instrukcji HiveQL. HiveQL jest jednak często używany, aby pominąć poprzednią `!sql`. Następujące dwie instrukcje są równoważne:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    W nowym klastrze jest wyświetlana tylko jedna tabela: **hivesampletable**.

4. Użyj następującego polecenia, aby wyświetlić schemat dla hivesampletable:

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

    Te informacje opisują kolumny w tabeli.

5. Wprowadź następujące instrukcje, aby utworzyć tabelę o nazwie **log4jLogs** przy użyciu przykładowych danych dostarczanych z klastrem usługi HDInsight: (Popraw je w zależności od [schematu identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme)).

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
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Te instrukcje wykonują następujące czynności:

    * `DROP TABLE` — Jeśli tabela istnieje, zostanie usunięta.

    * `CREATE EXTERNAL TABLE` — tworzy tabelę **zewnętrzną** w usłudze Hive. Tabele zewnętrzne przechowują wyłącznie definicję tabeli w programie Hive. Dane pozostaną w oryginalnej lokalizacji.

    * `ROW FORMAT` — jak są formatowane dane. W takim przypadku pola w każdym dzienniku są oddzielone spacją.

    * `STORED AS TEXTFILE LOCATION` — miejsce przechowywania danych i w jakim formacie pliku.

    * `SELECT` — wybiera liczbę wszystkich wierszy, w których kolumna **T4** zawiera wartość **[Error]** . To zapytanie zwraca wartość **3** , ponieważ istnieją trzy wiersze, które zawierają tę wartość.

    * `INPUT__FILE__NAME LIKE '%.log'`-Hive próbuje zastosować schemat do wszystkich plików w katalogu. W takim przypadku katalog zawiera pliki, które nie są zgodne ze schematem. Aby zapobiec utracie danych bezużytecznych w wynikach, ta instrukcja informuje gałąź, że powinna zwracać tylko dane z plików kończących się na. log.

   > [!NOTE]  
   > Tabele zewnętrzne powinny być używane, gdy oczekuje się, że dane podstawowe mają być aktualizowane przez zewnętrzne źródło. Na przykład proces automatycznego przekazywania danych lub operacja MapReduce.
   >
   > Porzucenie tabeli **zewnętrznej nie powoduje usunięcia danych** , tylko definicji tabeli.

    Dane wyjściowe tego polecenia są podobne do następującego tekstu:

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

6. Aby wyjść z Z usługi Beeline, użyj `!exit`.

## <a id="file"></a>Uruchamianie pliku HiveQL

Jest to kontynuacja z poprzedniego przykładu. Wykonaj następujące kroki, aby utworzyć plik, a następnie uruchom go za pomocą Z usługi Beeline.

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **Query. HQL**:

    ```bash
    nano query.hql
    ```

2. Użyj następującego tekstu jako zawartości pliku. To zapytanie tworzy nową tabelę "wewnętrzną" o nazwie **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje wykonują następujące czynności:

   * **CREATE TABLE jeśli nie istnieje** — Jeśli tabela jeszcze nie istnieje, zostanie utworzona. Ponieważ **zewnętrzne** słowo kluczowe nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Tabele wewnętrzne są przechowywane w magazynie danych programu Hive i są zarządzane całkowicie przez program Hive.
   * **Przechowywane jako Orc** — dane są przechowywane w formacie zoptymalizowanego wiersza kolumnowy (Orc). Format ORC to wysoce zoptymalizowany i wydajny format służący do przechowywania danych programu Hive.
   * **Wstaw zastąpienie... SELECT** -wybiera wiersze z tabeli **log4jLogs** zawierającej wartość **[Error]** , a następnie wstawia dane do tabeli **errorLogs** .

    > [!NOTE]  
    > W przeciwieństwie do tabel zewnętrznych, porzucanie tabeli wewnętrznej również usuwa dane źródłowe.

3. Aby zapisać plik, użyj **klawisza Ctrl**+ **_X**, a następnie wprowadź **Y**, a na koniec **wprowadź**.

4. Użyj następującego polecenia, aby uruchomić plik za pomocą Z usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametr `-i` uruchamia Z usługi Beeline i uruchamia instrukcje w pliku `query.hql`. Po zakończeniu zapytania zostanie wyświetlony monit `jdbc:hive2://headnodehost:10001/>`. Można również uruchomić plik za pomocą parametru `-f`, który kończy Z usługi Beeline po zakończeniu zapytania.

5. Aby sprawdzić, czy tabela **errorLogs** została utworzona, użyj następującej instrukcji, aby zwrócić wszystkie wiersze z **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Należy zwrócić trzy wiersze danych, a wszystkie zawierające **[Error]** w kolumnie T4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="summary"></a><a id="nextsteps"></a>Następne kroki

Aby uzyskać więcej ogólnych informacji na temat platformy Hive w usłudze HDInsight, zobacz następujący dokument:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać więcej informacji na temat innych sposobów pracy z usługą Hadoop w usłudze HDInsight, zobacz następujące dokumenty:

* [Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
