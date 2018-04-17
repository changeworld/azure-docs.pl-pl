---
title: Analizowanie danych opóźnienie transmitowane przy użyciu Hive w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystanie z programu Hive można analizować dane transmitowane w HDInsight opartych na systemie Linux, a następnie wyeksportować dane do bazy danych SQL za pomocą Sqoop.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: cc5d48b881ba59679c19baa3506c3c14c0db8048
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analizowanie danych opóźnienie transmitowane przy użyciu usługi Hive w usłudze HDInsight z systemem Linux

Sprawdzanie analizowanie danych opóźnienie transmitowane przy użyciu usługi Hive w usłudze HDInsight z systemem Linux i wyeksportować dane do bazy danych SQL Azure za pomocą Sqoop.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest tylko system operacyjny używany w usłudze Azure HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi HDInsight**. Zobacz [rozpocząć korzystanie z platformy Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) instrukcje dotyczące sposobu tworzenia nowego klastra usługi HDInsight opartej na systemie Linux.

* **Usługa Azure SQL Database**. Korzystasz z bazy danych Azure SQL jako miejsce docelowe magazynu danych. Jeśli nie masz bazy danych SQL, zobacz [tworzenie bazy danych Azure SQL w portalu Azure](../sql-database/sql-database-get-started.md).

* **Interfejs wiersza polecenia platformy Azure**. Jeśli nie zainstalowano wiersza polecenia platformy Azure, zobacz [zainstalować 1.0 interfejsu wiersza polecenia Azure](../cli-install-nodejs.md) dla większej liczby kroków.

## <a name="download-the-flight-data"></a>Pobierz dane transmitowane

1. Przejdź do [badań i Nowatorska technologia administracji, biura statystyk transportu][rita-website].

2. Na stronie wybierz następujące wartości:

   | Name (Nazwa) | Wartość |
   | --- | --- |
   | Filtr roku |2013 |
   | Filtruj okres |styczeń |
   | Pola |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Usuń zaznaczenie wszystkich innych pól. 

3. Wybierz **Pobierz**.

## <a name="upload-the-data"></a>Przekazywanie danych

1. Użyj następującego polecenia, aby przekazać plik zip do węzła głównego klastra usługi HDInsight:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Zastąp *FILENAME* z nazwą pliku zip. Zastąp *USERNAME* z logowaniem SSH dla klastra usługi HDInsight. Zastąp *CLUSTERNAME* z nazwą klastra usługi HDInsight.

   > [!NOTE]
   > Jeśli używasz hasła uwierzytelniania nazwy logowania SSH, zostanie wyświetlony monit o hasło. Jeśli używasz klucza publicznego, być może należy użyć `-i` parametru i określ ścieżkę do odpowiedniego klucza prywatnego. Na przykład `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Po zakończeniu przekazywania, połącz się z klastrem przy użyciu protokołu SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Użyj następującego polecenia, aby rozpakować plik zip:

    ```
    unzip FILENAME.zip
    ```

    To polecenie wyodrębnia plik CSV, który jest około 60 MB.

4. Użyj następującego polecenia, aby utworzyć katalog w magazynie usługi HDInsight, a następnie skopiuj plik do katalogu:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Tworzenie i uruchamianie HiveQL

Wykonaj następujące kroki, aby zaimportować dane z pliku CSV do tabeli programu Hive o nazwie **opóźnienia**.

1. Użyj następującego polecenia można tworzyć i edytować nowy plik o nazwie **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Zawartość tego pliku, należy użyć następującego tekstu:

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

2. Aby zapisać plik, użyj Ctrl + X, to Y.

3. Aby uruchomić Hive i przeprowadzić **flightdelays.hql** plików, użyj następującego polecenia:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Po __flightdelays.hql__ zakończeniu uruchamiania skryptu, użyj następującego polecenia, aby otworzyć sesji interaktywnej Beeline:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Po otrzymaniu `jdbc:hive2://localhost:10001/>` monit, użyj następującego zapytania do pobierania danych z danych opóźnienie transmitowane zaimportowane:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    To zapytanie pobiera listę miejscowości doświadczonym pogody, oraz średni opóźnienia, opóźnienia i zapisuje ją `/tutorials/flightdelays/output`. Później Sqoop odczytuje dane z tej lokalizacji i eksportuje je do bazy danych SQL Azure.

6. Aby zakończyć Beeline, wprowadź `!quit` w wierszu.

## <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

Jeśli masz już bazę danych SQL, należy uzyskać nazwę serwera. Aby znaleźć nazwę serwera w [portalu Azure](https://portal.azure.com), wybierz pozycję **baz danych programu SQL**, a następnie przeprowadź filtrowanie na nazwę bazy danych, którego chcesz użyć. Nazwa serwera ma na liście **serwera** kolumny.

Jeśli nie masz już bazę danych SQL, skorzystaj z informacji w [tworzenie bazy danych Azure SQL w portalu Azure](../sql-database/sql-database-get-started.md) go utworzyć. Zapisz nazwę serwera, służący do bazy danych.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

> [!NOTE]
> Istnieje wiele sposobów łączenia z bazą danych SQL i tworzenie tabeli. Następujące kroki użyj [protokół FreeTDS](http://www.freetds.org/) z klastrem usługi HDInsight.


1. Aby zainstalować protokół FreeTDS, użyj następującego polecenia z połączenia SSH do klastra:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Po zakończeniu instalacji, użyj następującego polecenia, aby połączyć się z serwerem bazy danych SQL. Zastąp **serverName** z nazwą serwera bazy danych SQL. Zastąp **adminLogin** i **adminPassword** z nazwy logowania bazy danych SQL. Zastąp **databaseName** o nazwie bazy danych.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Po wyświetleniu monitu wprowadź hasło dla nazwy logowania administratora bazy danych SQL.

    Pojawi się dane wyjściowe podobne do następującego tekstu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. W `1>` monit, wprowadź następujące wiersze:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Gdy `GO` instrukcja została wprowadzona, poprzednie instrukcje są oceniane. To zapytanie tworzy tabelę o nazwie **opóźnienia**, z indeksem klastrowanym.

    Użyj następującego zapytania, aby sprawdzić, czy tabela została utworzona:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Dane wyjściowe będą podobne do następującego tekstu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Wprowadź `exit` na `1>` prompt wyjść z narzędzia tsql.

## <a name="export-data-with-sqoop"></a>Eksportowanie danych z Sqoop

1. Aby sprawdzić, czy Sqoop widzą bazy danych SQL, użyj następującego polecenia:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    To polecenie zwraca listę baz danych, łącznie z bazy danych, w którym został utworzony w tabeli opóźnienia wcześniej.

2. Eksportowanie danych z hivesampletable do tabeli opóźnienia, użyj następującego polecenia:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop łączy do bazy danych, która zawiera tabelę opóźnienia i eksportowanie danych `/tutorials/flightdelays/output` katalogu do tabeli opóźnienia.

3. Po zakończeniu działania polecenia sqoop, należy użyć narzędzia tsql do połączenia z bazą danych:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Aby sprawdzić, czy dane zostały wyeksportowane do tabeli opóźnienia, należy zastosować następujące instrukcje:

    ```
    SELECT * FROM delays
    GO
    ```

    Powinna zostać wyświetlona lista danych w tabeli. Typ `exit` aby wyjść z narzędzia tsql.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej sposobów pracować z danymi w usłudze HDInsight, zobacz następujące artykuły:

* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z Oozie z usługą HDInsight][hdinsight-use-oozie]
* [Korzystanie z usługą HDInsight Sqoop][hdinsight-use-sqoop]
* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]
* [Tworzenie programów Java MapReduce dla platformy Hadoop w usłudze HDInsight][hdinsight-develop-mapreduce]
* [Opracowywanie Python przesyłania strumieniowego programy MapReduce dla usługi HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
