---
title: Z usługi Beeline Apache za pomocą programu Apache Hive — usługa Azure HDInsight
description: Dowiedz się, jak używać klienta z usługi Beeline do uruchamiania zapytań Hive z usługą Hadoop w HDInsight. Z usługi beeline to narzędzie do pracy przy użyciu serwera HiveServer2 za pośrednictwem JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056864"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Hive za pomocą klienta programu Apache z usługi Beeline

Dowiedz się, jak używać [Apache z usługi Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) do uruchamiania zapytań Apache Hive na HDInsight.

Z usługi beeline jest klientem programu Hive, który znajduje się na węzłów głównych klastra usługi HDInsight. Z usługi beeline używa sterownika JDBC, aby nawiązać połączenie serwera HiveServer2, usługa hostowana w klastrze usługi HDInsight. Można również korzystanie z usługi Beeline, dostęp do programu Hive na HDInsight zdalnie za pośrednictwem Internetu. Poniższe przykłady zawierają najbardziej typowe parametry połączenia używane do nawiązywania HDInsight z z usługi Beeline:

## <a name="types-of-connections"></a>Typy połączeń

### <a name="from-an-ssh-session"></a>W sesji SSH

Podczas łączenia się z sesji SSH z węzłem głównym klastra, można połączyć do `headnodehost` adres na porcie `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Za pośrednictwem sieci wirtualnej platformy Azure

Podczas nawiązywania połączenia z klientem HDInsight za pośrednictwem usługi Azure Virtual Network, należy podać w pełni kwalifikowana nazwa domeny (FQDN) węzła głównego klastra. Ponieważ to połączenie jest nawiązywane bezpośrednio do węzłów klastra, połączenie korzysta z portu `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Zastąp `<headnode-FQDN>` z w pełni kwalifikowaną nazwę domeny z węzłem głównym klastra. Aby znaleźć nazwę FQDN węzła głównego, skorzystaj z informacji w [Zarządzanie HDInsight przy użyciu interfejsu API Apache Ambari REST](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentu.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Do klastra usługi HDInsight Enterprise Security pakietu (ESP)

Gdy połączenie od klienta do klastra pakietu zabezpieczeń przedsiębiorstwa (ESP) przyłączone do usługi Azure Active Directory (AAD), należy także określić nazwę domeny `<AAD-Domain>` i nazwy konta użytkownika domeny z uprawnieniami dostępu do klastra `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Zastąp `<username>` o nazwie konta domeny z uprawnieniami dostępu do klastra. Zastąp `<AAD-DOMAIN>` o nazwie z usługi Azure Active Directory (AAD) dołączonego do klastra. Użyj wielkimi literami ciągu dla `<AAD-DOMAIN>` wartość, w przeciwnym razie nie można odnaleźć poświadczeń. Sprawdź `/etc/krb5.conf` obszaru nazw, w razie potrzeby.

---

### <a name="over-public-internet"></a>Za pośrednictwem publicznej sieci internet

Podczas nawiązywania połączenia za pośrednictwem publicznej sieci internet, należy podać nazwę konta logowania klastra (domyślny `admin`) i hasło. Na przykład za pomocą z usługi Beeline z komputera klienckiego nawiązać `<clustername>.azurehdinsight.net` adresu. To połączenie jest nawiązywane za pośrednictwem portu `443`i jest szyfrowany przy użyciu protokołu SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zastąp `admin` przy użyciu konta logowania klastra dla klastra. Zastąp `password` hasłem do konta logowania klastra.

---

### <a id="sparksql"></a>Korzystanie z usługi Beeline z platformą Apache Spark

Platforma Apache Spark udostępnia własną implementację serwera HiveServer2, która czasami jest nazywany serwerem Spark Thrift. Ta usługa korzysta z programu Spark SQL do rozwiązywania kwerend, a nie gałęzi i może zapewnić lepszą wydajność w zależności od Twojego zapytania.

#### <a name="over-public-internet-with-apache-spark"></a>Za pośrednictwem publicznej sieci internet z platformą Apache Spark

Parametry połączenia używane podczas nawiązywania połączenia przez internet jest nieco inne. Zamiast `httpPath=/hive2` jest `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z klastra głównego lub do wewnątrz sieci wirtualnej platformy Azure z platformą Apache Spark

Po nawiązaniu połączenia bezpośrednio z węzła głównego klastra lub zasobów w tej samej sieci wirtualnej platformy Azure, co klaster HDInsight portu `10002` powinna być używana dla platformy Spark Thrift server zamiast `10001`. Poniższy przykład pokazuje, jak połączyć się bezpośrednio z węzłem głównym:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Wymagania wstępne

* Klaster Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Zwróć uwagę [schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla klastra magazynu głównego. Na przykład `wasb://` dla usługi Azure Storage `abfs://` dla usługi Azure Data Lake Storage Gen2 lub `adl://` dla usługi Azure Data Lake Storage Gen1. Jeśli bezpiecznego transferu jest włączona dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI jest `wasbs://` lub `abfss://`, odpowiednio. Aby uzyskać więcej informacji, zobacz [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).


* Opcja 1: Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Większość czynności w tym dokumencie przyjęto założenie, że używasz z usługi Beeline z sesji SSH do klastra.

* Opcja 2:  Na lokalnym kliencie z usługi Beeline.


## <a id="beeline"></a>Uruchomienie zapytania programu Hive

Ten przykład jest oparty na temat korzystania z poziomu klienta z usługi Beeline połączenie SSH.

1. Otwórz połączenie SSH z klastrem przy użyciu poniższego kodu. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Po wyświetleniu monitu wprowadź hasło dla konta użytkownika SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Łączenie z usługi HiveServer2 przy użyciu klienta z usługi Beeline z otwartych sesji SSH, wprowadzając następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Z usługi beeline polecenia zaczynają się od `!` znak, na przykład `!help` Wyświetla Pomoc. Jednak `!` można pominąć w przypadku niektórych poleceń. Na przykład `help` działa również.

    Brak `!sql`, która jest używana do wykonywania instrukcje HiveQL. Jednak HiveQL używa się tak najczęściej, można pominąć w poprzednim `!sql`. Następujące dwie instrukcje są równoważne:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    W nowym klastrze, znajduje się tylko jedna tabela: **hivesampletable**.

4. Aby wyświetlić schemat tabeli hivesampletable, użyj następującego polecenia:

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

5. Wprowadź poniższe instrukcje, aby utworzyć tabelę o nazwie **log4jLogs** przy użyciu dane przykładowe z klastrem usługi HDInsight: (Na podstawie Popraw stosownie do potrzeb Twojej [schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Te instrukcje, wykonaj następujące czynności:

    * `DROP TABLE` -Jeśli tabela istnieje, zostanie usunięta.

    * `CREATE EXTERNAL TABLE` — Stwarza **zewnętrznych** tabeli programu Hive. Tabele zewnętrzne przechowywać wyłącznie w definicji tabeli programu Hive. Dane pozostaną w oryginalnej lokalizacji.

    * `ROW FORMAT` — W jaki sposób dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.

    * `STORED AS TEXTFILE LOCATION` — W przypadku gdy dane są przechowywane i w jakim formacie pliku.

    * `SELECT` -Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. To zapytanie zwraca wartość **3** , jak istnieją trzy wiersze, które zawierają tę wartość.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive podejmie próbę zastosowania schematu do wszystkich plików w katalogu. W tym przypadku katalog zawiera pliki, które nie pasuje do schematu. Aby zapobiec odzyskiwanie danych w wynikach, ta instrukcja każe gałąź, czy tylko powinna zwrócić dane z plików kończy się rozszerzeniem. log.

   > [!NOTE]  
   > Jeśli potrzebujesz danych bazowych do zaktualizowania za pomocą zewnętrznego źródła, należy używać tabel zewnętrznych. Na przykład proces przekazywania danych lub operacji MapReduce.
   >
   > Usunięcie tabeli zewnętrznej jest **nie** usunąć dane w definicji tabeli.

    Dane wyjściowe tego polecenia będą podobne do następującego tekstu:

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

6. Aby wyjść z usługi Beeline, należy użyć `!exit`.

## <a id="file"></a>Uruchom plik HiveQL

Jest to kontynuacja z poprzedniego przykładu. Wykonaj następujące kroki, aby utworzyć plik, a następnie uruchomić go za pomocą z usługi Beeline.

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **query.hql**:

    ```bash
    nano query.hql
    ```

2. Skorzystaj z poniższego tekstu jako zawartość pliku. To zapytanie tworzy nową tabelę "internal" o nazwie **przesłano**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje, wykonaj następujące czynności:

   * **Tworzenie tabeli IF NOT EXISTS** — Jeśli tabela już istnieje, zostanie utworzony. Ponieważ **zewnętrznych** — słowo kluczowe nie jest używany, ta instrukcja tworzy wewnętrznej tabeli. Tabele wewnętrzne są przechowywane w magazynie danych programu Hive i są całkowicie zarządzane przez program Hive.
   * **PRZECHOWYWANE ORC AS** — przechowuje dane w formacie zoptymalizowane pod kątem wiersz kolumnowych (ORC). ORC format jest wysoce zoptymalizowane i wydajne formatu do przechowywania danych programu Hive.
   * **ZASTĄP INSERT... Wybierz** -wybiera wiersze z **log4jLogs** tabeli, która zawiera **[Błąd]**, następnie wstawia dane do **przesłano** tabeli.

    > [!NOTE]  
    > W przeciwieństwie do tabel zewnętrznych porzucenie wewnętrznej tabeli powoduje usunięcie danych bazowych.

3. Aby zapisać plik, użyj **Ctrl**+**_X**, wprowadź **Y**, a na koniec **Enter**.

4. Aby uruchomić plik, korzystając z usługi Beeline, należy użyć następującego:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` Parametru z usługi Beeline jest uruchomiona i instrukcje `query.hql` pliku. Po wykonaniu zapytania przyjeździe do biura `jdbc:hive2://headnodehost:10001/>` wiersza. Można również uruchomić plik przy użyciu `-f` parametr, który kończy działanie z usługi Beeline, po wykonaniu kwerendy.

5. Aby sprawdzić, czy **przesłano** tabela została utworzona, poniższa instrukcja umożliwia zwracanie wszystkich wierszy z **przesłano**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Trzy wiersze danych ma zostać zwrócone, zawierający wszystkie **[Błąd]** w kolumnie t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Następne kroki

Aby uzyskać bardziej ogólne informacje na temat programu Hive w HDInsight zobacz następujący dokument:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)

Aby uzyskać więcej informacji na temat innych sposobów, że możesz pracować z platformą Hadoop w HDInsight zobacz następujące dokumenty:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
