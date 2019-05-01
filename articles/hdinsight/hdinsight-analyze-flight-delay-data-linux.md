---
title: 'Samouczek: Wykonywanie operacji wyodrębniania, przekształcania i ładowania (ETL) przy użyciu oprogramowania Hive w usłudze HDInsight — Azure '
description: Dowiedz się, jak wyodrębnić dane z nieprzetworzonego zestawu danych CSV, przekształcić je za pomocą oprogramowania Hive w usłudze HDInsight, a następnie załadować przekształcone dane do bazy danych Azure SQL Database za pomocą narzędzia Apache Sqoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: eb86dc8c5c3b215a2c90380b4009efd00d2a243c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723145"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight

W tym samouczku użyjesz nieprzetworzonego pliku danych CSV, zaimportujesz go do magazynu klastra usługi HDInsight i przekształcisz dane za pomocą oprogramowania [Apache Hive](https://hive.apache.org/) w usłudze Azure HDInsight. Przekształcone dane można załadować do bazy danych Azure SQL Database przy użyciu narzędzia [Apache Sqoop](https://sqoop.apache.org/). W tym artykule zostaną użyte publicznie dostępne dane lotów.

> [!IMPORTANT]  
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą Azure HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Ten samouczek obejmuje następujące zadania: 

> [!div class="checklist"]
> * Pobieranie przykładowych danych lotów
> * Przekazywanie danych do klastra usługi HDInsight
> * Przekształcanie danych za pomocą oprogramowania Hive
> * Tworzenie tabeli w bazie danych Azure SQL Database
> * Eksportowanie danych do bazy danych Azure SQL Database za pomocą narzędzia Sqoop


Poniższa ilustracja przedstawia typowy przepływ aplikacji ETL.

![Operacja ETL używająca oprogramowania Apache Hive w usłudze Azure HDInsight](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "Operacja ETL używająca oprogramowania Apache Hive w usłudze Azure HDInsight")

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster Hadoop oparty na systemie Linux w usłudze HDInsight**. Aby uzyskać instrukcje dotyczące tworzenia nowego klastra usługi HDInsight opartego na systemie Linux, zobacz [Rozpoczęcie korzystania z usługi Apache Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Usługa Azure SQL Database**. Używasz bazy danych Azure SQL Database jako docelowego magazynu danych. Jeśli nie masz bazy danych SQL, zobacz [Tworzenie bazy danych Azure SQL Database w witrynie Azure Portal](../sql-database/sql-database-get-started.md).

* **Interfejs wiersza polecenia platformy Azure**. Jeśli nie zainstalowano wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), aby uzyskać więcej procedur.

* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Pobieranie danych lotów

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Na stronie wybierz następujące wartości:

   | Name (Nazwa) | Wartość |
   | --- | --- |
   | Rok filtrowania |2013 |
   | Filter Period (Okres filtrowania) |January (Styczeń) |
   | Pola |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Wyczyść wszystkie pozostałe pola. 

3. Wybierz przycisk **Download** (Pobierz). Uzyskasz plik zip z wybranymi polami danych.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Przekazywanie danych do klastra usługi HDInsight

Istnieje wiele sposobów przekazywania danych do magazynu skojarzonego z klastrem usługi HDInsight. W tej sekcji przekażesz dane za pomocą polecenia `scp`. Aby dowiedzieć się więcej o innych sposobach przekazywania danych, zobacz [Upload data to HDInsight (przekazywanie danych do usługi HDInsight)](hdinsight-upload-data.md).

1. Otwórz wiersz polecenia i użyj następującego polecenia, aby przekazać plik zip do węzła głównego klastra usługi HDInsight:

    ```bash
    scp <FILENAME>.zip <SSH-USERNAME>@<CLUSTERNAME>-ssh.azurehdinsight.net:<FILENAME.zip>
    ```

    Zamień ciąg *FILENAME* na nazwę pliku zip. Zamień ciąg *USERNAME* na nazwę logowania SSH dla klastra usługi HDInsight. Zamień ciąg *CLUSTERNAME* na nazwę klastra usługi HDInsight.

   > [!NOTE]  
   > Jeśli uwierzytelniasz nazwę logowania SSH przy użyciu hasła, zostanie wyświetlony monit o podanie hasła. Jeśli używasz klucza publicznego, może być konieczne użycie parametru `-i` w celu określenia ścieżki do zgodnego klucza prywatnego. Na przykład `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Po zakończeniu przekazywania połącz się z klastrem przy użyciu protokołu SSH. W wierszu polecenia wprowadź następujące polecenie:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

3. Użyj następującego polecenia, aby rozpakować plik zip:

    ```bash
    unzip FILENAME.zip
    ```

    To polecenie spowoduje wyodrębnienie pliku CSV o rozmiarze około 60 MB.

4. Użyj następujących poleceń, aby utworzyć katalog w magazynie usługi HDInsight, a następnie skopiuj plik CSV do katalogu:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put <FILENAME>.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Przekształcanie danych za pomocą zapytania oprogramowania Hive

Istnieje wiele sposobów uruchamiania zadania oprogramowania Hive w klastrze usługi HDInsight. W tej sekcji uruchomisz zadanie oprogramowania Hive za pomocą usługi [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell). Aby uzyskać informacje o innych metodach uruchamiania zadania Hive, zobacz [Używanie oprogramowania Apache Hive w usłudze HDInsight](./hadoop/hdinsight-use-hive.md).

W ramach zadania oprogramowania Hive można zaimportować dane z pliku csv do tabeli oprogramowania Hive o nazwie **Delays**.

1. W wierszu polecenia SSH, który już istnieje dla klastra usługi HDInsight, użyj następującego polecenia, aby utworzyć i edytować nowy plik o nazwie **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Użyj następującego tekstu jako zawartości tego pliku:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Aby zapisać plik, naciśnij klawisz **Esc**, a następnie wprowadź ciąg `:x`.

3. Aby uruchomić oprogramowanie Hive i uruchomić plik **flightdelays.hql**, użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Gdy skrypt __flightdelays.hql__ zakończy działanie, użyj następującego polecenia, aby otworzyć interaktywną sesję usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Po otrzymaniu wiersza `jdbc:hive2://localhost:10001/>` użyj następującego zapytania, aby pobrać dane z zaimportowanych danych opóźnień lotów:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    To zapytanie pobiera listę miejscowości z opóźnieniami ze względu na pogodę i średni czas opóźnienia, a następnie zapisuje ją w lokalizacji `/tutorials/flightdelays/output`. Później narzędzie Sqoop odczyta dane z tej lokalizacji i wyeksportuje je do bazy danych Azure SQL Database.

6. Aby zakończyć działanie usługi Beeline, wprowadź polecenie `!quit` w wierszu polecenia.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

W tej sekcji założono, że utworzono już bazę danych Azure SQL Database. Jeśli nie masz jeszcze bazy danych SQL, skorzystaj z informacji w artykule [Tworzenie bazy danych Azure SQL Database w witrynie Azure Portal](../sql-database/sql-database-get-started.md), aby ją utworzyć.

Jeśli masz już bazę danych SQL, musisz uzyskać nazwę serwera. Aby znaleźć nazwę serwera w witrynie [Azure Portal](https://portal.azure.com), wybierz pozycję **Bazy danych SQL**, a następnie odfiltruj nazwę bazy danych, której chcesz użyć. Nazwa serwera jest wyświetlana w kolumnie **Nazwa serwera**.

![Uzyskiwanie szczegółów serwera Azure SQL](./media/hdinsight-analyze-flight-delay-data-linux/get-azure-sql-server-details.png "Uzyskiwanie szczegółów serwera Azure SQL")

> [!NOTE]  
> Istnieje wiele sposobów nawiązywania połączenia z bazą danych SQL i tworzenia tabeli. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.


1. Aby zainstalować rozwiązanie FreeTDS, użyj następującego polecenia dla połączenia SSH z klastrem:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Po zakończeniu instalacji użyj poniższego polecenia, aby połączyć się z serwerem usługi SQL Database. Zamień ciąg **serverName** na nazwę serwera usługi SQL Database. Zamień ciągi **adminLogin** i **adminPassword** na nazwę logowania bazy danych SQL. Zamień ciąg **databaseName** na nazwę bazy danych.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Gdy zostanie wyświetlony monit, wprowadź hasło dla nazwy logowania administratora bazy danych SQL.

    Uzyskasz dane wyjściowe podobne do następującego tekstu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. W wierszu `1>` wprowadź następujące wiersze:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. To zapytanie tworzy tabelę o nazwie **delays** z indeksem klastrowanym.

    Użyj następującego zapytania, aby sprawdzić, czy utworzono tabelę:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Dane wyjściowe będą podobne do następującego tekstu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Wprowadź ciąg `exit` w wierszu `1>`, aby zakończyć działanie narzędzia tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Eksportowanie danych do bazy danych SQL przy użyciu narzędzia Apache Sqoop

W poprzednich sekcjach skopiowano przekształcone dane w lokalizacji `/tutorials/flightdelays/output`. W tej sekcji użyjesz narzędzia Sqoop, aby wyeksportować dane z lokalizacji „/tutorials/flightdelays/output” do tabeli utworzonej w bazie danych Azure SQL Database. 

1. Aby sprawdzić, czy baza danych SQL jest widoczna w narzędziu Sqoop, użyj następującego polecenia:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    To polecenie zwraca listę baz danych, łącznie z bazami danych, w których wcześniej utworzono tabelę delays.

2. Użyj następującego polecenia, aby wyeksportować dane z przykładowej tabeli oprogramowania Hive do tabeli delays:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Narzędzie Sqoop nawiążę połączenie z bazą danych zawierającą tabelę delays i wyeksportuje dane z katalogu `/tutorials/flightdelays/output` do tabeli delays.

3. Po zakończeniu działania polecenia sqoop użyj narzędzia tsql, aby nawiązać połączenie z bazą danych:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Aby sprawdzić, czy dane zostały wyeksportowane do tabeli delays, użyj następujących instrukcji:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Powinna zostać wyświetlona lista danych w tabeli. Tabela zawiera nazwę miejscowości i średni czas opóźnienia lotów dla tej miejscowości. 

    Wpisz `exit`, aby zakończyć działanie narzędzia tsql.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak wykonywać operacje wyodrębniania, przekształcania i ładowania danych przy użyciu klastra Apache Hadoop w usłudze HDInsight. Przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć klastry Hadoop w usłudze HDInsight na żądanie przy użyciu usługi Azure Data Factory.

> [!div class="nextstepaction"]
>[Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)

Aby poznać więcej sposobów pracy z danymi w usłudze HDInsight, zobacz następujące artykuły:

* [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](../storage/data-lake-storage/tutorial-extract-transform-load-hive.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z programu Apache Pig z usługą HDInsight][hdinsight-use-pig]
* [Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w usłudze HDInsight][hdinsight-develop-mapreduce]

* [Korzystanie z programu Apache Oozie z usługą HDInsight][hdinsight-use-oozie]
* [Korzystanie z programu Apache Sqoop z usługą HDInsight][hdinsight-use-sqoop]



[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/


[rita-website]: https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
