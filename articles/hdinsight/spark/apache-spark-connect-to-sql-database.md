---
title: Platforma Apache Spark umożliwia odczytywanie i zapisywanie danych do usługi Azure SQL database
description: Dowiedz się, jak skonfigurować połączenie między klastra platformy HDInsight Spark i Azure SQL database do odczytywania danych, zapis danych i przesyłanie strumieniowe danych do bazy danych SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: 84946083146517146ad9aeb48693230aaaaf7943
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701176"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Klaster HDInsight Spark umożliwia odczytywanie i zapisywanie danych do usługi Azure SQL database

Dowiedz się, jak połączyć klaster Apache Spark w usłudze Azure HDInsight przy użyciu usługi Azure SQL database i odczytania, zapisywania i przesyłanie strumieniowe danych do bazy danych SQL. Instrukcje w tym artykule korzystają [notesu programu Jupyter](https://jupyter.org/) można uruchamiać fragmenty kodu z języka Scala. Można jednak tworzenie autonomicznych aplikacji w języku Scala lub Python i wykonywać te same zadania. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi Azure HDInsight Spark**.  Postępuj zgodnie z instrukcjami w artykule [Tworzenie klastra Apache Spark w HDInsight](apache-spark-jupyter-spark-sql.md).

* **Usługa Azure SQL database**. Postępuj zgodnie z instrukcjami w artykule [utworzyć bazę danych Azure SQL](../../sql-database/sql-database-get-started-portal.md). Upewnij się, Utwórz bazę danych przy użyciu przykładu **AdventureWorksLT** schemat i dane. Upewnij się również, że utworzono regułę zapory na poziomie serwera umożliwia adresu IP klienta dostępu do bazy danych SQL na serwerze. Instrukcjami, aby dodać reguły zapory są dostępne w tym samym artykule. Po utworzeniu bazy danych Azure SQL, upewnij się, że następujące wartości zachować pod ręką. Będą one potrzebne do łączenia z bazą danych z klastra Spark.

    * Nazwa serwera hostującego bazę danych Azure SQL.
    * Nazwa bazy danych SQL Azure.
    * Nazwa użytkownika administratora bazy danych Azure SQL / hasła.

* **SQL Server Management Studio**. Postępuj zgodnie z instrukcjami w artykule [Użyj SSMS do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter 

Rozpocznij od utworzenia [notesu programu Jupyter](https://jupyter.org/) skojarzonego z klastrem Spark. Ten notes umożliwia uruchamiać fragmenty kodu, używane w tym artykule. 

1. Z [witryny Azure portal](https://portal.azure.com/), otwórz klaster.
1. Wybierz **notesu programu Jupyter** poniżej **pulpity nawigacyjne klastra** po prawej stronie.  Jeśli nie widzisz **pulpity nawigacyjne klastra**, kliknij przycisk **Przegląd** menu po lewej stronie w bloku. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    ![Notes Jupyter na platformie Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter notebook na platformie Spark")
   
   > [!NOTE]  
   > Można również uzyskać dostęp notesu Jupyter w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. W notesie Jupyter w prawym górnym rogu, kliknij przycisk **New**, a następnie kliknij przycisk **Spark** Aby utworzyć notes Scala. Notesy Jupyter w klastrze HDInsight Spark zapewniają również **PySpark** jądra dla aplikacji Python2 i **PySpark3** jądra dla aplikacji w języku Python3. W tym artykule utworzymy Notes Scala.
   
    ![Jądra dla notesu Jupyter na platformie Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "jądra dla notesu Jupyter na platformie Spark")

    Aby uzyskać informacje na temat tych jąder, zobacz [Use Jupyter notebooks kernels with Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-kernels.md) (Używanie jąder notesu Jupyter z klastrami Apache Spark w usłudze HDInsight).

   > [!NOTE]  
   > W tym artykule używamy jądra Spark (Scala), ponieważ dane przesyłane strumieniowo z platformy Spark do bazy danych SQL jest obsługiwana tylko w języku Scala i Java obecnie. Mimo że odczytywanie z oraz zapisywanie w bazie SQL może odbywać się przy użyciu języka Python w celu zachowania spójności, w tym artykule, używamy Scala dla wszystkich trzech operacji.

1. Zostanie otwarty nowy notes o domyślnej nazwie **bez tytułu**. Kliknij nazwę notesu, a następnie wprowadź wybraną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Wprowadzanie nazwy notesu")

Teraz możesz rozpocząć tworzenie aplikacji.
    
## <a name="read-data-from-azure-sql-database"></a>Odczytywanie danych z bazy danych Azure SQL

W tej sekcji można odczytać danych z tabeli (na przykład **SalesLT.Address**) znajdujące się w bazie AdventureWorks.

1. W nowego notesu Jupyter w komórce kodu Wklej poniższy fragment kodu, a następnie zastąp wartości zastępcze wartościami dotyczącymi usługi Azure SQL database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

1. Poniższy fragment kodu umożliwia utworzenie adresu URL JDBC, którą można przekazać do elementów dataframe aparatu Spark tworzy interfejsy API `Properties` obiekt do przechowywania parametrów. Wklej ten fragment w komórce kodu i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Poniższy fragment kodu umożliwia Utwórz ramkę danych przy użyciu danych z tabeli w usłudze Azure SQL database. W tym fragmencie kodu użyto **SalesLT.Address** tabeli, która jest dostępna jako część **AdventureWorksLT** bazy danych. Wklej ten fragment w komórce kodu i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Można teraz wykonywać operacje na ramkę danych, takie jak pobieranie schematu danych:

       sqlTableDF.printSchema
   
    Zobaczysz dane wyjściowe podobne do następujących:

    ![Wprowadzanie nazwy notesu](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Wprowadzanie nazwy notesu")

1. Można również wykonywać operacje takie jak pobieranie pierwszych 10 wierszy.

       sqlTableDF.show(10)

1. Można również pobrać określone kolumny z zestawu danych.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Zapis danych do bazy danych Azure SQL

W tej sekcji używamy dostępny przykładowy plik CSV w klastrze do tworzenia tabeli w bazie danych Azure SQL i wypełnić je danymi. Przykładowy plik CSV (**HVAC.csv**) jest dostępny w wszystkich klastrów HDInsight w `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. W nowego notesu Jupyter w komórce kodu Wklej poniższy fragment kodu, a następnie zastąp wartości zastępcze wartościami dotyczącymi usługi Azure SQL database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.  

1. Poniższy fragment kodu tworzy adres URL JDBC, którą można przekazać do elementów dataframe aparatu Spark tworzy interfejsy API `Properties` obiekt do przechowywania parametrów. Wklej ten fragment w komórce kodu i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Poniższy fragment kodu umożliwia wyodrębnianie schemat danych w HVAC.csv i schematu do ładowania danych z pliku CSV w ramkę danych, `readDf`. Wklej ten fragment w komórce kodu i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Użyj `readDf` ramkę danych, aby utworzyć tabelę tymczasową `temphvactable`. Następnie użyj tabeli tymczasowej, aby utworzyć tabelę programu hive `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Na koniec użyj tabelę programu hive, aby utworzyć tabelę w bazie danych Azure SQL. Poniższy fragment kodu tworzy `hvactable` w bazie danych Azure SQL.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Połącz z bazą danych Azure SQL za pomocą programu SSMS i sprawdź, czy widzisz `dbo.hvactable` istnieje.

    a. Uruchomić program SSMS i Połącz z bazą danych Azure SQL, zapewniając informacje dotyczące połączenia, jak pokazano na poniższym zrzucie ekranu.

    ![Nawiązywanie połączenia z bazą danych SQL przy użyciu programu SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "nawiązywanie połączenia z bazą danych SQL przy użyciu programu SSMS")

    b. W Eksploratorze obiektów rozwiń węzeł bazy danych Azure SQL i węzeł tabeli, aby zobaczyć **dbo.hvactable** utworzone.

    ![Nawiązywanie połączenia z bazą danych SQL przy użyciu programu SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "nawiązywanie połączenia z bazą danych SQL przy użyciu programu SSMS")

1. Uruchom zapytanie w programie SSMS, aby zobaczyć kolumn w tabeli.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Stream dane do bazy danych Azure SQL

W tej sekcji, firma Microsoft przesyłanie strumieniowe danych do **hvactable** już utworzony w usłudze Azure SQL database w poprzedniej sekcji.

1. Pierwszym krokiem, upewnij się, nie ma żadnych rekordów w **hvactable**. Za pomocą programu SSMS, uruchom następujące zapytanie w tabeli.

       DELETE FROM [dbo].[hvactable]

1. Tworzenie nowego notesu Jupyter w klastrze HDInsight Spark. Wklej poniższy fragment kodu w komórce kodu, a następnie naciśnij klawisz **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Firma Microsoft strumienia danych z **HVAC.csv** do hvactable. Plik HVAC.csv jest dostępny w klastrze w */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. W poniższym fragmencie kodu możemy najpierw pobrania schematu dane, które mają być przesyłane strumieniowo. Następnie utwórz ramkę danych przesyłania strumieniowego przy użyciu tego schematu. Wklej ten fragment w komórce kodu i naciśnij klawisz **SHIFT + ENTER** do uruchomienia.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Dane wyjściowe pokazują schemat **HVAC.csv**. **Hvactable** ma taki sam schemat. Dane wyjściowe wyświetla listę kolumn w tabeli.

    ![Schemat tabeli](./media/apache-spark-connect-to-sql-database/schema-of-table.png "schemat tabeli")

1. Na koniec użyj następującego fragmentu kodu do odczytywania danych z HVAC.csv i przesyłania strumieniowego do **hvactable** w bazie danych Azure SQL. Wklej ten fragment w komórce kodu, Zastąp wartości zastępcze wartościami dotyczącymi usługi Azure SQL database, a następnie naciśnij **SHIFT + ENTER** do uruchomienia.

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

1. Sprawdź, czy dane są przesyłane strumieniowo do **hvactable** , uruchamiając następujące zapytanie w SQL Server Management Studio (SSMS). Za każdym razem, gdy możesz uruchomić zapytanie, pokazuje liczbę wierszy w tabeli, zwiększając.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Kolejne kroki

* [Analizowanie danych w usługi Data Lake Storage przy użyciu klastra platformy HDInsight Spark](apache-spark-use-with-data-lake-store.md)
* [Przetwarzanie zdarzeń przesyłania strumieniowego ze strukturą za pomocą usługi Event Hub](apache-spark-eventhub-structured-streaming.md)
* [Use Apache Spark Structured Streaming przy użyciu platformy Apache Kafka w HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
