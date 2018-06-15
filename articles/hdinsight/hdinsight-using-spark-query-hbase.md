---
title: Użyj do odczytywania i zapisywania danych HBase - Azure HDInsight Spark | Dokumentacja firmy Microsoft
description: Użyj konektora HBase Spark do odczytywania i zapisywania danych z klastra Spark klaster HBase.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 7cfc7f586e8a92c29736a7c4cff0b12796be430a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161179"
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Odczytywanie i zapisywanie danych w bazie danych HBase za pomocą platformy Spark

Bazy danych Apache HBase jest zwykle zbadać jego interfejs API niskiego poziomu (skanowania, pobiera i naraża) lub składni SQL, używając Phoenix. Apache także Spark HBase łącznik, który jest wygodny sposób i alternatywne wydajność zapytań i modyfikować dane przechowywane przez HBase.

## <a name="prerequisites"></a>Wymagania wstępne

* Dwa odrębne klastrów HDInsight, HBase jednego i co Spark przy użyciu 2.1 Spark (3,6 HDInsight) zainstalowany.
* Klaster Spark musi komunikować się bezpośrednio z klastra HBase z minimalnym opóźnieniem, aby zalecaną konfiguracją jest wdrażanie obu klastrów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight przy użyciu portalu Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* SSH dostęp do każdego klastra.
* Dostęp do magazynu domyślnego każdego klastra.

## <a name="overall-process"></a>Ogólny proces

Włączanie klaster Spark do zapytania z klastrem usługi HDInsight ogólny proces wygląda następująco:

1. Przygotuj przykładowych danych w bazie danych HBase.
2. Możliwość nabycia pliku bazy danych hbase-site.xml z folderu konfiguracji klastra HBase (/ etc/hbase/conf).
3. Umieść kopię bazy danych hbase-site.xml, w folderze konfiguracji Spark 2 (/ etc/spark2/conf).
4. Uruchom `spark-shell` odwołuje się do łącznika HBase Spark przez jego Maven koordynuje w `packages` opcji.
5. Zdefiniuj katalogu, który mapuje schemat bazy danych HBase z platformy Spark.
6. Interakcję z danymi HBase przy użyciu RDD lub DataFrame interfejsów API.

## <a name="prepare-sample-data-in-hbase"></a>Przygotowanie przykładowe dane w bazie danych HBase

W tym kroku możesz utworzyć i wypełnić prostą tabelę w bazie danych HBase, który można następnie wykonasz zapytania przy użyciu platformy Spark.

1. Połączenie z węzłem głównym klastra HBase przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Uruchom powłokę HBase:

        hbase shell

3. Utwórz `Contacts` tabeli z rodzinami kolumn `Personal` i `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Ładowanie kilka wierszy przykładowych danych:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Możliwość nabycia hbase-site.xml z klastra HBase

1. Połączenie z węzłem głównym klastra HBase przy użyciu protokołu SSH.
2. Skopiuj hbase-site.xml z magazynu lokalnego do katalogu głównego magazynu domyślne klaster HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Przejdź do używania klastra HBase [portalu Azure](https://portal.azure.com).
4. Wybierz konta magazynu. 

    ![Konta magazynu](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Wybierz konto magazynu na liście znajduje się znacznik wyboru w kolumnie domyślne.

    ![Domyślne konto magazynu](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. W okienku konta magazynu wybierz Kafelek obiektów blob.

    ![Obiekty BLOB kafelka](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Na liście kontenerów Wybierz kontener, który jest używany przez klaster HBase.
8. Na liście plików, wybierz `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. W panelu Właściwości obiektu Blob, wybierz opcję Pobierz i Zapisz `hbase-site.xml` do lokalizacji na komputerze lokalnym.

    ![Do pobrania](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Umieść hbase-site.xml w klastrze Spark

1. Przejdź do używania klastra Spark [portalu Azure](https://portal.azure.com).
2. Wybierz konta magazynu.

    ![Konta magazynu](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Wybierz konto magazynu na liście znajduje się znacznik wyboru w kolumnie domyślne.

    ![Domyślne konto magazynu](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. W okienku konta magazynu wybierz Kafelek obiektów blob.

    ![Obiekty BLOB kafelka](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Na liście kontenerów Wybierz kontener, który jest używany przez klaster Spark.
6. Wybierz przekazywania.

    ![Upload](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Wybierz `hbase-site.xml` plik pobrany na komputerze lokalnym.

    ![Przekaż hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Wybierz przekazywania.
9. Połączenie z węzłem głównym klastra Spark przy użyciu protokołu SSH.
10. Kopiuj `hbase-site.xml` z klastra Spark domyślna magazynu do folderu konfiguracji Spark 2 w lokalnym magazynie klastra:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Uruchom powłokę Spark odwołujące się do łącznika HBase Spark

1. Połączenie z węzłem głównym klastra Spark przy użyciu protokołu SSH.
2. Uruchom powłokę spark Określanie pakietu łącznika HBase Spark:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/coroups/public/

3. Nie zamykaj tego wystąpienia powłoki Spark i przejdź do następnego kroku.

## <a name="define-a-catalog-and-query"></a>Zdefiniuj katalogu i zapytań

W tym kroku należy zdefiniować obiektu katalogu, który mapuje schemat bazy danych HBase z platformy Spark. 

1. Uruchom następujące polecenie w otwartych powłoki Spark, `import` instrukcji:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Określ katalog dla tabeli kontaktów utworzone w bazie danych HBase:
    1. Definiowania schematu katalogu o nazwie tabeli HBase `Contacts`.
    2. Zidentyfikuj rowkey jako `key`oraz mapowania nazw kolumn używane w łączniku Spark rodziny kolumn, nazwa kolumny i typ kolumny w HBase.
    3. Rowkey również musi być zdefiniowany szczegółowo jako kolumnę o nazwie (`rowkey`), mającego rodziny konkretnej kolumny `cf` z `rowkey`.

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

3. Zdefiniuj metodę, która zapewnia DataFrame wokół Twojej `Contacts` tabeli w bazie danych HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Utwórz wystąpienie DataFrame:

        val df = withCatalog(catalog)

5. Zapytanie DataFrame:

        df.show()

6. Powinny pojawić się dwa wiersze danych:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Zarejestruj tabeli tymczasowej, aby mogła zbadać tabeli HBase przy użyciu programu Spark SQL:

        df.registerTempTable("contacts")

8. Zapytanie SQL przed `contacts` tabeli:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Powinny być widoczne wyniki, takich jak te:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Wstawianie nowych danych

1. Aby wstawić nowy rekord kontaktu, zdefiniuj `ContactRecord` klasy:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Utwórz wystąpienie `ContactRecord` i umieszcza je w tablicy:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Zapisz tablicę nowe dane do bazy danych HBase:

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

* [Platforma Spark HBase łącznika](https://github.com/hortonworks-spark/shc)
