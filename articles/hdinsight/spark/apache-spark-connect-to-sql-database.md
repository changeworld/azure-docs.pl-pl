---
title: Użyj Apache Spark do odczytywania i zapisywania danych do usługi Azure SQL database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować połączenie między klastrem Spark w usłudze HDInsight i bazy danych Azure SQL można odczytać danych, zapis danych i strumienia danych do bazy danych SQL
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: nitinme
ms.openlocfilehash: 63bf7d5a0ad988ff7a6b498b4e91e90de97b507b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32775646"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Używanie klastra Spark w usłudze HDInsight do odczytywania i zapisywania danych do bazy danych Azure SQL

Dowiedz się, jak klastra Apache Spark w usłudze Azure HDInsight Uzyskuj dostęp do bazy danych Azure SQL i następnie odczytu, zapisu i przesyłać strumieniowo dane do bazy danych SQL. Instrukcje w tym artykule użyć notesu Jupyter do uruchomienia Scala wstawki kodu. Można jednak tworzenie autonomicznych aplikacji w języku Scala lub Python i wykonaj te same zadania. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Azure klastra Spark w usłudze HDInsight**.  Postępuj zgodnie z instrukcjami w [Tworzenie klastra Apache Spark w usłudze HDInsight](apache-spark-jupyter-spark-sql.md).

* **Baza danych SQL Azure**. Postępuj zgodnie z instrukcjami w [tworzenie bazy danych Azure SQL](../../sql-database/sql-database-get-started-portal.md). Upewnij się, że Utwórz bazę danych z próbką **AdventureWorksLT** schemat i dane. Ponadto upewnij się, że utworzono regułę zapory poziomu serwera w celu zezwalania na adres IP klienta do dostępu do bazy danych SQL na serwerze. Z instrukcjami, aby dodać regułę zapory jest dostępna w tym samym artykule. Po utworzeniu bazy danych Azure SQL, upewnij się, że zachowasz następujące wartości pod ręką. Należy je do połączenia z bazą danych z klastra Spark.

    * Nazwa serwera hostującego bazę danych Azure SQL
    * Nazwa bazy danych SQL Azure
    * Nazwa użytkownika administratora bazy danych Azure SQL / hasła

* **SQL Server Management Studio**. Postępuj zgodnie z instrukcjami w [SSMS używany do nawiązywania połączeń i zapytania na danych](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Rozpocznij od utworzenia notesu Jupyter skojarzony z klastrem Spark. Ten notes używane do uruchamiania wstawki kodu używanych w tym artykule. 

1. Z [portalu Azure](https://portal.azure.com/), otwórz klastra. 

2. Z **szybkie linki** kliknij **klastra pulpity nawigacyjne** otworzyć **klastra pulpity nawigacyjne** widoku.  Jeśli nie widzisz **szybkie linki**, kliknij przycisk **omówienie** z menu po lewej stronie w bloku.

    ![Pulpitu nawigacyjnego klastra Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "pulpitu nawigacyjnego klastra Spark") 

3. Kliknij przycisk **notesu Jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    ![Notesu Jupyter na Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "notesu Jupyter na Spark")
   
   > [!NOTE]
   > Można również przejść notesu Jupyter w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. Notesu Jupyter, w prawym górnym rogu, kliknij przycisk **nowy**, a następnie kliknij przycisk **Spark** do tworzenie notesu Scala. Udostępniają notesów Jupyter w klastrze Spark w usłudze HDInsight **PySpark** jądra dla aplikacji Python2 i **PySpark3** jądra dla Python3 aplikacji. W tym artykule utworzymy notesu Scala.
   
    ![Jądra dla notesu Jupyter na Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "jądra dla notesu Jupyter na Spark")

    Aby uzyskać informacje na temat tych jąder, zobacz [Use Jupyter notebooks kernels with Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-kernels.md) (Używanie jąder notesu Jupyter z klastrami Apache Spark w usłudze HDInsight).

   > [!NOTE]
   > W tym artykule używamy jądra Spark (Scala), ponieważ dane przesyłane strumieniowo z Spark do bazy danych SQL jest obsługiwana tylko w języku Scala i Java obecnie. Mimo że odczytywanie z oraz zapisywanie SQL może odbywać się przy użyciu języka Python, spójności w tym artykule, używamy Scala dla wszystkich operacji.
   >

5. Zostanie otwarty nowy notes o nazwie domyślnej **bez tytułu**. Kliknij nazwę notesu, a następnie wprowadź wybraną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Wprowadzanie nazwy notesu")

Można teraz rozpocząć tworzenie aplikacji.
    
## <a name="read-data-from-azure-sql-database"></a>Odczyt danych z bazy danych Azure SQL

W tej sekcji można odczytać danych z tabeli (na przykład **SalesLT.Address**) znajdujące się w bazie danych AdventureWorks.

1. W nowego notesu Jupyter w komórce kodu Wklej poniższy fragment i zastąp symbole zastępcze wartości dla Twojej bazy danych Azure SQL.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

2. Poniższy fragment umożliwia tworzenie adresów URL JDBC, które można przekazać do dataframe Spark tworzy interfejsów API `Properties` obiektu do przechowywania parametrów. Wklej fragment w komórce kod i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. Poniższy fragment umożliwia utworzenie dataframe przy użyciu danych z tabeli w bazie danych Azure SQL. W tym fragmencie, używamy **SalesLT.Address** tabeli, która jest dostępna jako część **AdventureWorksLT** bazy danych. Wklej fragment w komórce kod i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Można teraz wykonywać operacje na dataframe, takie jak pobieranie schematu danych:

       sqlTableDF.printSchema
   
    Zostaną wyświetlone dane wyjściowe podobne do następujących:

    ![Wprowadzanie nazwy notesu](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Wprowadzanie nazwy notesu")

5. Można również wykonywać operacje, takie jak pobieranie pierwszych 10 wierszy.

       sqlTableDF.show(10)

6. Lub pobrać określonych kolumn z zestawu danych.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Zapis danych do bazy danych Azure SQL

W tej sekcji używamy dostępne przykładowy plik CSV w klastrze do tworzenia tabeli w bazie danych Azure SQL i wypełnić je danymi. Przykładowy plik CSV (**HVAC.csv**) jest dostępny na wszystkich klastrach usługi HDInsight w `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. W nowego notesu Jupyter w komórce kodu Wklej poniższy fragment i zastąp symbole zastępcze wartości dla Twojej bazy danych Azure SQL.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

2. Poniższy fragment kompilacje JDBC adres URL, które można przekazać do dataframe Spark tworzy interfejsów API `Properties` obiektu do przechowywania parametrów. Wklej fragment w komórce kod i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. Użyj następującego fragmentu kodu do wyodrębniania danych w HVAC.csv schematu i użycie schematu do ładowania danych z pliku CSV w dataframe, `readDf`. Wklej fragment w komórce kod i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Użyj `readDf` dataframe można utworzyć tabeli tymczasowej `temphvactable`. Następnie użyj tabeli tymczasowej do utworzenia tabeli programu hive `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Na koniec skorzystaj z tabeli hive można utworzyć tabeli w bazie danych Azure SQL. Poniższy fragment kodu tworzy `hvactable` w bazie danych Azure SQL.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Połączenia z bazą danych Azure SQL przy użyciu narzędzia SSMS i sprawdź, czy widzisz `dbo.hvactable` istnieje.

    a. Uruchomić program SSMS oraz połączenie z bazą danych Azure SQL udostępniając szczegóły połączenia, jak pokazano na poniższym zrzucie ekranu.

    ![Połącz z bazą danych SQL przy użyciu narzędzia SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Połącz z bazą danych SQL przy użyciu narzędzia SSMS")

    b. W Eksploratorze obiektów rozwiń węzeł bazy danych Azure SQL i węzeł tabeli, aby zobaczyć **dbo.hvactable** utworzony.

    ![Połącz z bazą danych SQL przy użyciu narzędzia SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Połącz z bazą danych SQL przy użyciu narzędzia SSMS")

7. Uruchom zapytanie w programie SSMS, aby zobaczyć kolumny w tabeli.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Strumień danych do bazy danych Azure SQL

W tej sekcji możemy strumienia danych do **hvactable** już utworzony w bazie danych Azure SQL w poprzedniej sekcji.

1. Pierwszym krokiem upewnij się, nie ma żadnych rekordów w **hvactable**. Przy użyciu narzędzia SSMS, uruchom następujące zapytanie w tabeli.

       DELETE FROM [dbo].[hvactable]

2. Tworzenie nowego notesu Jupyter w klastrze Spark w usłudze HDInsight. Wklej poniższy fragment kodu w komórce kodu, a następnie naciśnij klawisz **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Firma Microsoft strumienia danych z **HVAC.csv** do hvactable. Plik HVAC.csv jest dostępny w klastrze na */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. W poniższy fragment kodu możemy najpierw pobrać schematu dane przesyłane strumieniowo. Następnie utwórz dataframe przesyłania strumieniowego, przy użyciu tego schematu. Wklej fragment w komórce kod i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. Dane wyjściowe zawierają schemat **HVAC.csv**. **Hvactable** ma ten sam schemat. Dane wyjściowe listy kolumn w tabeli.

    ![Schemat tabeli](./media/apache-spark-connect-to-sql-database/schema-of-table.png "schemat tabeli")

5. Na koniec użyj następującego fragmentu kodu do odczytywania danych z HVAC.csv i go do strumienia **hvactable** w bazie danych Azure SQL. Wkleić fragment kodu w komórce kodu, zastąp symbole zastępcze wartości dla Twojej bazy danych Azure SQL, a następnie naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

6. Sprawdź, czy dane są przesyłane strumieniowo do **hvactable** , uruchamiając następujące zapytanie w programu SQL Server Management Studio (SSMS). Zawsze, gdy uruchom zapytanie, pokazuje liczbę wierszy w zwiększenie tabeli.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Kolejne kroki

* [Używanie klastra Spark w usłudze HDInsight do analizy danych w usłudze Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Proces strukturę zdarzenia przesyłania strumieniowego przy użyciu Centrum EventHub](apache-spark-eventhub-structured-streaming.md)
* [Korzystanie z platformy Spark strukturę przesyłania strumieniowego z Kafka w usłudze HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
