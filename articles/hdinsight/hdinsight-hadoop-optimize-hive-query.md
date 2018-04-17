---
title: Optymalizacja zapytań programu Hive w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zoptymalizować zapytania Hive dla platformy Hadoop w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 8fc4a1c1759ad857502a540d6c3be11b0f607c79
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Optymalizacja zapytań programu Hive w usłudze Azure HDInsight

Domyślnie klastry Hadoop nie są zoptymalizowane pod kątem wydajności. W tym artykule omówiono niektóre najczęściej stosowane do zapytania metody optymalizacji wydajności Hive.

## <a name="scale-out-worker-nodes"></a>Skalowanie w poziomie węzłów procesu roboczego

Zwiększenie liczby węzłów procesu roboczego w klastrze, można wykorzystać więcej mapowań i reduktory do uruchomienia równoległego. Istnieją dwa sposoby może zwiększyć skalowania w poziomie w usłudze HDInsight:

* Podczas udostępniania można określić liczbę węzłów procesu roboczego przy użyciu portalu Azure, programu Azure PowerShell lub interfejsu wiersza polecenia i platform.  Więcej informacji można znaleźć w artykule [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów usługi HDInsight). Poniższy zrzut ekranu przedstawia proces roboczy konfiguracji węzła w portalu Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* W czasie wykonywania użytkownik może również skalować klastra bez konieczności ponownego tworzenia jedną:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Aby uzyskać więcej informacji o różnych maszyn wirtualnych obsługiwanych przez usługi HDInsight, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Włączanie aplikacji Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) jest aparatowi wykonywania alternatywnych aparat MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez przebiega szybciej, ponieważ:

* **Wykonanie skierowane acykliczne Graph (DAG) jako pojedyncze zadanie w aparacie MapReduce**. DAG wymaga każdy zestaw mapowań przez jeden zestaw reduktory. Powoduje to wiele zadań MapReduce być wykonana dla każdego zapytania Hive. Tez nie ma takiego ograniczenia i może przetwarzać DAG złożonego jako jedno zadanie w związku z tym zminimalizować koszty uruchomienia zadania.
* **Pozwala uniknąć niepotrzebnego zapisy**. Ze względu na wiele zadań jest wykonana dla tego samego zapytania Hive w aparacie MapReduce dane wyjściowe każdego zadania są zapisywane w systemie plików HDFS dla pośrednich danych. Ponieważ Tez minimalizuje liczbę zadań dla każdego zapytania Hive jest w stanie uniknąć niepotrzebnych zapisu.
* **Minimalizuje opóźnień uruchamiania**. Tez lepiej jest w stanie zminimalizować opóźnienie rozpoczęcia zmniejszenie liczby mapowań niezbędne do uruchomienia, a także zwiększanie optymalizacji w całej.
* **Ponownie używa kontenerów**. Po każdej zmianie Tez możliwe jest ponowne użycie kontenerów, aby upewnić się, zmniejsza opóźnienia z powodu uruchamiania kontenerów.
* **Techniki optymalizacji ciągłego**. Tradycyjnie optymalizacji została wykonana podczas fazy kompilacji. Więcej informacji na temat danych wejściowych jest dostępne umożliwiające lepsze optymalizacji w czasie wykonywania. Tez używa techniki optymalizacji ciągłego, umożliwiających Optymalizowanie planu dalsze w fazie środowiska wykonawczego.

Aby uzyskać więcej informacji dotyczących tych pojęć, zobacz [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Można wprowadzić żadnych zapytań Hive Tez włączane przez prefiksu zapytanie z poniższych ustawień:

    set hive.execution.engine=tez;

Klastry usługi HDInsight opartej na systemie Linux ma domyślnie włączona w aplikacji Tez.


## <a name="hive-partitioning"></a>Gałąź rejestru partycjonowania

Operacja We/Wy jest głównych wąskie gardło do uruchamiania zapytań Hive. Wydajność można poprawić, jeśli można zmniejszyć ilość danych, który musi być do odczytu. Domyślnie zapytań programu Hive skanowania całego tabele programu Hive. Jest to doskonałe rozwiązanie dla zapytania, takie jak skanowanie tabeli. Jednak dla zapytań, które należy tylko skanowanie niewielką ilość danych (na przykład zapytania z filtrowania), to zachowanie tworzy niepotrzebne obciążenie. Partycjonowanie hive umożliwia zapytań programu Hive dostępu niezbędnej ilości danych w tabelach gałęzi do.

Partycjonowanie hive jest implementowany przez reorganizacja nieprzetworzone dane do nowych katalogów z każdej partycji o własny katalog — gdy partycja jest zdefiniowany przez użytkownika. Na poniższym diagramie przedstawiono partycjonowania tabeli programu Hive w kolumnie *roku*. Nowy katalog jest tworzony dla każdego roku.

![Podział na partycje][image-hdi-optimize-hive-partitioning_1]

Niektóre kwestie partycjonowania:

* **Czy nie w partycji** -partycjonowania dla kolumn mających tylko kilka wartości może spowodować kilka partycji. Na przykład partycjonowania na płci tylko tworzy dwie partycje można utworzyć (męskiego i żeńskiego), w związku z tym tylko zmniejszyć opóźnienia maksymalnie o połowę.
* **Nie przez partycję** — na y. utworzeniu partycji dla kolumny zawierającej unikatowe wartości (na przykład nazwa użytkownika) powoduje, że wiele partycji. W partycji powoduje dużo akcent namenode klastra, musi do obsługi dużej liczby katalogów.
* **Unikaj danych pochylenia** -rozsądny sposób wybrać opcję kluczem partycjonowania, tak aby wszystkie partycje są nawet rozmiar. Przykładem jest partycjonowania na *stanu* może spowodować to liczba rekordów w obszarze Kalifornijskiej być prawie 30 x z Vermont z powodu różnic w populacji.

Aby utworzyć tabelę partycji, użyj *podzielona na partycje według* klauzuli:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Po utworzeniu tabeli partycjonowanej, możesz utworzyć partycjonowania statyczne lub dynamiczne partycjonowanie.

* **Partycjonowania statycznego** oznacza, że masz już podzielonej danych w odpowiednich katalogów i poproś partycje Hive ręcznie oparte na lokalizację katalogu. Poniższy fragment kodu jest wartością przykładową.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Dynamiczne partycjonowanie** oznacza, że chcesz gałąź, aby automatycznie utworzyć partycji dla Ciebie. Ponieważ został już utworzony partycjonowania tabeli z tabeli tymczasowej, konieczne będzie wstawiania danych do tabeli partycjonowanej:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Aby uzyskać więcej informacji, zobacz [partycjonowane tabele](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Użyj formatu ORCFile
Hive obsługuje różnych formatach plików. Na przykład:

* **Tekst**: to jest domyślny format pliku i współdziała z większości scenariuszy
* **Avro**: sprawdza się w przypadku scenariuszy współdziałanie
* **ORC/Parquet**: najbardziej odpowiednie dla wydajności

Format ORC (zoptymalizowanych pod kątem wiersza kolumnowy) jest bardzo wydajny sposób przechowywania danych Hive. W porównaniu do innych formatów ORC ma następujące zalety:

* Obsługa typów złożonych tym DateTime i typy złożone i częściowo ustrukturyzowanych
* maksymalnie 70% kompresji
* indeksy co 10 000 wierszy, co umożliwia pomijanie wierszy
* znaczny spadek wykonywania środowiska wykonawczego

Aby włączyć ORC format, należy najpierw utworzyć tabelę z klauzulą *przechowywane jako ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Następnie możesz wstawiania danych do tabeli ORC z tabeli tymczasowej. Na przykład:

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

Więcej na temat formatu ORC [tutaj](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization umożliwia Hive można przetworzyć partii wierszy 1024 razem zamiast przetwarzania jeden wiersz jednocześnie. Oznacza to, że proste operacje są wykonywane szybciej ponieważ mniej wewnętrzny kod musi być uruchamiane.

Aby włączyć vectorization prefiksu zapytanie Hive z następującymi ustawieniami:

    set hive.vectorized.execution.enabled = true;

Aby uzyskać więcej informacji, zobacz [Przekształcono wykonywania zapytania](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Inne metody optymalizacji
Dostępnych jest więcej metod optymalizacji można rozważyć, na przykład:

* **Gałąź rejestru bucketing:** technika, która umożliwia klastra lub segment dużych zestawów danych w celu zoptymalizowania wydajności zapytania.
* **Dołącz do optymalizacji:** optymalizacji wykonywania zapytania gałęzi planowania podnieść wydajność sprzężenia i zmniejsza potrzebę wskazówek użytkownika. Aby uzyskać więcej informacji, zobacz [Join optymalizacji](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zwiększ reduktory**.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule uzyskanych kilka typowych metody optymalizacji zapytań Hive. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Use Apache Hive w usłudze HDInsight](hadoop/hdinsight-use-hive.md)
* [Analizowanie danych opóźnienie transmitowane przy użyciu usługi Hive w usłudze HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analizowanie danych Twitter przy użyciu Hive w usłudze HDInsight](hdinsight-analyze-twitter-data.md)
* [Analizowanie danych czujnika przy użyciu konsoli zapytania Hive na platformie Hadoop w usłudze HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Korzystanie z programu Hive z usługą HDInsight do analizy dzienników witryn sieci Web](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
