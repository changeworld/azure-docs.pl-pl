---
title: Zbiorcze ładowanie do rozwiązania Apache Phoenix za pomocą narzędzia psql — Azure HDInsight
description: Ładowanie zbiorcze ładowanie danych do tabel Phoenix narzędzie psql.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: fe6705dc3dedd521357f924dd433c7eacf88ba84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718630"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Zbiorcze ładowanie danych do oprogramowania Apache Phoenix za pomocą programu psql

[Apache Phoenix](https://phoenix.apache.org/) typu open source, równoległe na wielką skalę relacyjnej bazy danych w oparciu o [bazy danych Apache HBase](../hbase/apache-hbase-overview.md). Phoenix udostępnia zapytań przypominający SQL, za pośrednictwem HBase. Phoenix używa sterowników JDBC, aby umożliwić użytkownikom tworzenie, usuwanie i alter SQL tabel, indeksów, widoków i sekwencje i upsert wierszy indywidualnie i zbiorczo. Phoenix używa noSQL natywnej kompilacji, zamiast używać MapReduce można skompilować zapytania, aby tworzyć aplikacje o małych opóźnieniach w bazie danych HBase. Phoenix dodaje wspólnej procesorów, które umożliwiają uruchamianie kodu dostarczane przez klienta w przestrzeni adresowej serwera, wykonywanie kodu, wspólnie z danymi. W ten sposób przeniesienia danych klienta/serwera.  Aby pracować z danymi w HDInsight przy użyciu Phoenix, najpierw utwórz tabele, a następnie Załaduj dane do nich.

## <a name="bulk-loading-with-apache-phoenix"></a>Zbiorcze ładowanie przy użyciu rozwiązania Apache Phoenix

Istnieje wiele sposobów, aby pobierać dane do bazy danych HBase, w tym za pomocą interfejsów API, zadanie MapReduce z TableOutputFormat, klienta lub wprowadzanie danych ręcznie za pomocą powłoki HBase. Phoenix udostępnia dwie metody ładowania danych z pliku CSV do tabel Phoenix: klienta podczas ładowania narzędzia o nazwie `psql`oraz narzędzie ładowania zbiorczego opartych na technologii MapReduce.

`psql` Narzędzie jest jednowątkowym i najlepiej nadaje się do ładowania w megabajtach lub gigabajtach danych. Wszystkie pliki CSV do załadowania musi mieć rozszerzenie "CSV".  Można również określić pliki skryptów SQL w `psql` wiersza polecenia z rozszerzeniem pliku "SQL".

Zbiorcze ładowanie za pomocą technologii MapReduce jest używany z dużo większe woluminy danych, zazwyczaj w scenariuszach produkcyjnych, jak MapReduce korzysta z wielu wątków.

Przed rozpoczęciem ładowania danych, sprawdź, czy włączono Phoenix i czy ustawienia limitu czasu zapytania zgodnie z oczekiwaniami.  Dostęp do klastra usługi HDInsight [Apache Ambari](https://ambari.apache.org/) pulpitu nawigacyjnego, wybierz opcję bazy danych HBase, a następnie na karcie Konfiguracja.  Przewiń w dół, aby sprawdzić, czy Apache Phoenix ma ustawioną `enabled` jak pokazano:

![Ustawienia klastra HDInsight programu Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Użyj `psql` zbiorcze ładowanie tabel

1. Utwórz nową tabelę, a następnie Zapisz zapytanie o nazwie pliku `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Skopiuj plik CSV (zawartość przykładzie pokazano) jako `customers.csv` do `/tmp/` katalogu do załadowania do nowo utworzonej tabeli.  Użyj `hdfs` polecenie, aby skopiować plik CSV do lokalizacji żądanego źródła.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Utwórz zapytanie SQL ZAZNACZYĆ, aby sprawdzić, dane wejściowe załadowany poprawnie, a następnie zapisać kwerendę o nazwie pliku `listCustomers.sql`. Możesz użyć dowolnego zapytania SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Zbiorcze ładowanie danych, otwierając *nowe* okna poleceń platformy Hadoop. Najpierw zmień na lokalizację katalogu wykonywania za pomocą `cd` polecenia, a następnie użyj `psql` narzędzia (Python `psql.py` polecenie). 

    Poniższy przykład oczekuje, że skopiowano `customers.csv` plików z konta magazynu z wykorzystaniem lokalnym katalogu tymczasowym `hdfs` jak w kroku 2 powyżej.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Aby określić `ZookeeperQuorum` nazwy, Znajdź [Apache ZooKeeper](https://zookeeper.apache.org/) kworum w ciągu w pliku `/etc/hbase/conf/hbase-site.xml` z nazwą właściwości `hbase.zookeeper.quorum`.

5. Po `psql` operacja została ukończona, powinien zostać wyświetlony komunikat w oknie polecenia:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Korzystanie z technologii MapReduce z tabelami ładowania zbiorczego

Do załadowania większą przepływność rozłożone w klastrze, należy użyć narzędzia obciążenia MapReduce. Ten moduł ładujący HFiles najpierw konwertuje wszystkie dane, a następnie zapewnia HFiles utworzonej bazy danych HBase.

1. Uruchom moduł ładujący CSV MapReduce za pomocą `hadoop` polecenia Phoenix JAR klienta:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Utwórz nową tabelę przy użyciu instrukcji SQL, podobnie jak w przypadku `CreateCustomersTable.sql` w poprzednim kroku 1.

3. Aby sprawdzić, czy schemat tabeli, uruchom `!describe inputTable`.

4. Ścieżki lokalizacji, do swoich danych wejściowych, takiego jak przykładowy `customers.csv` pliku. Pliki wejściowe może być na tym koncie magazynu WASB/ADLS. W tym przykładowym scenariuszu pliki wejściowe znajdują się w `<storage account parent>/inputFolderBulkLoad` katalogu.

5. Zmień katalog wykonanie polecenia ładowania zbiorczego MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Zlokalizuj swoje `ZookeeperQuorum` wartość w `/etc/hbase/conf/hbase-site.xml`, z nazwą właściwości `hbase.zookeeper.quorum`.

7. Konfigurowanie ścieżki klasy i uruchom `CsvBulkLoadTool` narzędzia polecenia:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Aby używanie technologii MapReduce z usługi Azure Data Lake Storage, zlokalizuj katalog główny usługi Data Lake Storage, która jest `hbase.rootdir` wartość w `hbase-site.xml`. W poniższym poleceniu w katalogu głównym usługi Data Lake Storage jest `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. W tym poleceniu Określ dane wejściowe usługi Data Lake Storage i danych wyjściowych, foldery jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Zalecenia

* Dla folderów wejściowe i wyjściowe, Azure Storage (WASB) lub Azure Data Lake Storage (ADL), należy użyć tego samego nośnika magazynowania. Aby przesyłanie danych z usługi Azure Storage do usługi Data Lake Storage, możesz użyć `distcp` polecenia:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Użyj węzłów procesu roboczego większy rozmiar. Procesy mapy kopiowania masowego MapReduce produktu dużych ilości tymczasowe dane wyjściowe, które wypełniają miejsce dostępne bez systemu plików DFS. W przypadku dużej ilości ładowania zbiorczego Użyj więcej i większy rozmiar węzłów procesu roboczego. Liczba węzłów procesu roboczego, którą należy przydzielić programowi klastra bezpośrednio wpływa na szybkość przetwarzania.

* Podziel plików wejściowych na fragmenty ~ 10 GB. Ładowanie zbiorcze jest operacją intensywnie korzystających z magazynu, dlatego rozdzielenie plików wejściowych na wiele wyników fragmentów lepszą wydajność.

* Należy unikać obszarów aktywności serwera regionu. Jeśli klucz wiersza monotonicznie wzrasta, sekwencyjne zapisy bazy danych HBase może wywoływać hotspotting serwera regionu. *Solenie* klucz wiersza zmniejsza sekwencyjnych zapisów. Phoenix zapewnia sposób przezroczysty soli klucza wiersza z bajtem dodającego ciąg inicjujący dla konkretnej tabeli, jak to jest wskazane poniżej.

## <a name="next-steps"></a>Kolejne kroki

* [Zbiorcze ładowanie danych ze rozwiązania Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Użyj rozwiązania Apache Phoenix klastrów opartych na systemie Linux bazy danych Apache HBase na platformie HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Solone tabel](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramatyki](https://phoenix.apache.org/language/index.html)
