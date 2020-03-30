---
title: Masowe ładowanie do Apache Phoenix przy użyciu psql - Azure HDInsight
description: Za pomocą narzędzia psql można załadować dane obciążenia zbiorczego do tabel Apache Phoenix w usłudze Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552614"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Zbiorcze ładowanie danych do oprogramowania Apache Phoenix za pomocą programu psql

[Apache Phoenix](https://phoenix.apache.org/) to otwarta, masowo równoległa relacyjnej bazy danych zbudowanej na [Apache HBase.](../hbase/apache-hbase-overview.md) Phoenix udostępnia zapytania podobne do SQL za pośrednictwem bazy HBase. Phoenix używa sterowników JDBC, aby umożliwić użytkownikom tworzenie, usuwanie i zmienianie tabel SQL, indeksów, widoków i sekwencji oraz wierszy upsert indywidualnie i zbiorczo. Phoenix używa kompilacji natywnej noSQL, a nie przy użyciu MapReduce do kompilowania zapytań, do tworzenia aplikacji o małym opóźnieniu na górze HBase. Phoenix dodaje koprocesory do obsługi uruchomionego kodu dostarczonego przez klienta w przestrzeni adresowej serwera, wykonując kod współlokowany z danymi. Minimalizuje to transfer danych klient/serwer.  Aby pracować z danymi przy użyciu phoenix w hdinsight, najpierw utwórz tabele, a następnie załaduj do nich dane.

## <a name="bulk-loading-with-apache-phoenix"></a>Ładowanie masowe z Apache Phoenix

Istnieje wiele sposobów, aby uzyskać dane do HBase, w tym przy użyciu interfejsów API klienta, MapReduce zadanie z TableOutputFormat lub wprowadzania danych ręcznie przy użyciu powłoki HBase. Phoenix udostępnia dwie metody ładowania danych CSV do tabel `psql`Phoenix: narzędzie ładowania klienta o nazwie i narzędzie do ładowania zbiorczego oparte na mapreduce.

Narzędzie `psql` jest jednowątkowe i najlepiej nadaje się do ładowania megabajtów lub gigabajtów danych. Wszystkie pliki CSV do załadowania muszą mieć rozszerzenie pliku '.csv'.  Można również określić pliki `psql` skryptów SQL w wierszu polecenia z rozszerzeniem pliku '.sql'.

Ładowanie zbiorcze z MapReduce jest używany dla znacznie większych ilości danych, zazwyczaj w scenariuszach produkcyjnych, jak MapReduce używa wielu wątków.

Przed rozpoczęciem ładowania danych sprawdź, czy phoenix jest włączony i że ustawienia limitu czasu kwerendy są zgodnie z oczekiwaniami.  Uzyskaj dostęp do pulpitu nawigacyjnego [apache ambari](https://ambari.apache.org/) klastra HDInsight, wybierz pozycję HBase, a następnie kartę Konfiguracja.  Przewiń w dół, aby sprawdzić, czy Apache Phoenix jest ustawiona na tak, `enabled` jak pokazano na rysunku:

![Ustawienia klastra Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Służy `psql` do zbiorczego ładowania tabel

1. Utwórz plik `createCustomersTable.sql`o nazwie i skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Utwórz plik `listCustomers.sql`o nazwie i skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```sql
    SELECT * from Customers;
    ```

1. Utwórz plik `customers.csv`o nazwie i skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Utwórz plik `customers2.csv`o nazwie i skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Otwórz wiersz polecenia i zmień katalog na lokalizację nowo utworzonych plików. Zamień clustername poniżej z rzeczywistą nazwą klastra HBase. Następnie wykonaj kod, aby przekazać pliki do węzła główny klastra:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z sesji ssh zmień katalog na lokalizację narzędzia **psql.** Wykonaj poniższe polecenie:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Zbiorcze ładowanie danych. Poniższy kod spowoduje utworzenie tabeli **Klienci,** a następnie przekazanie danych.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Po `psql` zakończeniu operacji powinien zostać wyświetlony komunikat podobny w następujący sposób:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Można nadal używać `psql` do wyświetlania zawartości tabeli Klienci. Wykonaj poniższy kod:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Alternatywnie można użyć [powłoki HBase](./query-hbase-with-hbase-shell.md)lub [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) do wykonywania zapytań o dane.

1. Prześlij dodatkowe dane. Teraz, gdy tabela już istnieje, polecenie określa tabelę. Wykonaj poniższe polecenie:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Zbiorcze ładowanie tabel za pomocą mapReduce

W przypadku ładowania o wyższej przepływności dystrybuowane przez klaster, należy użyć MapReduce narzędzie do ładowania. Ten moduł ładujący najpierw konwertuje wszystkie dane na HFiles, a następnie udostępnia utworzone pliki HFiles do bazy HBase.

1. Ta sekcja jest kontynuowana z sesją ssh i obiektami utworzonymi wcześniej. Utwórz tabelę **Klienci** i plik **customers.csv** zgodnie z potrzebami, wykonując powyższe kroki. W razie potrzeby ponownie nałóż połączenie ssh.

1. Obcięcie zawartości tabeli **Klienci.** Z otwartej sesji ssh wykonaj poniższe polecenia:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Skopiuj `customers.csv` plik z nazwy headnode do usługi Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Zmień katalog wykonywania polecenia MapReduce obciążenie zbiorcze:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Uruchom moduł ładujący CSV MapReduce za pomocą `hadoop` polecenia z buforem klienta Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Po zakończeniu przesyłania powinien zostać wyświetlony komunikat podobny w następujący sposób:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Aby użyć mapReduce z usługą Azure Data Lake Storage, znajdź `hbase.rootdir` katalog `hbase-site.xml`główny magazynu usługi Data Lake Storage, który jest wartością w . W poniższym poleceniu katalog główny `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`magazynu usługi Data Lake storage jest . W tym poleceniu określ foldery wejściowe i wyjściowe magazynu usługi Data Lake jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Aby zbadać i wyświetlić dane, można użyć **psql,** jak opisano wcześniej. Można również użyć [powłoki HBase](./query-hbase-with-hbase-shell.md)lub [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Zalecenia

* Użyj tego samego nośnika magazynu dla folderów wejściowych i wyjściowych, usługi Azure Storage (WASB) lub Usługi Azure Data Lake Storage (ADL). Aby przenieść dane z usługi Azure Storage `distcp` do usługi Data Lake Storage, można użyć polecenia:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Użyj węzłów procesu roboczego o większym rozmiarze. Procesy mapowe kopii zbiorczej MapReduce wytwarzają duże ilości tymczasowych danych wyjściowych, które zapełniają dostępne miejsce inne niż DFS. W przypadku dużej ilości ładowania zbiorczego należy używać większej liczby węzłów procesu roboczego. Liczba węzłów procesu roboczego przydzielanych do klastra ma bezpośredni wpływ na szybkość przetwarzania.

* Podziel pliki wejściowe na ~10 GB fragmentów. Ładowanie zbiorcze jest operacją intensywnie korzystającą z magazynu, więc dzielenie plików wejściowych na wiele fragmentów powoduje lepszą wydajność.

* Unikaj hotspotów serwerów regionu. Jeśli klucz wiersza jest monotonicznie zwiększenie, HBase sekwencyjne zapisy mogą wywoływać hotspotting serwera regionu. *Solenie* klawisza wiersza zmniejsza zapisy sekwencyjne. Phoenix zapewnia sposób na przezroczyste sól klucz wiersza z bajtem solenia dla danej tabeli, jak przypomniano poniżej.

## <a name="next-steps"></a>Następne kroki

* [Masowe ładowanie danych z Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Użyj Apache Phoenix z klastrami Apache HBase opartymi na systemie Linux w funkcji HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Stoły solone](https://phoenix.apache.org/salted.html)
* [Apache Phoenix Gramatyka](https://phoenix.apache.org/language/index.html)
