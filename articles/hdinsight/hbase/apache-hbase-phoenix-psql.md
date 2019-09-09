---
title: Ładowanie zbiorcze do Apache Phoenix przy użyciu PSQL — Azure HDInsight
description: Ładowanie zbiorczych danych ładowania do tabel Apache Phoenix w usłudze Azure HDInsight za pomocą narzędzia PSQL
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 15bb65e004de916862297f91278328cddb16487d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810417"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Zbiorcze ładowanie danych do oprogramowania Apache Phoenix za pomocą programu psql

[Apache Phoenix](https://phoenix.apache.org/) to "open source", wysoce równoległa relacyjna baza danych oparta na platformie [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix udostępnia zapytania podobne do języka SQL w programie HBase. Phoenix korzysta ze sterowników JDBC, aby umożliwić użytkownikom tworzenie, usuwanie i modyfikowanie tabel SQL, indeksów, widoków i sekwencji oraz wierszy upsert pojedynczo i zbiorczo. Phoenix używa kompilacji natywnej noSQL zamiast używania MapReduce do kompilowania zapytań, aby utworzyć aplikacje o niskim opóźnieniu na HBase. Phoenix dodaje współprocesory obsługujące uruchamianie kodu dostarczonego przez klienta w przestrzeni adresowej serwera, wykonując kod umieszczony wspólnie z danymi. To minimalizuje transfer danych klienta/serwera.  Aby korzystać z danych przy użyciu platformy Phoenix w usłudze HDInsight, najpierw Utwórz tabele, a następnie Załaduj do nich dane.

## <a name="bulk-loading-with-apache-phoenix"></a>Ładowanie zbiorcze z Apache Phoenix

Istnieje wiele sposobów na uzyskanie danych do HBase, w tym Używanie interfejsów API klienta, zadania MapReduce z TableOutputFormat lub wprowadzanie danych ręcznie przy użyciu powłoki HBase. Phoenix oferuje dwie metody ładowania danych CSV do tabel w Phoenix: narzędzia ładowania klienta o nazwie `psql`i narzędzia ładowania zbiorczego opartego na MapReduce.

`psql` Narzędzie jest jednowątkowe i najlepiej nadaje się do ładowania megabajtów lub gigabajtów danych. Wszystkie pliki CSV do załadowania muszą mieć rozszerzenie pliku CSV.  Możesz również określić pliki skryptów SQL w `psql` wierszu polecenia z rozszerzeniem pliku ". SQL".

Ładowanie zbiorcze z MapReduce jest używane dla znacznie większych woluminów danych, zwykle w scenariuszach produkcyjnych, ponieważ MapReduce używa wielu wątków.

Przed rozpoczęciem ładowania danych upewnij się, że Phoenix jest włączona i że ustawienia limitu czasu zapytania są zgodnie z oczekiwaniami.  Uzyskaj dostęp do pulpitu nawigacyjnego [Apache Ambari](https://ambari.apache.org/) klastra usługi HDInsight, wybierz pozycję HBase, a następnie kartę Konfiguracja.  Przewiń w dół, aby sprawdzić, czy Apache Phoenix `enabled` jest ustawiona na tak, jak pokazano:

![Apache Phoenix ustawień klastra usługi HDInsight](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Użyj `psql` do ładowania zbiorczego tabel

1. Utwórz nową tabelę, a następnie Zapisz zapytanie z nazwą pliku `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Skopiuj plik CSV (pokazany przykładowo zawartość) `customers.csv` `/tmp/` do katalogu w celu załadowania do nowo utworzonej tabeli.  Użyj polecenia `hdfs` , aby skopiować plik CSV do żądanej lokalizacji źródłowej.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Utwórz zapytanie SELECT SQL, aby sprawdzić, czy dane wejściowe zostały załadowane prawidłowo, a następnie Zapisz `listCustomers.sql`zapytanie z nazwą pliku. Możesz użyć dowolnego zapytania SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Ładuj zbiorczo dane, otwierając *nowe* okno polecenia usługi Hadoop. Najpierw przejdź do lokalizacji katalogu wykonywania za pomocą `cd` polecenia, a następnie `psql` Użyj narzędzia (polecenie języka Python `psql.py` ). 

    Poniższy przykład oczekuje, że `customers.csv` plik jest kopiowany z konta magazynu do lokalnego katalogu tymczasowego przy użyciu opcji `hdfs` w kroku 2 powyżej.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Aby określić `ZookeeperQuorum` nazwę, zlokalizuj [Apache ZooKeeper](https://zookeeper.apache.org/) ciągu kworum w pliku `/etc/hbase/conf/hbase-site.xml` o nazwie `hbase.zookeeper.quorum`właściwości.

5. Po zakończeniu `psql` operacji w oknie poleceń powinien zostać wyświetlony komunikat:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Ładowanie tabel przy użyciu MapReduce

Aby obciążać obciążenie wyższego poziomu przepływności za pośrednictwem klastra, użyj narzędzia ładowania MapReduce. Ten moduł ładujący najpierw konwertuje wszystkie dane do HFiles, a następnie tworzy HFiles do HBase.

1. Uruchom moduł ładujący MapReduce woluminów CSV przy `hadoop` użyciu polecenia z JAR klienta w Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Utwórz nową tabelę za pomocą instrukcji SQL, tak jak `CreateCustomersTable.sql` w poprzednim kroku 1.

3. Aby sprawdzić schemat tabeli, uruchom `!describe inputTable`polecenie.

4. Określ ścieżkę lokalizacji do danych wejściowych, na przykład przykładowy `customers.csv` plik. Pliki wejściowe mogą znajdować się na koncie magazynu WASB/ADLS. W tym przykładowym scenariuszu pliki wejściowe znajdują się `<storage account parent>/inputFolderBulkLoad` w katalogu.

5. Przejdź do katalogu wykonywania polecenia MapReduce zbiorczego ładowania:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Znajdź wartość w `/etc/hbase/conf/hbase-site.xml`, z nazwą `hbase.zookeeper.quorum`właściwości. `ZookeeperQuorum`

7. Skonfiguruj ścieżkę klasową i uruchom `CsvBulkLoadTool` polecenie Narzędzia:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Aby użyć MapReduce z Azure Data Lake Storage, Zlokalizuj Data Lake Storage katalog główny, który jest `hbase.rootdir` wartością w. `hbase-site.xml` W poniższym poleceniu Data Lake Storage katalog `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`główny. W tym poleceniu Określ Data Lake Storage wejściowe i wyjściowe folderów jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Zalecenia

* Użyj tego samego nośnika magazynu dla folderów wejściowych i wyjściowych, usługi Azure Storage (WASB) lub Azure Data Lake Storage (ADL). Aby przenieść dane z usługi Azure Storage do Data Lake Storage, można użyć `distcp` polecenia:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Używaj węzłów procesu roboczego o większym rozmiarze. Procesy mapy kopii zbiorczej MapReduce tworzą duże ilości danych wyjściowych, które wypełniają dostępne miejsce inne niż systemu plików DFS. W przypadku dużej ilości ładowania zbiorczego należy użyć więcej i większych węzłów procesów roboczych. Liczba węzłów procesu roboczego przydzielenia do klastra wpływa bezpośrednio na szybkość przetwarzania.

* Podziel pliki wejściowe na segmenty ~ 10 GB. Ładowanie zbiorcze jest operacją intensywnie korzystającą z magazynu, więc dzielenie plików wejściowych na wiele fragmentów skutkuje lepszą wydajnością.

* Unikaj hotspotów serwera regionów. Jeśli klucz wiersza jest monotonicznie rosnący, HBase sekwencyjne zapisy mogą wywołać serwer regionu hotspotting. *Solenie* klucza wiersza zmniejsza sekwencyjne zapisywanie. Phoenix zapewnia sposób nieprzezroczystego posolenia klucza wiersza z bajtem soli dla określonej tabeli, jak opisano poniżej.

## <a name="next-steps"></a>Następne kroki

* [Ładowanie danych zbiorczych za pomocą Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Korzystanie z Apache Phoenix w przypadku klastrów Apache HBase opartych na systemie Linux w usłudze HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Tabele solone](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramatyki](https://phoenix.apache.org/language/index.html)
