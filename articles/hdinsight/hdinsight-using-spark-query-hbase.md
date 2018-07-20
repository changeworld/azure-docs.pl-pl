---
title: Odczytywanie i zapisywanie danych HBase — Azure HDInsight za pomocą platformy Spark
description: Korzystanie z łącznika bazy danych HBase Spark, aby odczytywać i zapisywać dane z klastra Spark do klastra HBase.
services: hdinsight
author: maxluk
ms.author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 5123a95852fae58adf0b4a4684b012d3b9c71e3b
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144775"
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Odczytywanie i zapisywanie danych w bazie danych HBase za pomocą platformy Spark

Apache HBase zwykle zostaje przesłane zapytanie za pomocą jego niskiego poziomu interfejsu API (skanowania, pobiera i umieszcza) lub przy użyciu składni SQL za pomocą Phoenix. Apache także łącznika usługi Spark bazy danych HBase, który jest wygodny sposób i wydajne alternatywne, zapytania i modyfikację danych przechowywanych przez bazę danych HBase.

## <a name="prerequisites"></a>Wymagania wstępne

* Klastry HDInsight, jednej bazy danych HBase i Spark co dwa odrębne przy użyciu platformy Spark 2.1 (HDInsight 3.6) zainstalowane.
* Klaster Spark musi komunikować się bezpośrednio z klastra HBase z minimalnym opóźnieniem, więc to zalecana konfiguracja jest wdrożenie zarówno klastrów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* Dostęp SSH do każdego klastra.
* Dostęp do magazynu domyślnego każdego klastra.

## <a name="overall-process"></a>Ogólny proces

Ogólny proces włączania klastra Spark do wykonywania zapytań klastra usługi HDInsight jest następująca:

1. Przygotuj przykładowe dane w bazie danych HBase.
2. Uzyskiwanie pliku bazy danych hbase-site.xml, z folderu konfiguracji klastra HBase (/ etc/hbase/conf).
3. Umieść kopię bazy danych hbase-site.xml, w folderze Konfiguracja Spark 2 (/ conf/spark2/itp.).
4. Uruchom `spark-shell` odwołuje się do łącznika usługi Spark HBase przez jego Maven koordynuje w `packages` opcji.
5. Zdefiniuj katalogu, który mapuje schematu z platformy Spark do bazy danych HBase.
6. Wchodzić w interakcje z danymi bazy danych HBase przy użyciu RDD lub interfejsów API ramki danych.

## <a name="prepare-sample-data-in-hbase"></a>Przygotowanie przykładowe dane w bazie danych HBase

W tym kroku możesz utworzyć i wypełnić prostej tabeli w bazie danych HBase, który można następnie wykonywać zapytania za pomocą platformy Spark.

1. Łączenie z węzłem głównym klastra HBase przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu protokołu SSH HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Uruchom powłokę HBase:

        hbase shell

3. Tworzenie `Contacts` tabelę z rodziny kolumn `Personal` i `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Załaduj kilka wierszy przykładowych danych:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Uzyskiwanie hbase-site.xml, z klastra usługi HBase

1. Łączenie z węzłem głównym klastra HBase przy użyciu protokołu SSH.
2. Kopiowanie bazy danych hbase-site.xml z magazynu lokalnego do katalogu głównego domyślnym magazynem klastra usługi HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Przejdź do klastra HBase przy użyciu [witryny Azure portal](https://portal.azure.com).
4. Wybór kont magazynu. 

    ![Konta magazynu](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Wybierz konto magazynu na liście znajduje się znacznik wyboru w kolumnie domyślne.

    ![Domyślne konto magazynu](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. W okienku konta magazynu wybierz Kafelek obiektów blob.

    ![Kafelek obiektów blob](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Na liście kontenerów Wybierz kontener, który jest używany przez klaster HBase.
8. Na liście plików wybierz `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. Na panelu Właściwości obiektu Blob, wybierz pozycję Pobierz i Zapisz `hbase-site.xml` do lokalizacji na komputerze lokalnym.

    ![Do pobrania](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umieść site.xml bazy danych hbase w klastrze Spark

1. Przejdź do klastra Spark przy użyciu [witryny Azure portal](https://portal.azure.com).
2. Wybór kont magazynu.

    ![Konta magazynu](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Wybierz konto magazynu na liście znajduje się znacznik wyboru w kolumnie domyślne.

    ![Domyślne konto magazynu](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. W okienku konta magazynu wybierz Kafelek obiektów blob.

    ![Kafelek obiektów blob](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Na liście kontenerów Wybierz kontener, który jest używany przez Twój klaster Spark.
6. Wybieranie pozycji Przekaż.

    ![Upload](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Wybierz `hbase-site.xml` plików, które zostały wcześniej pobrane na komputer lokalny.

    ![Przekaż site.xml bazy danych hbase](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Wybieranie pozycji Przekaż.
9. Łączenie z węzłem głównym klastra Spark przy użyciu protokołu SSH.
10. Kopiuj `hbase-site.xml` z klastrem Spark domyślnego magazynu do folderu konfiguracji Spark 2 klastra lokalnego magazynu:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Uruchom powłokę aparatu Spark odwołujące się do łącznika usługi Spark bazy danych HBase

1. Łączenie z węzłem głównym klastra Spark przy użyciu protokołu SSH.
2. Uruchom powłokę programu platformy spark, określając pakietu łącznika usługi Spark bazy danych HBase:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/content/groups/public/

3. Nie zamykaj tego wystąpienia powłoki usługi Spark, a następnie przejdź do następnego kroku.

## <a name="define-a-catalog-and-query"></a>Zdefiniuj wykazu i zapytań

W tym kroku należy zdefiniować obiektu katalogu, który mapuje schemat bazy danych HBase z platformy Spark. 

1. Uruchom następujące polecenie w powłoce platformy Spark Otwórz `import` instrukcji:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Zdefiniuj katalogu do tabeli kontaktów, utworzone w bazie danych HBase:
    1. Zdefiniować schemat tabeli HBase, o nazwie katalogu `Contacts`.
    2. Identyfikowanie rowkey jako `key`i mapowania nazw kolumn użytych w Spark do rodziny kolumn, nazwa kolumny i typ kolumny, ponieważ używany w bazie danych HBase.
    3. Rowkey również musi być zdefiniowany szczegółowo jako kolumna o nazwie (`rowkey`), który ma rodziny kolumn określonych `cf` z `rowkey`.

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

3. Zdefiniowania metody, która zapewnia wokół ramkę danych Twojej `Contacts` tabeli w bazie danych HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Utwórz wystąpienie obiektu ramki danych:

        val df = withCatalog(catalog)

5. Zapytanie ramki danych:

        df.show()

6. Powinny zostać wyświetlone dwa wiersze danych:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Zarejestruj tabeli tymczasowej, dzięki czemu można tworzyć zapytania w tabeli HBase przy użyciu modułu Spark SQL:

        df.registerTempTable("contacts")

8. Zapytanie SQL do wystawiania `contacts` tabeli:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Powinny zostać wyświetlone wyniki, takie jak te:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Wstaw nowe dane

1. Aby wstawić nowy rekord kontaktu, zdefiniuj `ContactRecord` klasy:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Utwórz wystąpienie obiektu `ContactRecord` i umieść je w tablicy:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Zapisz tablicy nowe dane do bazy danych HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Sprawdź wyniki:
    
        df.show()

5. Powinny pojawić się następujące dane wyjściowe:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Kolejne kroki

* [Baza danych HBase łącznik Spark](https://github.com/hortonworks-spark/shc)
