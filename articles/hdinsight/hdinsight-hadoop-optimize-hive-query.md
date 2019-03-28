---
title: Optymalizowanie zapytań technologii Hive w usłudze Azure HDInsight
description: W tym artykule opisano, jak zoptymalizować swoje zapytania usługi Apache Hive dla usługi Hadoop w HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.openlocfilehash: 63def1464c3c219b5181a31bf19cc80a1e487025
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521741"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optymalizowanie zapytań technologii Hive w usłudze Azure HDInsight

W usłudze Azure HDInsight istnieje kilka typów klastrów i technologie, które można uruchomić zapytania usługi Apache Hive. Podczas tworzenia klastra usługi HDInsight, należy wybrać typ klastra odpowiednie do optymalizowania wydajności na potrzeby związane z obciążeniem.

Na przykład wybrać **Interactive Query** typ zoptymalizowane pod kątem zapytań ad-hoc, interaktywne klastra. Wybierz pozycję Apache **Hadoop** typ zoptymalizowane pod kątem zapytań programu Hive, używane jako proces wsadowy klastra. **Platforma Spark** i **HBase** typy klastrów można również uruchomić zapytania programu Hive. Aby uzyskać więcej informacji na temat uruchamiania zapytań programu Hive na różne typy klastrów HDInsight, zobacz [co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hadoop/hdinsight-use-hive.md).

Klastry HDInsight typu klastra Hadoop nie są zoptymalizowane pod kątem wydajności domyślnie. W tym artykule opisano niektóre z najczęściej stosowanych do zapytań metody optymalizacji wydajności technologii Hive.

## <a name="scale-out-worker-nodes"></a>Skalowanie węzłów procesu roboczego

Zwiększenie liczby węzłów procesu roboczego w klastrze usługi HDInsight umożliwia pracę do większej liczby maperów i reduktorów do równoległego uruchamiania. Istnieją dwa sposoby, można zwiększyć, skalowanie w poziomie w HDInsight:

* W tym czasie, podczas tworzenia klastra można określić liczbę węzłów procesu roboczego przy użyciu witryny Azure portal, programu Azure PowerShell lub interfejsu wiersza polecenia.  Więcej informacji można znaleźć w artykule [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight). Poniższy zrzut ekranu przedstawia proces roboczy konfiguracji węzła w witrynie Azure portal:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
    
* Po utworzeniu można również edytować liczbę węzłów procesu roboczego do skalowania w poziomie dalsze klastra bez konieczności ponownego tworzenia jeden:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Aby uzyskać więcej informacji na temat skalowania HDInsight, zobacz [klastrów HDInsight skalowania](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Używanie Apache Tez zamiast Map Reduce

[Apache Tez](https://hortonworks.com/hadoop/tez/) to aparat wykonywania alternatywne do aparatu MapReduce. Opartą na systemie Linux klastrów HDInsight mają domyślnie włączony w aplikacji Tez.

![tez_1][image-hdi-optimize-hive-tez_1]

Tez jest szybsze, ponieważ:

* **Wykonaj kierowane acykliczne wykresu (DAG) jako pojedyncze zadanie w aparacie MapReduce**. DAG wymaga każdy zestaw liczby maperów stosowaną przez jeden zestaw reduktorów. Powoduje to, że wiele zadań MapReduce zostać rozszerzona dla każdego zapytania programu Hive. Tez nie ma takiego ograniczenia i może przetwarzać złożonych DAG jako jedno zadanie, w związku z tym dodatkowe obciążenie uruchamiania zadania.
* **Pozwala uniknąć niepotrzebnych zapisów**. Wiele zadań będą używani do przetwarzania tego samego zapytania programu Hive w aparacie MapReduce. Dane wyjściowe każdego zadania MapReduce są zapisywane w systemie plików hdfs dla danych pośrednich. Ponieważ Tez minimalizuje liczbę zadań dla każdego zapytania programu Hive, jest w stanie uniknąć niepotrzebnych zapisu.
* **Minimalizuje opóźnienia rozruchu**. Tez lepiej jest w stanie zminimalizować opóźnienie uruchamiania zmniejszenie liczby liczby maperów, których potrzebuje, aby rozpocząć, a także ulepszanie optymalizacji w całej.
* **Ponownie używa kontenerów**. Zawsze, gdy Tez możliwe jest w stanie to ponowne użycie kontenerów, aby upewnić się, czy jest mniejsze opóźnienie z powodu uruchamiania kontenerów.
* **Techniki optymalizacji ciągłe**. Tradycyjnie optymalizacji została wykonana w fazie kompilacji. Jednak więcej informacji na temat danych wejściowych jest dostępny umożliwiające celu uzyskania lepszej optymalizacji w czasie wykonywania. Tez wykorzystuje techniki optymalizacji ciągłym, w celu optymalizacji plan dalsze w fazie środowiska uruchomieniowego.

Aby uzyskać więcej informacji dotyczących tych pojęć, zobacz [Apache TEZ](https://hortonworks.com/hadoop/tez/).

Możesz wykonać dowolne zapytanie Hive Tez włączone przez dodanie przedrostka zapytania za pomocą następującego polecenia zestawu:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Hive, partycjonowanie

Operacje We/Wy są główne wąskie gardło do uruchamiania zapytań programu Hive. Wydajność można poprawić, jeśli można zmniejszyć ilość danych, który ma być odczytywane. Domyślnie zapytania programu Hive skanowanie całych tabel programu Hive. Jednak dla zapytań, którzy muszą tylko skanowanie niewielką ilość danych (na przykład zapytania przy użyciu filtrowania), to zachowanie tworzy niepotrzebne obciążenie. Partycjonowanie gałęzi umożliwia zapytania Hive w celu dostępu do niezbędnej ilości danych tabel programu Hive.

Partycjonowanie hive jest implementowany przez reorganizowanie danych pierwotnych do nowych katalogów. Każda partycja ma swój własny katalog pliku. Podział jest zdefiniowany przez użytkownika. Na poniższym diagramie przedstawiono podział na partycje według kolumny tabeli programu Hive *roku*. Nowy katalog jest tworzony dla każdego roku.

![Hive, partycjonowanie][image-hdi-optimize-hive-partitioning_1]

Niektóre kwestie partycjonowania:

* **Czy nie ma pod partycji** -partycjonowania w kolumnach za pomocą tylko kilku wartości może spowodować, że kilka partycji. Na przykład partycjonowania na płeć tworzy tylko dwie partycje można utworzyć (głosu męskiego i żeńskiego), w związku z tym tylko zredukowaniem opóźnień, maksymalnie o połowę.
* **Nie przez partycję** — na y. Tworzenie partycji kolumn przy użyciu unikatowej wartości (na przykład nazwa użytkownika) powoduje, że wiele partycji. Za pośrednictwem partycji powoduje, że wiele obciążenia na namenode klastra nie pozostał do obsługi dużej liczby katalogów.
* **Należy unikać niesymetryczność danych** — należy uważnie wybierać klucz partycji, aby wszystkie partycje mają rozmiar nieparzystych. Na przykład partycjonowania na *stanu* kolumny może spowodować przesunięcie rozkład danych. Ponieważ stan Kalifornia populacji prawie 30 x, że z Vermont, rozmiar partycji jest potencjalnie niesymetryczna i wydajność będzie zależeć znacznie.

Aby utworzyć tabelę partycji, użyj *podzielona na partycje według* klauzuli:

   ```hive
   CREATE TABLE lineitem_part
       (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING, 
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
   STORED AS TEXTFILE;
   ```
   
Po utworzeniu tabeli partycjonowanej, możesz utworzyć partycjonowanie statyczne lub dynamiczne partycjonowanie.

* **Partycjonowania statycznego** oznacza, że masz już podzielonej na fragmenty dane w odpowiednich katalogów. Przy użyciu statycznych partycji możesz dodać partycje Hive ręcznie oparte na lokalizację katalogu. Przykładem jest poniższy fragment kodu.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **Dynamiczne partycjonowanie** oznacza, że ma gałęzi, aby automatycznie utworzyć partycji dla Ciebie. Ponieważ utworzono już partycjonowania tabeli z tabeli przejściowej, jest wszystko, co należy zrobić, wstawiania danych do tabeli partycjonowanej:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
Aby uzyskać więcej informacji, zobacz [partycjonowane tabele](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Użycie formatu ORCFile
Hive obsługuje różne formaty plików. Na przykład:

* **Tekst**: domyślny format pliku i sprawdza się w przypadku większości scenariuszy.
* **Avro**: działa dobrze sprawdza się w scenariuszach współpracy.
* **ORC/Parquet**: najbardziej odpowiednie dla wydajności.

Format ORC (zoptymalizowane pod kątem wiersz kolumnowy) jest bardzo wydajny sposób przechowywania danych programu Hive. W porównaniu do innych formatów, ORC ma następujące zalety:

* Obsługa złożonych typów, w tym daty/godziny i typów złożonych i o połowicznej strukturze.
* nawet o 70% kompresji.
* indeksuje co 10 000 wierszy, które pozwalają na pomijanie wierszy.
* znaczny spadek wykonania.

Aby włączyć formacie ORC, należy najpierw utworzyć tabelę z klauzulą *przechowywane jako ORC*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
Następnie możesz wstawić dane do tabeli ORC z tabeli tymczasowej. Na przykład:

   ```hive
   INSERT INTO TABLE lineitem_orc
   SELECT L_ORDERKEY as L_ORDERKEY, 
          L_PARTKEY as L_PARTKEY , 
          L_SUPPKEY as L_SUPPKEY,
          L_LINENUMBER as L_LINENUMBER,
          L_QUANTITY as L_QUANTITY, 
          L_EXTENDEDPRICE as L_EXTENDEDPRICE,
          L_DISCOUNT as L_DISCOUNT,
          L_TAX as L_TAX,
          L_RETURNFLAG as L_RETURNFLAG,
          L_LINESTATUS as L_LINESTATUS,
          L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;
   ```
   
Możesz dowiedzieć się więcej na temat formatu ORC w [ręczne Apache Hive języka](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Wektoryzacja

Wektoryzacji umożliwia Hive do przetwarzania partii wierszy 1024 zamiast przetwarzania jeden wiersz w danym momencie. Oznacza to, że proste operacje są wykonywane szybciej ponieważ mniejszej ilości kodu wewnętrznego, musi zostać uruchomiony.

Aby włączyć wektoryzacji prefiks zapytania Hive z następującymi ustawieniami:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

Aby uzyskać więcej informacji, zobacz [Wektoryzowana wykonywania zapytania](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Inne metody optymalizacji
Istnieją więcej metod optymalizacji, które należy rozważyć, na przykład:

* **Hive, obsługą zasobników:** technika, która pozwala na klastrze lub segmentu dużych zestawów danych w celu zoptymalizowania wydajności zapytania.
* **Dołącz do optymalizacji:** optymalizacji wykonanie zapytania programu Hive w planowanie zwiększyć wydajność sprzężenia i zmniejszyć zapotrzebowanie na wskazówki użytkownika. Aby uzyskać więcej informacji, zobacz [Dołącz do optymalizacji](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zwiększ reduktorów**.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają przedstawiono kilka typowych metod optymalizacji zapytań Hive. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Use Apache Hive w HDInsight](hadoop/hdinsight-use-hive.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu technologii Hive w HDInsight](hdinsight-analyze-flight-delay-data-linux.md)
* [Analizowanie danych serwisu Twitter przy użyciu technologii Hive w HDInsight](hdinsight-analyze-twitter-data-linux.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
