---
title: Operacjonalizacja potoku analizy danych — Platforma Azure
description: Skonfiguruj i uruchom przykładowy potok danych, który jest wyzwalany przez nowe dane i daje zwięzłe wyniki.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922661"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacjonalizacja potoku analizy danych

*Potoki danych* w ramach wielu rozwiązań do analizy danych. Jak sama nazwa wskazuje, potok danych przyjmuje nieprzetworzone dane, czyści i przekształca je w razie potrzeby, a następnie zazwyczaj wykonuje obliczenia lub agregacje przed zapisaniem przetworzonych danych. Przetwarzane dane są używane przez klientów, raporty lub interfejsy API. Potok danych musi zawierać powtarzalne wyniki, czy zgodnie z harmonogramem lub po wyzwoleniu przez nowe dane.

W tym artykule opisano sposób operacjonalizacji potoków danych w celu powtarzalności przy użyciu funkcji Oozie uruchomionej w klastrach programu HDInsight Hadoop. Przykładowy scenariusz przeprowadzi Cię przez potok danych, który przygotowuje i przetwarza dane szeregów czasowych lotu linii lotniczych.

W poniższym scenariuszu dane wejściowe są plikiem płaskim zawierającym partię danych lotu przez jeden miesiąc. Te dane lotu obejmują takie informacje, jak lotnisko pochodzenia i przeznaczenia, przebyty mil, godziny odlotu i przylotu itd. Celem tego rurociągu jest podsumowanie codziennych wyników linii lotniczych, gdzie każda linia lotnicza ma jeden wiersz na każdy dzień ze średnimi opóźnieniami odlotów i przylotów w minutach i całkowitą liczą mil przebytą tego dnia.

| YEAR | MONTH | DAY_OF_MONTH | Przewoźnika |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | Aa | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | Dl | 6.935409 | -2.1893024 | 1909696 |

Przykładowy potok czeka, aż nadejdzie dane lotu nowego okresu, a następnie przechowuje szczegółowe informacje o locie w magazynie danych Apache Hive w celu uzyskania analiz długoterminowych. Potok tworzy również znacznie mniejszy zestaw danych, który podsumowuje tylko dzienne dane lotu. Te codzienne dane podsumowania lotu są wysyłane do bazy danych SQL w celu dostarczenia raportów, takich jak dla witryny sieci Web.

Na poniższym diagramie przedstawiono przykładowy potok.

![Omówienie potoku danych o przykładzie lotu HDI](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Apache Oozie — omówienie rozwiązania

Ten potok używa Apache Oozie uruchomionego w klastrze HDInsight Hadoop.

Oozie opisuje swoje potoki pod względem *działań,* *przepływów pracy*i *koordynatorów.* Akcje określają rzeczywistą pracę do wykonania, na przykład uruchamianie kwerendy hive. Przepływy pracy definiują sekwencję akcji. Koordynatorzy definiują harmonogram uruchamiania przepływu pracy. Koordynatorzy mogą również czekać na dostępność nowych danych przed uruchomieniem wystąpienia przepływu pracy.

Na poniższym diagramie przedstawiono projekt wysokiego poziomu tego przykładowego potoku Oozie.

![Oozie Flight przykład potok danych](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Aprowizuj zasoby platformy Azure

Ten potok wymaga usługi Azure SQL Database i klastra PROGRAMU HDInsight Hadoop w tej samej lokalizacji. Usługa Azure SQL Database przechowuje zarówno dane podsumowania produkowane przez potok i Magazyn metadanych Oozie.

### <a name="provision-azure-sql-database"></a>Aprowizuj bazę danych SQL platformy Azure

1. Tworzenie bazy danych SQL platformy Azure. Zobacz [Tworzenie bazy danych SQL platformy Azure w witrynie Azure portal](../sql-database/sql-database-single-database-get-started.md).

1. Aby upewnić się, że klaster USŁUGI HDInsight może uzyskać dostęp do połączonej bazy danych SQL Database platformy Azure, skonfiguruj reguły zapory usługi Azure SQL Database, aby umożliwić dostęp do serwera usługom i zasobom platformy Azure. Tę opcję można włączyć w witrynie Azure portal, wybierając **pozycję Ustaw zaporę serwera**i wybierając **on** poniżej **Zezwalaj na usługi i zasoby platformy Azure, aby uzyskać dostęp do tego serwera** dla serwera lub bazy danych usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Użyj [edytora zapytań,](../sql-database/sql-database-single-database-get-started.md#query-the-database) aby wykonać `dailyflights` następujące instrukcje SQL, aby utworzyć tabelę, która będzie przechowywać podsumowane dane z każdego uruchomienia potoku.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Usługa Azure SQL Database jest już gotowa.

### <a name="provision-an-apache-hadoop-cluster"></a>Aprowizuj klaster Apache Hadoop

Utwórz klaster Apache Hadoop z niestandardowym magazynem metasklepu. Podczas tworzenia klastra z portalu na karcie **Magazyn** upewnij się, że w obszarze **Ustawienia metasklepu**wybrano bazę danych SQL . Aby uzyskać więcej informacji na temat wybierania magazynu met, zobacz [Wybieranie niestandardowego magazynu metasklepu podczas tworzenia klastra](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Wprowadzenie do usługi HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Sprawdź konfigurację tunelowania SSH

Aby użyć konsoli sieci Web Oozie do wyświetlania stanu wystąpienia koordynatora i przepływu pracy, skonfiguruj tunel SSH do klastra HDInsight. Aby uzyskać więcej informacji, zobacz [SSH Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Chrome z rozszerzeniem [Foxy Proxy](https://getfoxyproxy.org/) umożliwia przeglądanie zasobów internetowych klastra w tunelu SSH. Skonfiguruj go do `localhost` proxy wszystkich żądań za pośrednictwem hosta w porcie tunelu 9876. Takie podejście jest zgodne z podsystemem Windows dla systemu Linux, znanym również jako Bash w systemie Windows 10.

1. Uruchom następujące polecenie, aby otworzyć tunel SSH `CLUSTERNAME` do klastra, gdzie jest nazwa klastra:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Sprawdź, czy tunel działa, przechodząc do Ambari w węźle głównym, przechodząc do:

    `http://headnodehost:8080`

1. Aby uzyskać dostęp do **konsoli internetowej Oozie** z poziomu Ambari, przejdź do pozycji Szybkie**łącza** **Oozie** > > [Aktywny serwer] > **interfejsie użytkownika sieci Oozie Web**.

## <a name="configure-hive"></a>Konfigurowanie gałęzi

### <a name="upload-data"></a>Przekazywanie danych

1. Pobierz przykładowy plik CSV zawierający dane lotu przez jeden miesiąc. Pobierz jego `2017-01-FlightData.zip` plik ZIP z [repozytorium GItHub HDInsight](https://github.com/hdinsight/hdinsight-dev-guide) i rozpakuj go do pliku `2017-01-FlightData.csv`CSV .

1. Skopiuj ten plik CSV do konta usługi Azure Storage dołączonego do klastra USŁUGI HDInsight i umieść go w folderze. `/example/data/flights`

    1. Użyj protokołu SCP, aby skopiować pliki z komputera lokalnego do magazynu lokalnego węzła głównego klastra usługi HDInsight.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Użyj [polecenia ssh,](./hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. W sesji ssh użyj polecenia HDFS, aby skopiować plik z magazynu lokalnego węzła głównego do usługi Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Tworzenie tabel

Przykładowe dane są już dostępne. Jednak potok wymaga dwóch tabel hive do przetwarzania,`rawFlights`jednej dla przychodzących danych`flights`( ) i jednej dla podsumowanych danych ( ). Utwórz te tabele w Ambari w następujący sposób.

1. Zaloguj się do Ambari, `http://headnodehost:8080`przechodząc do .

2. Z listy usług wybierz pozycję **Gałąź**.

    ![Lista usług Apache Ambari wybierając Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Wybierz **pozycję Przejdź do widoku** obok etykiety Widok ul.

    ![Ambari Apache Hive lista podsumowań](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. W obszarze tekstowym kwerendy wklej następujące `rawFlights` instrukcje, aby utworzyć tabelę. Tabela `rawFlights` zawiera schemat do odczytu dla plików CSV w folderze `/example/data/flights` w usłudze Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Wybierz **pozycję Wykonaj,** aby utworzyć tabelę.

    ![hdi ambari usługi hive zapytanie](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Aby utworzyć `flights` tabelę, zastąp tekst w obszarze tekstowym kwerendy następującymi instrukcjami. Tabela `flights` jest tabelą zarządzaną gałęzią, która dzieli dane partycjonowania do niej według roku, miesiąca i dnia miesiąca. Ta tabela będzie zawierać wszystkie historyczne dane lotu, z najniższą szczegółowością obecną w danych źródłowych jednego wiersza na lot.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Wybierz **pozycję Wykonaj,** aby utworzyć tabelę.

## <a name="create-the-oozie-workflow"></a>Tworzenie przepływu pracy Oozie

Potoki zazwyczaj przetwarzają dane w partiach według danego przedziału czasu. W takim przypadku potok przetwarza dane lotu codziennie. Takie podejście pozwala na wprowadzanie plików CSV, aby dotrzeć codziennie, co tydzień, co miesiąc lub rocznie.

Przykładowy przepływ pracy przetwarza dane lotu dzień po dniu, w trzech głównych krokach:

1. Uruchom kwerendę hive, aby wyodrębnić dane dla zakresu dat tego dnia `rawFlights` ze źródłowego pliku `flights` CSV reprezentowanego przez tabelę i wstawić dane do tabeli.
2. Uruchom kwerendę hive dynamicznie utworzyć tabelę przemieszczania w gałęzi dla dnia, który zawiera kopię danych lotu podsumowane według dnia i przewoźnika.
3. Użyj Apache Sqoop, aby skopiować wszystkie dane z dziennej tabeli przemieszczania w gałęzi do tabeli docelowej `dailyflights` w bazie danych SQL azure. Sqoop odczytuje wiersze źródłowe z danych za tabelą Hive zamieszkałą w usłudze Azure Storage i ładuje je do bazy danych SQL przy użyciu połączenia JDBC.

Te trzy kroki są koordynowane przez przepływ pracy Oozie.

1. Z lokalnej stacji roboczej utwórz plik o nazwie `job.properties`. Użyj poniższego tekstu jako zawartości początkowej pliku.
Następnie zaktualizuj wartości dla określonego środowiska. W poniższej tabeli tekst podsumowuje każdą z właściwości i wskazuje, gdzie można znaleźć wartości dla własnego środowiska.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Właściwość | Źródło wartości |
    | --- | --- |
    | nazwaNok. | Pełna ścieżka do kontenera usługi Azure Storage dołączone do klastra USŁUGI HDInsight. |
    | jobTracker | Wewnętrzna nazwa hosta węzła YARN klastra aktywnego. Na stronie głównej Ambari wybierz pozycję YARN z listy usług, a następnie wybierz pozycję Active Resource Manager. Identyfikator URI nazwy hosta jest wyświetlany w górnej części strony. Dołącz port 8050. |
    | Queuename | Nazwa kolejki YARN używane podczas planowania akcji hive. Pozostaw jako domyślny. |
    | oozie.use.system.libpath | Zostaw jako prawdziwe. |
    | Baza aplikacji | Ścieżka do podfolderu w usłudze Azure Storage, w której wdrażasz przepływ pracy usługi Oozie i pliki pomocnicze. |
    | oozie.wf.application.path | Lokalizacja przepływu `workflow.xml` pracy Oozie do uruchomienia. |
    | hiveScriptLoadPartition (hiveScriptLoadPartition) | Ścieżka w usłudze Azure Storage `hive-load-flights-partition.hql`do pliku zapytania hive . |
    | ulScriptCreateDailyTable | Ścieżka w usłudze Azure Storage `hive-create-daily-summary-table.hql`do pliku zapytania hive . |
    | ulDailyTableName | Dynamicznie generowana nazwa używana dla tabeli przemieszczania. |
    | gałka danych hive | Ścieżka w usłudze Azure Storage do danych zawartych w tabeli przemieszczania. |
    | sqlDatabaseConnectionStrowanie | Ciąg połączenia składni JDBC do bazy danych SQL Azure. |
    | sqlDatabaseTableName | Nazwa tabeli w usłudze Azure SQL Database, do której są wstawiane wiersze podsumowania. Pozostaw jako `dailyflights`. |
    | rok | Składnik roku dnia, dla którego obliczane są podsumowania lotu. Zostaw tak, jak jest. |
    | miesiąc | Składnik miesiąca dnia, dla którego obliczane są podsumowania lotu. Zostaw tak, jak jest. |
    | dzień | Składnik dnia miesiąca dnia, dla którego obliczane są podsumowania lotu. Zostaw tak, jak jest. |

1. Z lokalnej stacji roboczej utwórz plik o nazwie `hive-load-flights-partition.hql`. Użyj poniższego kodu jako zawartości pliku.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Zmienne Oozie używają składni `${variableName}`. Te zmienne są `job.properties` ustawione w pliku. Oozie zastępuje rzeczywiste wartości w czasie wykonywania.

1. Z lokalnej stacji roboczej utwórz plik o nazwie `hive-create-daily-summary-table.hql`. Użyj poniższego kodu jako zawartości pliku.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Ta kwerenda tworzy tabelę przemieszczania, która będzie przechowywać tylko podsumowane dane przez jeden dzień, należy wziąć pod uwagę SELECT instrukcji, która oblicza średnie opóźnienia i całkowitą odległość przebytej przez przewoźnika w ciągu dnia. Dane wstawione do tej tabeli przechowywane w znanej lokalizacji (ścieżka wskazana przez hiveDataFolder zmiennej), tak aby można było użyć jako źródło Sqoop w następnym kroku.

1. Z lokalnej stacji roboczej utwórz plik o nazwie `workflow.xml`. Użyj poniższego kodu jako zawartości pliku. Powyższe kroki są wyrażone jako oddzielne akcje w pliku przepływu pracy Oozie.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

Dwa zapytania hive są dostępne przez ich ścieżki w usłudze Azure Storage, a pozostałe wartości zmiennych są dostarczane przez `job.properties` plik. Ten plik konfiguruje przepływ pracy do uruchomienia dla daty 3 stycznia 2017 r.

## <a name="deploy-and-run-the-oozie-workflow"></a>Wdrażanie i uruchamianie przepływu pracy Oozie

Użyj protokołu SCP z sesji bash, aby`workflow.xml`wdrożyć przepływ pracy`hive-load-flights-partition.hql` usługi `hive-create-daily-summary-table.hql`Oozie (`job.properties`), zapytania gałęzi ( i ) i konfigurację zadania ( ).  W Oozie tylko `job.properties` plik może istnieć w lokalnym magazynie headnode. Wszystkie inne pliki muszą być przechowywane w usłudze HDFS, w tym przypadku usługi Azure Storage. Sqoop akcji używanej przez przepływ pracy zależy od sterownika JDBC do komunikowania się z bazy danych SQL, które muszą być kopiowane z węzła głównego do hdfs.

1. Utwórz `load_flights_by_day` podfolder pod ścieżką użytkownika w magazynie lokalnym węzła głównego. Z otwartej sesji ssh wykonaj następujące polecenie:

    ```bash
    mkdir load_flights_by_day
    ```

1. Skopiuj wszystkie pliki `workflow.xml` w `job.properties` bieżącym katalogu `load_flights_by_day` (i plikach) do podfolderu. Na lokalnej stacji roboczej wykonaj następujące polecenie:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Kopiowanie plików przepływu pracy do usługi HDFS. Z otwartej sesji ssh wykonaj następujące polecenia:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Skopiuj `mssql-jdbc-7.0.0.jre8.jar` z lokalnego węzła głównego do folderu przepływu pracy w pamięci HDFS. W razie potrzeby popraw polecenie, jeśli klaster zawiera inny plik jar. W `workflow.xml` razie potrzeby należy poprawić, aby odzwierciedlić inny plik słoika. Z otwartej sesji ssh wykonaj następujące polecenie:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Uruchom przepływ pracy. Z otwartej sesji ssh wykonaj następujące polecenie:

    ```bash
    oozie job -config job.properties -run
    ```

1. Obserwuj stan za pomocą konsoli internetowej Oozie. Z poziomu Ambari wybierz pozycję **Oozie**, **Szybkie łącza**, a następnie **konsolę internetową Oozie**. Na karcie **Zadania przepływu pracy** wybierz pozycję Wszystkie **zadania**.

    ![hdi oozie przepływy pracy konsoli internetowej](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Gdy stan jest pomyślnie, kwerendy tabeli bazy danych SQL, aby wyświetlić wstawione wiersze. Korzystając z witryny Azure Portal, przejdź do okienka bazy danych SQL, wybierz pozycję **Narzędzia**i otwórz **Edytor zapytań**.

        SELECT * FROM dailyflights

Teraz, gdy przepływ pracy jest uruchomiony dla pojedynczego dnia testu, można zawinąć ten przepływ pracy z koordynatorem, który planuje przepływ pracy, dzięki czemu jest uruchamiany codziennie.

## <a name="run-the-workflow-with-a-coordinator"></a>Uruchamianie przepływu pracy z koordynatorem

Aby zaplanować ten przepływ pracy tak, aby był uruchamiany codziennie (lub wszystkie dni w zakresie dat), można użyć koordynatora. Koordynator jest definiowany przez plik XML, na przykład: `coordinator.xml`

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Jak widać, większość koordynatora jest po prostu przekazywanie informacji o konfiguracji do wystąpienia przepływu pracy. Istnieje jednak kilka ważnych elementów, które należy wywołać.

* Punkt 1: `start` `end` I atrybuty na samym elemencie `coordinator-app` kontrolują przedział czasu, w którym działa koordynator.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordynator jest odpowiedzialny za planowanie `start` akcji `end` w zakresie i zakresu dat, zgodnie z interwałem określonym przez `frequency` atrybut. Każda zaplanowana akcja z kolei uruchamia przepływ pracy zgodnie z konfiguracją. W powyższej definicji koordynatora jest skonfigurowany do uruchamiania działań od 1 stycznia 2017 r. do 5 stycznia 2017 r. Częstotliwość jest ustawiona na jeden dzień przez wyrażenie `${coord:days(1)}`częstotliwości [języka wyrażenia Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) . Powoduje to, że koordynator planuje akcję (a tym samym przepływ pracy) raz dziennie. W przypadku zakresów dat, które są w przeszłości, jak w tym przykładzie, akcja zostanie zaplanowana do uruchomienia bezzwłocznie. Początek daty, od której akcja jest zaplanowana do uruchomienia jest nazywany *czas nominalny*. Na przykład, aby przetworzyć dane za 1 stycznia 2017 r., koordynator zaplanuje akcję o nominalnym czasie 2017-01-01T00:00:00 GMT.

* Punkt 2: W zakresie dat przepływu `dataset` pracy element określa, gdzie szukać w hdfs dla danych dla określonego zakresu dat i konfiguruje sposób Oozie określa, czy dane są jeszcze dostępne do przetworzenia.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Ścieżka do danych w hdfs jest zbudowany dynamicznie zgodnie `uri-template` z wyrażeniem podanym w elemencie. W tym koordynatorze częstotliwość jednego dnia jest również używana z zestawem danych. Podczas gdy daty rozpoczęcia i zakończenia na formancie elementu koordynatora, gdy `initial-instance` `frequency` akcje są zaplanowane (i definiuje ich nominalne czasy), i na zbiorze danych kontrolować obliczanie daty, która jest używana do konstruowania `uri-template`. . W takim przypadku należy ustawić wystąpienie początkowe na jeden dzień przed rozpoczęciem koordynatora, aby upewnić się, że odbiera on dane pierwszego dnia (1/1/2017). Obliczanie daty zestawu danych jest przesuwające się dalej od wartości `initial-instance` (31.12.2016 r.) z postępem w przyrostach częstotliwości zestawu danych (jeden dzień), aż znajdzie najnowszą datę, która nie uchwala nominalnego czasu ustawionego przez koordynatora (2017-01-01T00:00:00 GMT dla pierwszej akcji).

    Pusty `done-flag` element wskazuje, że gdy Oozie sprawdza obecność danych wejściowych w wyznaczonym czasie, Oozie określa dane, czy są dostępne przez obecność katalogu lub pliku. W tym przypadku jest to obecność pliku csv. Jeśli plik csv jest obecny, Oozie zakłada, że dane są gotowe i uruchamia wystąpienie przepływu pracy do przetwarzania pliku. Jeśli nie ma pliku csv, Oozie zakłada, że dane nie są jeszcze gotowe i że uruchomienie przepływu pracy przechodzi w stan oczekiwania.

* Punkt 3: `data-in` Element określa określony sygnatura czasowa, który `uri-template` ma być używany jako czas nominalny podczas zastępowania wartości dla skojarzonego zestawu danych.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    W takim przypadku ustaw wystąpienie `${coord:current(0)}`na wyrażenie , co przekłada się na użycie nominalnego czasu akcji pierwotnie zaplanowanego przez koordynatora. Innymi słowy, gdy koordynator planuje działanie z nominalnym czasem 01/01/2017, następnie 01/01/2017 jest to, co jest używane do zastąpienia YEAR (2017) i MIESIĄC (01) zmiennych w szablonie URI. Po obliczeniu szablonu identyfikatora URI dla tego wystąpienia Oozie sprawdza, czy oczekiwany katalog lub plik jest dostępny i odpowiednio planuje następne uruchomienie przepływu pracy.

Trzy poprzednie punkty łączą się w celu uzyskania sytuacji, w której koordynator planuje przetwarzanie danych źródłowych w sposób dzień po dniu.

* Punkt 1: Koordynator rozpoczyna się od nominalnej daty 2017-01-01.

* Punkt 2: Oozie szuka `sourceDataFolder/2017-01-FlightData.csv`danych dostępnych w .

* Punkt 3: Gdy Oozie znajdzie ten plik, planuje wystąpienie przepływu pracy, który będzie przetwarzać dane dla 2017-01-01. Następnie Oozie kontynuuje przetwarzanie dla 2017-01-02. Ocena ta powtarza się do 2017-01-05, ale nie obejmuje.

Podobnie jak w przypadku przepływów pracy, konfiguracja koordynatora jest zdefiniowana `job.properties` w pliku, który ma nadzbiór ustawień używanych przez przepływ pracy.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

Jedynymi nowymi właściwościami `job.properties` wprowadzonymi w tym pliku są:

| Właściwość | Źródło wartości |
| --- | --- |
| oozie.coord.application.path | Wskazuje lokalizację pliku `coordinator.xml` zawierającego koordynatora Oozie do uruchomienia. |
| hiveDailyTableNamePrefix | Prefiks używany podczas dynamicznego tworzenia nazwy tabeli przemieszczania. |
| poprawka do gałki tabeli hiveDataFolder | Prefiks ścieżki, w której będą przechowywane wszystkie tabele przemieszczania. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Wdrażanie i uruchamianie koordynatora Oozie

Aby uruchomić potok u koordynatora, postępuj w podobny sposób jak w przypadku przepływu pracy, z wyjątkiem pracy z folderu jeden poziom powyżej folderu, który zawiera przepływ pracy. Ta konwencja folderów oddziela koordynatorów od przepływów pracy na dysku, dzięki czemu można skojarzyć jednego koordynatora z różnymi podrzędnymi przepływami pracy.

1. Użyj protokołu SCP z komputera lokalnego, aby skopiować pliki koordynatora do lokalnego magazynu węzła głównego klastra.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH do węzła głównego.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Skopiuj pliki koordynatora do usługi HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Uruchom koordynatora.

    ```bash
    oozie job -config job.properties -run
    ```

5. Sprawdź stan za pomocą konsoli sieci Web Oozie, tym razem wybierając kartę **Zadania koordynatora,** a następnie **Wszystkie zadania**.

    ![Oozie Web Console Coordinator Jobs](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Wybierz wystąpienie koordynatora, aby wyświetlić listę zaplanowanych akcji. W takim przypadku powinieneś zobaczyć cztery akcje o nominalnych czasach w zakresie od 1/1/2017 do 1/4/2017.

    ![Zadanie koordynatora konsoli internetowej Oozie](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Każda akcja na tej liście odpowiada wystąpieniu przepływu pracy, który przetwarza dane z jednego dnia, gdzie początek tego dnia jest wskazywany przez czas nominalny.

## <a name="next-steps"></a>Następne kroki

[Apache Oozie Dokumentacja](https://oozie.apache.org/docs/4.2.0/index.html)
