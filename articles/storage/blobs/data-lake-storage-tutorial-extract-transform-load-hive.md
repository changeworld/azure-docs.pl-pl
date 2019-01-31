---
title: 'Samouczek: Wykonywanie operacji wyodrębniania, przekształcania i ładowania (ETL) przy użyciu technologii Apache Hive w usłudze Azure HDInsight'
description: W ramach tego samouczka dowiesz się, jak wyodrębnić dane z zestawu danych pierwotnych w postaci pliku CSV, przekształcić je za pomocą technologii Apache Hive w usłudze Azure HDInsight, a następnie załadować przekształcone dane do bazy danych Azure SQL Database za pomocą narzędzia Sqoop.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 70ad37aa0ccbab762aa6e5cfb05d385e8b2a86ee
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244015"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu technologii Apache Hive w usłudze Azure HDInsight

W ramach tego samouczka wykonasz operację ETL: wyodrębnianie, przekształcanie i ładowanie danych. Pobierzesz plik danych pierwotnych w formacie CSV, zaimportujesz te dane do klastra usługi Azure HDInsight, przekształcisz je przy użyciu technologii Apache Hive i załadujesz do usługi Azure SQL Database za pomocą narzędzia Apache Sqoop.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyodrębnianie danych i przekazywanie ich do klastra usługi HDInsight.
> * Przekształcanie danych przy użyciu technologii Apache Hive.
> * Ładowanie danych do usługi Azure SQL Database za pomocą narzędzia Sqoop.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster Hadoop oparty na systemie Linux w usłudze HDInsight**: Aby utworzyć nowy klaster usługi HDInsight oparty na systemie Linux, zobacz [Konfigurowanie klastrów w usłudze HDInsight za pomocą platform Hadoop, Spark, Kafka i innych](./data-lake-storage-quickstart-create-connect-hdi-cluster.md).

* **Azure SQL Database**: Używasz bazy danych Azure SQL Database jako docelowego magazynu danych. Jeśli nie masz bazy danych SQL, zobacz [Tworzenie bazy danych SQL platformy Azure w witrynie Azure Portal](../../sql-database/sql-database-get-started.md).

* **Interfejs wiersza polecenia platformy Azure**: Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Klient SSH**: Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Hadoop) przy użyciu protokołu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Procedura przedstawiona w tym artykule wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą Azure HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

### <a name="download-the-flight-data"></a>Pobieranie danych lotów

W tym samouczku w celu zademonstrowania sposobu wykonywania operacji ETL są używane dane dotyczące lotów opracowane przez agencję Bureau of Transportation Statistics. Aby ukończyć samouczek, musisz pobrać te dane.

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

1. Na stronie wybierz następujące wartości:

   | Name (Nazwa) | Wartość |
   | --- | --- |
   | **Filter Year (Filtruj rok)** |2013 |
   | **Filter Period (Filtruj okres)** |January (Styczeń) |
   | **Pola** |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

1. Wyczyść wszystkie pozostałe pola.

1. Wybierz przycisk **Download** (Pobierz). Uzyskasz plik zip z wybranymi polami danych.

## <a name="extract-and-upload-the-data"></a>Wyodrębnianie i przekazywanie danych

W tej sekcji użyjesz polecenia `scp`, aby przekazać dane do klastra usługi HDInsight.

Otwórz wiersz polecenia i użyj następującego polecenia, aby przekazać plik zip do węzła głównego klastra usługi HDInsight:

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* Zastąp ciąg \<FILE_NAME> nazwą pliku zip.
* Zastąp ciąg \<SSH_USER_NAME> nazwą logowania SSH dla klastra usługi HDInsight.
* Zastąp ciąg \<CLUSTER_NAME> nazwą klastra usługi HDInsight.

Jeśli uwierzytelniasz nazwę logowania SSH przy użyciu hasła, zostanie wyświetlony monit o podanie hasła. 

Jeśli używasz klucza publicznego, może być konieczne użycie parametru `-i` w celu określenia ścieżki do zgodnego klucza prywatnego. Na przykład `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

Po zakończeniu przekazywania połącz się z klastrem przy użyciu protokołu SSH. W wierszu polecenia wprowadź następujące polecenie:

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

Użyj następującego polecenia, aby rozpakować plik zip:

```bash
unzip <FILE_NAME>.zip
```

To polecenie umożliwia wyodrębnienie pliku **csv** o rozmiarze około 60 MB.

Użyj następujących poleceń, aby utworzyć katalog, a następnie skopiować plik *csv* do tego katalogu:

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

Za pomocą następującego polecenia utwórz system plików usługi Data Lake Storage Gen2.

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>Przekształcanie danych

W tej sekcji uruchomisz zadanie Apache Hive za pomocą usługi Beeline.

W ramach zadania Apache Hive zaimportujesz dane z pliku csv do tabeli Apache Hive o nazwie **delays**.

W wierszu polecenia SSH, który już istnieje dla klastra usługi HDInsight, użyj następującego polecenia, aby utworzyć i edytować nowy plik o nazwie **flightdelays.hql**:

```bash
nano flightdelays.hql
```

Użyj następującego tekstu jako zawartości tego pliku:

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
 LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

-- Drop the delays table if it exists
DROP TABLE delays;
-- Create the delays table and populate it with data
-- pulled in from the CSV file (via the external table defined previously)
CREATE TABLE delays
LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
AS
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

Aby zapisać plik, naciśnij klawisz Esc, a następnie wprowadź polecenie `:x`.

Aby uruchomić oprogramowanie Hive i uruchomić plik **flightdelays.hql**, użyj następującego polecenia:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

Gdy skrypt __flightdelays.hql__ zakończy działanie, użyj następującego polecenia, aby otworzyć interaktywną sesję usługi Beeline:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

Po otrzymaniu wiersza `jdbc:hive2://localhost:10001/>` użyj następującego zapytania, aby pobrać dane z zaimportowanych danych opóźnień lotów:

```hiveql
INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
SELECT regexp_replace(origin_city_name, '''', ''),
    avg(weather_delay)
FROM delays
WHERE weather_delay IS NOT NULL
GROUP BY origin_city_name;
```

To zapytanie pobiera listę miejscowości z opóźnieniami ze względu na pogodę i średni czas opóźnienia, a następnie zapisuje ją w lokalizacji `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Później narzędzie Sqoop odczyta dane z tej lokalizacji i wyeksportuje je do bazy danych Azure SQL Database.

Aby zakończyć działanie usługi Beeline, wprowadź polecenie `!quit` w wierszu polecenia.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

Aby wykonać tę operację, musisz dysponować nazwą serwera bazy danych SQL Database. Wykonaj następujące kroki, aby znaleźć nazwę serwera.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Bazy danych SQL**.

1. Filtruj listę według nazwy bazy danych, która ma zostać użyta. Nazwa serwera jest wyświetlana w kolumnie **Nazwa serwera**.

1. Filtruj listę według nazwy bazy danych, której chcesz użyć. Nazwa serwera jest wyświetlana w kolumnie **Nazwa serwera**.

    ![Uzyskiwanie szczegółów serwera Azure SQL](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Uzyskiwanie szczegółów serwera Azure SQL")

    Istnieje wiele sposobów nawiązywania połączenia z bazą danych SQL i tworzenia tabeli. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.

Aby zainstalować rozwiązanie FreeTDS, użyj następującego polecenia dla połączenia SSH z klastrem:

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

Po zakończeniu instalacji użyj poniższego polecenia, aby połączyć się z serwerem bazy danych SQL Database.

* Zastąp ciąg \<SERVER_NAME> nazwą serwera bazy danych SQL Database.
* Zastąp ciąg \<ADMIN_LOGIN> nazwą logowania administratora bazy danych SQL Database.
* Zastąp ciąg \<DATABASE_NAME> nazwą bazy danych.

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

Gdy zostanie wyświetlony monit, wprowadź hasło dla nazwy logowania administratora bazy danych SQL Database.

Uzyskasz dane wyjściowe podobne do następującego tekstu:

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

W wierszu `1>` wprowadź następujące instrukcje:

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje.
Zapytanie tworzy tabelę o nazwie **delays** z indeksem klastrowanym.

Za pomocą następującego zapytania sprawdź, czy utworzono tabelę:

```hiveql
SELECT * FROM information_schema.tables
GO
```

Dane wyjściowe będą podobne do następującego tekstu:

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Wprowadź ciąg `exit` w wierszu `1>`, aby zakończyć działanie narzędzia tsql.

## <a name="export-and-load-the-data"></a>Eksportowanie i ładowanie danych

W poprzednich sekcjach skopiowano przekształcone dane w lokalizacji `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. W tej sekcji użyjesz narzędzia Sqoop, aby wyeksportować dane z lokalizacji `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` do tabeli utworzonej w bazie danych Azure SQL Database.

Aby sprawdzić, czy baza danych SQL jest widoczna w narzędziu Sqoop, użyj następującego polecenia:

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

To polecenie zwraca listę baz danych, łącznie z bazą danych, w której utworzono tabelę **delays**.

Za pomocą następującego polecenia wyeksportuj dane z tabeli **hivesampletable** do tabeli **delays**:

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Narzędzie Sqoop nawiązuje połączenie z bazą danych zawierającą tabelę **delays** i eksportuje dane z katalogu `/tutorials/flightdelays/output` do tabeli **delays**.

Po zakończeniu działania polecenia `sqoop` użyj narzędzia tsql, aby nawiązać połączenie z bazą danych:

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

Za pomocą następujących instrukcji sprawdź, czy dane zostały wyeksportowane do tabeli **delays**:

```sql
SELECT * FROM delays
GO
```

Powinna zostać wyświetlona lista danych w tabeli. Tabela zawiera nazwę miejscowości i średni czas opóźnienia lotów dla tej miejscowości.

Wpisz polecenie `exit`, aby wyjść z narzędzia tsql.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wszystkie zasoby używane w tym samouczku istniały już wcześniej. Oczyszczanie nie jest konieczne.

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej sposobów pracy z danymi w usłudze HDInsight, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
