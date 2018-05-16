---
title: Ładowanie do Apache Phoenix przy użyciu psql - Azure HDInsight zbiorczego | Dokumentacja firmy Microsoft
description: Narzędzie psql załadować do tabel Phoenix danych ładowania zbiorczego.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 54d3b7ae22162d84ef6287945d4ff95fa1274874
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Zbiorcze ładowanie danych do oprogramowania Phoenix za pomocą programu psql

[Apache Phoenix](http://phoenix.apache.org/) jest otwartym przetwarzania równoległego relacyjnej bazy danych, zbudowana na [HBase](../hbase/apache-hbase-overview.md). Phoenix zapewnia przypominającego SQL zapytań za pośrednictwem HBase. Phoenix używa JDBC sterowniki, aby umożliwić użytkownikom na tworzenie, usuwanie i alter SQL tabel, indeksów, widoków i sekwencji i upsert wierszy indywidualnie i zbiorczo. Phoenix używa natywnej kompilacji noSQL, zamiast używać MapReduce skompilować zapytania, aby tworzyć aplikacje małe opóźnienia w bazie danych HBase. Phoenix dodaje wspólnej procesorów do obsługi uruchamiania kodu dostarczonych przez klienta w przestrzeni adresowej serwera, wykonywanie kodu wspólnie z danymi. Pozwala to zmniejszyć transferu danych klienta i serwera.  Aby pracować z danymi w usłudze HDInsight przy użyciu Phoenix, należy najpierw utworzyć tabel, a następnie ładowanie danych do ich.

## <a name="bulk-loading-with-phoenix"></a>Zbiorcze ładowanie z Phoenix

Istnieje wiele sposobów pobierania danych do bazy danych HBase w tym za pomocą interfejsów API, zadania MapReduce z TableOutputFormat, klienta lub wprowadzanie danych ręcznie przy użyciu powłoki HBase. Phoenix udostępnia dwie metody ładowania danych CSV do tabel Phoenix: klient ładowania narzędzia o nazwie `psql`, narzędzie obciążenia opartych na MapReduce zbiorcze i.

`psql` Narzędzie jest jednowątkowy i jest najodpowiedniejsze do użytku podczas ładowania w megabajtach lub gigabajtach danych. Wszystkie pliki CSV można załadować musi mieć rozszerzenie "CSV".  Można również określić pliki skryptów SQL w `psql` wiersza polecenia z rozszerzeniem "SQL".

Zbiorcze ładowanie z MapReduce służy do znacznie większych woluminów danych, zwykle w scenariuszach produkcyjnych, jak MapReduce używa wielu wątków.

Przed rozpoczęciem ładowania danych, sprawdź, czy Phoenix jest włączona oraz czy ustawienia limitu czasu zapytania są zgodnie z oczekiwaniami.  Dostęp do pulpitu nawigacyjnego Ambari klastra usługi HDInsight, wybierz bazy danych HBase, a następnie na karcie Konfiguracja.  Przewiń w dół do Sprawdź, czy wartość jest Apache Phoenix `enabled` pokazany:

![Apache Phoenix klastra usługi HDInsight ustawienia](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Użyj `psql` do tabel ładowania zbiorczego

1. Utwórz nową tabelę, a następnie Zapisz zapytanie z nazwą pliku `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Skopiuj plik CSV (zawartość przykładzie pokazano) jako `customers.csv` do `/tmp/` katalogu ładowania do nowo utworzonej tabeli.  Użyj `hdfs` polecenie, aby skopiować plik CSV do lokalizacji żądanego źródła.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Utwórz kwerendę SQL SELECT, sprawdź dane wejściowe załadowane poprawnie, a następnie zapisać kwerendę o nazwie pliku `listCustomers.sql`. Można użyć dowolnego zapytania SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Zbiorcze ładowania danych przez otwarcie *nowe* okno polecenia Hadoop. Najpierw zmień lokalizację katalogu wykonywania z `cd` polecenia, a następnie użyj `psql` narzędzia (Python `psql.py` polecenia). 

    Poniższy przykład oczekuje, że skopiowano `customers.csv` plik z konta magazynu przy użyciu lokalnego katalogu tymczasowego `hdfs` jak kroku 2. powyżej.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Aby określić `ZookeeperQuorum` name, odszukaj ciąg kworum dozorcy w pliku `/etc/hbase/conf/hbase-site.xml` o nazwie właściwości `hbase.zookeeper.quorum`.

5. Po `psql` operacji zostało zakończone, w oknie polecenia powinien zostać wyświetlony komunikat:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Korzystać z usługi MapReduce z tabelami ładowania zbiorczego

Do załadowania wyższej przepustowości rozłożone w klastrze, należy użyć narzędzia obciążenia MapReduce. Ten moduł ładujący najpierw konwertuje wszystkie dane na HFiles, a następnie umożliwia utworzony HFiles do bazy danych HBase.

1. Uruchom program ładujący CSV MapReduce przy użyciu `hadoop` polecenia Phoenix JAR klienta:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Utwórz nową tabelę za pomocą instrukcji SQL, jak `CreateCustomersTable.sql` w poprzednim kroku 1.

3. Aby zweryfikować schemat tabeli, uruchom `!describe inputTable`.

4. Ścieżka lokalizacji do wprowadzania danych, takich jak na przykład określić `customers.csv` pliku. Pliki wejściowe może być na koncie magazynu WASB/ADLS. W tym przykładowym scenariuszu plików wejściowych znajdują się w `<storage account parent>/inputFolderBulkLoad` katalogu.

5. Przejdź do katalogu wykonanie polecenia ładowania zbiorczego MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Znajdź użytkownika `ZookeeperQuorum` wartość w `/etc/hbase/conf/hbase-site.xml`, o nazwie właściwości `hbase.zookeeper.quorum`.

7. Ustawianie ścieżki klasy i uruchom `CsvBulkLoadTool` narzędzia polecenia:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Aby korzystać z usługi MapReduce z ADLS, zlokalizuj ADLS katalogu głównego, który jest `hbase.rootdir` wartość w `hbase-site.xml`. W poniższym poleceniu jest katalogiem głównym ADLS `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. W tym poleceniu Określ ADLS danych wejściowych i wyjściowych folderów jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Zalecenia

* Użyj tego samego nośnika magazynowania dla wejścia i wyjścia folderów WASB lub ADLS. Aby przenieść dane z WASB do ADLS, można użyć `distcp` polecenia:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Użyj węzłów procesu roboczego większy rozmiar. Procesy mapy kopiowania zbiorczego MapReduce utworzyć dużych ilości tymczasowe dane wyjściowe, które zapełnić miejsce dostępne z systemem innym niż systemu plików DFS. W przypadku dużą ilość ładowania zbiorczego Użyj węzłów procesu roboczego więcej i większy rozmiar. Liczba węzłów procesu roboczego, który przydzielić do klastra bezpośrednio wpływa na szybkość przetwarzania.

* Podziel wejściowych plików na fragmenty ~ 10 GB. Ładowanie zbiorcze jest operacją intensywnie magazynu, więc Podziel pliki danych wejściowych na wiele wyników fragmentów lepszą wydajność.

* Unikaj hotspotami serwera regionu. Jeśli klucz wiersza monotonicznie wzrasta, sekwencyjnych zapisów HBase może wywoływać hotspotting serwera regionu. *Solenie* zmniejsza klucza wiersza zapisu sekwencyjnego. Phoenix zapewnia sposób przezroczysty soli klucz wiersza z solenie bajtów dla danej tabeli, zgodnie z informacjami zawartymi poniżej.

## <a name="next-steps"></a>Kolejne kroki

* [Zbiorcze ładowanie danych ze Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Apache Phoenix za pomocą klastrów HBase opartych na systemie Linux w usłudze HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Solone tabel](https://phoenix.apache.org/salted.html)
* [Gramatyka Phoenix](http://phoenix.apache.org/language/index.html)
