---
title: Odczytywanie i zapisywanie danych HBase — Azure HDInsight za pomocą platformy Spark
description: Korzystanie z łącznika bazy danych HBase Spark, aby odczytywać i zapisywać dane z klastra Spark do klastra HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 200691f7af16e82d554d0e1e019b6a4e5c75949f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484942"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark

Bazy danych Apache HBase jest zazwyczaj badane za pomocą jego niskiego poziomu interfejsu API (skanowania, pobiera i umieszcza) lub przy użyciu składni SQL, przy użyciu rozwiązania Apache Phoenix. Apache także Apache HBase łącznika usługi Spark, który jest wygodny sposób i wydajne alternatywne, zapytania i modyfikację danych przechowywanych przez bazę danych HBase.

## <a name="prerequisites"></a>Wymagania wstępne

* Dwa osobne klastry HDInsight, jednej bazy danych HBase i Spark jednym z co najmniej Spark 2.1 (HDInsight 3.6) zainstalowane.
* Klaster Spark musi komunikować się bezpośrednio z klastra HBase z minimalnym opóźnieniem, więc to zalecana konfiguracja jest wdrożenie zarówno klastrów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schemat identyfikatora URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) do obsługi klastrów magazynu podstawowego. Takie rozwiązanie byłoby wasb: / / usługi Azure Blob Storage, abfs: / / dla usługi Azure Data Lake Storage Gen2 lub systemu plików adl: / / dla usługi Azure Data Lake Storage Gen1. Bezpieczny transfer jest włączona dla usługi Blob Storage lub Data Lake Storage Gen2, identyfikator URI będzie mieć wasbs: / / lub abfss: / / odpowiednio Zobacz też [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).


## <a name="overall-process"></a>Ogólny proces

Ogólny proces włączania klastra Spark do wykonywania zapytań klastra usługi HDInsight jest następująca:

1. Przygotuj przykładowe dane w bazie danych HBase.
2. Uzyskiwanie pliku bazy danych hbase-site.xml, z folderu konfiguracji klastra HBase (/ etc/hbase/conf).
3. Umieść kopię bazy danych hbase-site.xml, w folderze Konfiguracja Spark 2 (/ conf/spark2/itp.).
4. Uruchom `spark-shell` odwołuje się do łącznika usługi Spark HBase przez jego Maven koordynuje w `packages` opcji.
5. Zdefiniuj katalogu, który mapuje schematu z platformy Spark do bazy danych HBase.
6. Wchodzić w interakcje z danymi bazy danych HBase przy użyciu RDD lub interfejsów API ramki danych.

## <a name="prepare-sample-data-in-apache-hbase"></a>Przygotowanie przykładowych danych w bazy danych Apache HBase

W tym kroku możesz utworzyć i wypełnić prostej tabeli w bazie danych HBase Apache, który można następnie wykonywać zapytania za pomocą platformy Spark.

1. Łączenie z węzłem głównym klastra HBase przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu protokołu SSH HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).  Edytować poniższe polecenie, zastępując `HBASECLUSTER` nazwą klastra usługi HBase `sshuser` przy użyciu protokołu ssh użytkownika nazwa konta, a następnie wprowadź polecenie.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Wprowadź poniższe polecenie, aby uruchomić z poziomu powłoki HBase:

        hbase shell

3. Wprowadź poniższe polecenie, aby utworzyć `Contacts` tabelę z rodziny kolumn `Personal` i `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Wpisz poniższe polecenia, aby załadować kilka wierszy przykładowych danych:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. Wprowadź poniższe polecenie, aby wyjść z poziomu powłoki HBase:

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiowanie bazy danych hbase-site.xml do klastra Spark
Kopiowanie bazy danych hbase-site.xml z magazynu lokalnego do katalogu głównego magazynu domyślnego klastra Spark.  Edytować poniższe polecenie, aby odpowiadały konfiguracji.  Następnie z otwartych sesji SSH z klastrem HBase, wpisz polecenie:

| Wartość składni | Nowa wartość|
|---|---|
|[Schemat identyfikatora URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modyfikacja w celu odzwierciedlenia magazynu.  Poniższej składni jest dla usługi blob storage z włączonym bezpiecznym transferem.|
|`SPARK_STORAGE_CONTAINER`|Zamień na nazwę domyślnego kontenera magazynu używane dla klastra Spark.|
|`SPARK_STORAGE_ACCOUNT`|Zamień domyślna nazwa konta magazynu używane dla klastra Spark.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umieść site.xml bazy danych hbase w klastrze Spark

1. Łączenie z węzłem głównym klastra Spark przy użyciu protokołu SSH.

2. Wprowadź poniższe polecenie, aby skopiować `hbase-site.xml` z klastrem Spark domyślnego magazynu do folderu konfiguracji Spark 2 klastra lokalnego magazynu:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Uruchom powłokę aparatu Spark odwołujące się do łącznika usługi Spark bazy danych HBase

1. Z otwartego sesji SSH do klastra Spark wprowadź poniższe polecenie, aby uruchomić powłoki spark:

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Nie zamykaj tego wystąpienia powłoki usługi Spark, a następnie przejdź do następnego kroku.

## <a name="define-a-catalog-and-query"></a>Zdefiniuj wykazu i zapytań

W tym kroku zdefiniujesz obiektu katalogu, który mapuje schemat bazy danych Apache HBase z platformy Apache Spark.  

1. W powłoce platformy Spark otwarte, wprowadź następujące `import` instrukcji:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Wprowadź poniższe polecenie, aby określić katalog do tabeli kontaktów utworzonego w bazie danych HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Kod wykonuje następujące działania:  

     a. Zdefiniować schemat tabeli HBase, o nazwie katalogu `Contacts`.  
     b. Identyfikowanie rowkey jako `key`i mapowania nazw kolumn użytych w Spark do rodziny kolumn, nazwa kolumny i typ kolumny, ponieważ używany w bazie danych HBase.  
     c. Rowkey również musi być zdefiniowany szczegółowo jako kolumna o nazwie (`rowkey`), który ma rodziny kolumn określonych `cf` z `rowkey`.  

3. Wprowadź poniższe polecenie, aby zdefiniować metodę, która zapewnia wokół ramkę danych Twojej `Contacts` tabeli w bazie danych HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Utwórz wystąpienie obiektu ramki danych:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Zapytanie ramki danych:

    ```scala
    df.show()
    ```

6. Powinny zostać wyświetlone dwa wiersze danych:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Zarejestruj tabeli tymczasowej, dzięki czemu można tworzyć zapytania w tabeli HBase przy użyciu modułu Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Zapytanie SQL do wystawiania `contacts` tabeli:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Powinny zostać wyświetlone wyniki, takie jak te:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Wstaw nowe dane

1. Aby wstawić nowy rekord kontaktu, zdefiniuj `ContactRecord` klasy:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Utwórz wystąpienie obiektu `ContactRecord` i umieść je w tablicy:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Zapisz tablicy nowe dane do bazy danych HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Sprawdź wyniki:

    ```scala  
    df.show()
    ```

5. Powinny pojawić się następujące dane wyjściowe:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Kolejne kroki

* [Apache Spark łącznika bazy danych HBase](https://github.com/hortonworks-spark/shc)
