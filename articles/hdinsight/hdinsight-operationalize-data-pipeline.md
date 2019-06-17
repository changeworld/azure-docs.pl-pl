---
title: Operacjonalizacja potoku analizy danych — Azure
description: Skonfigurować i uruchomić przykład potoku danych, który jest wyzwalany przez nowe dane i tworzy zwięzłe wyników.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 524386c046534b0ef0050e15d326118b84822822
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718048"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operacjonalizacja potoku analizy danych

*Potoki danych* underly wiele rozwiązań do analizy danych. Jak sugeruje nazwa, potok danych pobiera nieprzetworzone dane, czyści przekształca je stosownie do potrzeb i następnie przeważnie wykonuje obliczenia lub agregacji przed zapisaniem przetworzone dane. Przetworzone dane są używane przez klientów, raportów lub interfejsów API. Należy podać powtarzalne wyniki w potoku danych, czy zgodnie z harmonogramem lub po wyzwoleniu przez nowe dane.

W tym artykule opisano jak operacjonalizować potoki danych powtarzalności, przy użyciu programu Oozie uruchomione w klastrach usługi HDInsight Hadoop. Przykładowy scenariusz przeprowadzi potoku danych, który przygotowuje i przetwarza dane szeregów czasowych lotu lotniczej.

W poniższym scenariuszu dane wejściowe są prostego pliku zawierającego partii danych lotów przez jeden miesiąc. Te dane lotu obejmują informacje, takie jak port początkowego i docelowego, mil przesyłane, wyjścia i czasy przybycia i tak dalej. Celem z tym potoku jest podsumowanie codzienną wydajność linii lotniczych, gdzie każdy linii lotniczych ma jeden wiersz dla każdego dnia średni wysyłki i przeznaczenia opóźnień w minut, a łączna liczba kilometrów przesyłane, tego samego dnia.

| YEAR | MONTH | DAY_OF_MONTH | OPERATORA |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

Przykładowy potok czeka, aż danych lotów nowy okres czasu nadejściu, a następnie przechowuje te informacje szczegółowe lot do magazynu danych Apache Hive dla długoterminowego analiz. Potok tworzy również znacznie mniejszy zestaw danych, który znajduje się podsumowanie tylko dziennego lotu dane. Ta lotu podsumowanie danych dotyczących dziennego są wysyłane do usługi SQL database w odniesieniu do sporządzanie raportów, takich jak witryny sieci Web.

Na poniższym diagramie przedstawiono przykład potoku.

![Potok danych w locie](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Omówienie rozwiązania Apache Oozie

Ten potok używa Apache Oozie uruchomione w klastrze usługi HDInsight Hadoop.

Oozie w tym artykule opisano jej potoki pod względem *akcje*, *przepływy pracy*, i *koordynatorów*. Akcje określają rzeczywistą pracę do wykonania, takie jak uruchomienie zapytania programu Hive. Przepływy pracy określają, sekwencję akcji. Koordynatorów określają harmonogram uruchamiania przepływu pracy. Koordynatorów można również poczekać na dostępność nowych danych przed uruchomieniem wystąpienia przepływu pracy.

Na poniższym diagramie przedstawiono projektowania wysokiego poziomu, ten przykład Oozie potoku.

![Potok danych lotu Oozie](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Aprowizowanie zasobów platformy Azure

Ten potok wymaga usługi Azure SQL Database i klaster usługi HDInsight Hadoop, w tej samej lokalizacji. Azure SQL Database przechowuje podsumowania danych generowanych przez potok i magazynu metadanych Oozie.

#### <a name="provision-azure-sql-database"></a>Aprowizowanie usługi Azure SQL Database

1. Przy użyciu witryny Azure portal utworzyć nową grupę zasobów o nazwie `oozie` ma zawierać wszystkie zasoby, które są używane przez ten przykład.
2. W ramach `oozie` grupę zasobów, Zainicjuj obsługę serwera SQL Azure i bazy danych. Nie potrzebujesz bazy danych większe niż w warstwie cenowej standardowa S1.
3. W witrynie Azure portal, przejdź do okienka w nowo wdrożonym bazy danych SQL i wybierz **narzędzia**.

    ![Przycisk narzędzia](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Wybierz **edytora zapytań**.

    ![Przycisk Edytor zapytań](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. W **edytora zapytań** okienku wybierz **logowania**.

    ![Przycisk logowania](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Wprowadź swoje poświadczenia bazy danych SQL, a następnie wybierz **OK**.

   ![Formularz logowania](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. W obszarze tekstowym edytora zapytań wpisz następujące instrukcje SQL, aby utworzyć `dailyflights` tabeli, w którym będą przechowywane podsumowane dane z każdego uruchomienia potoku.

    ```
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

8. Wybierz **Uruchom** do wykonywania instrukcji SQL.

    ![Przycisk Uruchom](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Usługi Azure SQL Database jest teraz gotowy.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Zainicjuj obsługę klastra Hadoop usługi HDInsight

1. W witrynie Azure portal wybierz **+ nowy** i wyszukaj HDInsight.
2. Wybierz pozycję **Utwórz**.
3. W okienku podstawy Podaj unikatową nazwę dla klastra, a następnie wybierz swoją subskrypcję platformy Azure.

    ![Nazwa klastra HDInsight i subskrypcji](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. W **typ klastra** okienku wybierz **Hadoop** klastra typu **Linux** systemu operacyjnego i najnowszej wersji klastra HDInsight. Pozostaw **klastra warstwy** na **standardowa**.

    ![Typ klastra usługi HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Wybierz **wybierz** do zastosowania wybór typu klastra.
6. Wykonaj **podstawy** okienko podawanie hasła logowania i wybierając swoje `oozie` zasobów z listy, a następnie wybierz **dalej**.

    ![W okienku podstawy HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. W **magazynu** okienko, pozostaw magazynu podstawowego typu zestawu do **usługi Azure Storage**, wybierz opcję **Utwórz nową**, a następnie podaj nazwę dla nowego konta.

    ![Ustawienia konta magazynu HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. Aby uzyskać **ustawienia magazynu metadanych**w obszarze **wybierz bazę danych SQL dla usługi Hive**, wybierz bazę danych utworzoną wcześniej.

    ![Ustawienia magazynu metadanych HDInsight Hive](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Wybierz **Uwierzytelnij bazę danych SQL**.

    ![Magazyn metadanych Hive HDInsight uwierzytelniania](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Wprowadź nazwę użytkownika bazy danych SQL i hasło, a następnie wybierz **wybierz**. 

       ![Magazyn metadanych HDInsight Hive uwierzytelnienia logowania](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Po powrocie **ustawienia magazynu metadanych** okienku wybierz bazę danych pod kątem metadanych programu Oozie przechowywania i uwierzytelniania, tak jak wcześniej. 

       ![Ustawienia magazynu metadanych HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Wybierz opcję **Dalej**.
13. Na **Podsumowanie** okienku wybierz **Utwórz** do wdrożenia klastra.

### <a name="verify-ssh-tunneling-setup"></a>Sprawdź ustawienia tunelowania SSH

Wyświetl stan usługi Koordynator i wystąpienia przepływu pracy za pomocą konsoli sieci Web programu Oozie, skonfiguruj tunelu SSH w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz [tunelu SSH](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Możesz również użyć dla programu Chrome przy użyciu [Foxy Proxy](https://getfoxyproxy.org/) rozszerzenia do przeglądania zasobów sieci web klastra przez tunel SSH. Skonfigurować go do serwera proxy wszystkie żądania za pośrednictwem hosta `localhost` port tunelu 9876. To podejście jest zgodny z podsystemu Windows dla systemu Linux, nazywana również Bash w systemie Windows 10.

1. Uruchom następujące polecenie, aby otworzyć tunel SSH do klastra:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Sprawdź, czy działa tunelu, przechodząc do narzędzia Ambari w węźle głównym, przechodząc do:

    http:\//headnodehost:8080

3. Dostęp do **konsoli sieci Web programu Oozie** w ramach Ambari, zaznacz **Oozie**, **szybkie linki**, a następnie wybierz pozycję **konsoli sieci Web programu Oozie**.

### <a name="configure-hive"></a>Konfigurowanie programu Hive

1. Pobierz przykładowy plik CSV zawierający dane lotu przez jeden miesiąc. Pobierz swój plik ZIP `2017-01-FlightData.zip` z [repozytorium HDInsight GitHub](https://github.com/hdinsight/hdinsight-dev-guide) i Rozpakuj go do pliku CSV `2017-01-FlightData.csv`. 

2. Skopiuj ten plik CSV do konta usługi Azure Storage dołączone do klastra usługi HDInsight i umieść go w `/example/data/flights` folderu.

Możesz skopiować plik przy użyciu punktu połączenia usługi w swojej `bash` sesji powłoki.

1. Użyj punktu połączenia usługi, aby skopiować pliki z komputera lokalnego w lokalnym magazynie węzła głównego klastra usługi HDInsight.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Użyj polecenia systemu plików HDFS, aby skopiować plik z magazynu lokalnego węzła głównego do usługi Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Przykładowe dane, jest teraz dostępna. Potok wymaga jednak dwie tabele programu Hive do przetwarzania, jeden dla danych przychodzących (`rawFlights`) i jeden dla podsumowane dane (`flights`). Utworzenie tych tabel w Ambari w następujący sposób.

1. Zaloguj się do narzędzia Ambari, przechodząc do protokołu http:\//headnodehost:8080.
2. Z listy usług wybierz **Hive**.

    ![Wybierając Hive narzędzia Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Wybierz **przejdź do widoku** obok etykiety 2.0 programu Hive widoku.

    ![Wybierając widoku Hive narzędzia Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. W obszarze tekstu zapytania wklej poniższe instrukcje, aby utworzyć `rawFlights` tabeli. `rawFlights` Tabeli znajdują się schematu przy odczycie plików CSV, w ramach `/example/data/flights` folderu w usłudze Azure Storage. 

    ```
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

5. Wybierz **Execute** tworzenia tabeli.

    ![Zapytanie programu hive na systemu Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Aby utworzyć `flights` tabeli, Zastąp tekst umieszczony w obszarze tekstu zapytania przy użyciu następujących instrukcji. `flights` Tabela jest tabelą zarządzanych Hive partycjonowania danych ładowane przez rok, miesiąc i dzień miesiąca. Ta tabela będzie zawierać wszystkie dane historyczne lotu, za pomocą najniższy poziom szczegółowości obecne w danych źródłowych o jeden wiersz na lot.

    ```
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

7. Wybierz **Execute** tworzenia tabeli.

### <a name="create-the-oozie-workflow"></a>Tworzenie przepływu pracy programu Oozie

Potoki zazwyczaj przetwarzanie danych w partiach w danym przedziale czasu. W takim przypadku potok przetwarza dane lotu codziennie. To podejście umożliwia wejściowych plików CSV do osiągnięcia codziennie, co tydzień, co miesiąc lub co rok.

Przykładowy przepływ pracy przetwarza lotu dane dzień po dniu, w trzech głównych kroków:

1. Uruchomienie zapytania programu Hive, aby wyodrębnić dane dla tego samego dnia, zakres dat z pliku CSV źródła, które są reprezentowane przez `rawFlights` tabeli i wstawianie danych do `flights` tabeli.
2. Uruchomienie zapytania programu Hive umożliwia dynamiczne tworzenie tabeli tymczasowej w gałęzi w ciągu dnia, który zawiera kopię danych lotów podsumowane według dnia, jak i operatora.
3. Skopiuj wszystkie dane z tabeli tymczasowej codzienne w gałęzi do miejsca docelowego przy użyciu narzędzia Apache Sqoop `dailyflights` tabeli w bazie danych SQL Azure. Sqoop odczytuje wiersze źródła danych w tej tabeli znajdującej się w usłudze Azure Storage i ładuje je do bazy danych SQL przy użyciu połączenia sterownika JDBC.

Te trzy kroki są koordynowane przez przepływ pracy programu Oozie. 

1. Utwórz kwerendę w pliku `hive-load-flights-partition.hql`.

    ```
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

    Zmienne programu Oozie należy użyć składni `${variableName}`. Te zmienne są ustawiane `job.properties` plików zgodnie z opisem w kolejnym kroku. Oozie zastępowane rzeczywistymi wartościami w czasie wykonywania.

2. Utwórz kwerendę w pliku `hive-create-daily-summary-table.hql`.

    ```
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

    To zapytanie tworzy tymczasową tabelę, która będzie przechowywać podsumowane dane przez jeden dzień, zwróć uwagę na instrukcji SELECT, która oblicza średnie opóźnienia i łączna liczba odległość przesyłane przez operatora, według dnia. Dane do tej tabeli przechowywaną w znanej lokalizacji (ścieżka wskazanego przez zmienną hiveDataFolder), dzięki czemu może służyć jako źródło dla Sqoop w następnym kroku.

3. Uruchom następujące polecenie narzędzia Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

Te trzy kroki są wyrażane jako osobne akcje trzy w następującym pliku przepływu pracy programu Oozie o nazwie `workflow.xml`.

```
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
            <archive>sqljdbc41.jar</archive>
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

Ich ścieżkę w usłudze Azure Storage są dostępne dla obu zapytań programu Hive, a pozostałe wartości zmiennych są dostarczane przez następujące `job.properties` pliku. Ten plik służy do konfigurowania przepływu pracy do uruchamiania w dniu 3 stycznia 2017 r.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Poniższa tabela zawiera podsumowanie każdej właściwości i wskazuje, gdzie można znaleźć wartości dla Twojego środowiska.

| Właściwość | Wartość źródła |
| --- | --- |
| nameNode | Pełna ścieżka do kontenera magazynu Azure dołączone do klastra usługi HDInsight. |
| jobTracker | Wewnętrzne nazwy hosta do aktywnego klastra YARN głównymi węzła. Na stronie głównej systemu Ambari z listy usług wybierz YARN, a następnie wybierz aktywny Resource Manager. Nazwa hosta identyfikatora URI jest wyświetlany w górnej części strony. Dołącz portu 8050. |
| queueName | Nazwa kolejki YARN, używane podczas planowania zadań Hive. Pozostaw domyślne. |
| oozie.use.system.libpath | Pozostaw jako PRAWDA. |
| appBase | Ścieżka do podfolderu w usłudze Azure Storage, gdzie wdrażanie przepływu pracy programu Oozie i pliki pomocnicze. |
| oozie.wf.application.path | Lokalizacja przepływu pracy programu Oozie `workflow.xml` do uruchomienia. |
| hiveScriptLoadPartition | Ścieżka do plik zapytania programu Hive w usłudze Azure Storage `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Ścieżka do plik zapytania programu Hive w usłudze Azure Storage `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Dynamicznie generowanym Nazwa do użycia dla tabeli przemieszczania. |
| hiveDataFolder | Ścieżka w usłudze Azure Storage do danych zawarty w tabeli przemieszczania. |
| sqlDatabaseConnectionString | Składnia parametry połączenia sterownika JDBC usługi Azure SQL Database. |
| sqlDatabaseTableName | Nazwa tabeli w usłudze Azure SQL Database, do którego zostaną wstawione wiersze podsumowania. Pozostaw `dailyflights`. |
| rocznie | Składnik roku, dnia, dla których lotu podsumowania są obliczane. Pozostawić. |
| miesiąc | Składnik miesiąca, dnia, dla których lotu podsumowania są obliczane. Pozostawić. |
| dzień | Dzień składnik miesiąca, dnia, dla których lotu podsumowania są obliczane. Pozostawić. |

> [!NOTE]  
> Pamiętaj zaktualizować swoją kopię `job.properties` pliku z wartościami dla danego środowiska, aby można było wdrożyć i uruchomić przepływ pracy programu Oozie.

### <a name="deploy-and-run-the-oozie-workflow"></a>Wdrażanie i uruchamianie przepływu pracy programu Oozie

Użyj połączenia usługi z sesji programu bash, aby wdrożyć przepływ pracy programu Oozie (`workflow.xml`), zapytania programu Hive (`hive-load-flights-partition.hql` i `hive-create-daily-summary-table.hql`) i konfiguracji zadań (`job.properties`).  W technologii Oozie, tylko `job.properties` pliku może istnieć w magazynie lokalnym węzła głównego. Wszystkie inne pliki muszą być przechowywane w systemie plików HDFS, w tym przypadku usługi Azure Storage. Akcja Sqoop używane przez przepływ pracy zależy od sterownika JDBC, do komunikowania się z bazą danych SQL, które muszą zostać skopiowane z węzłem głównym systemu plików HDFS.

1. Utwórz `load_flights_by_day` podfolder poniżej ścieżki użytkownika w magazynie lokalnym węzła głównego.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Skopiuj wszystkie pliki w bieżącym katalogu ( `workflow.xml` i `job.properties` plików) do `load_flights_by_day` podfolderu.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH węzłem głównym i przejdź do `load_flights_by_day` folderu.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Skopiuj pliki przepływu pracy do systemu plików HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Kopiuj `sqljdbc41.jar` z lokalnego węzła głównego do folderu przepływu pracy w systemie plików HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Uruchom przepływ pracy.

        oozie job -config job.properties -run

7. Sprawdź, czy stan za pomocą konsoli sieci Web programu Oozie. W ramach Ambari, zaznacz **Oozie**, **szybkich łączy**, a następnie **konsoli sieci Web programu Oozie**. W obszarze **zadania przepływu pracy** zaznacz **wszystkie zadania**.

    ![Oozie Web Console Workflows](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. Jeśli stan jest zakończyło się pomyślnie, zapytanie tabeli bazy danych SQL, aby wyświetlić wstawione wiersze. Przy użyciu witryny Azure portal, przejdź do okienka dla usługi SQL Database, wybierz **narzędzia**, a następnie otwórz **edytora zapytań**.

        SELECT * FROM dailyflights

Teraz, gdy w ciągu dnia jeden test jest uruchomiony przepływ pracy, może zawijać się ten przepływ pracy przy użyciu koordynatora, która planuje przepływu pracy, dzięki czemu jest uruchamiane raz dziennie.

### <a name="run-the-workflow-with-a-coordinator"></a>Uruchamianie przepływu pracy przy użyciu koordynatora

Aby zaplanować ten przepływ pracy, aby była uruchamiana codziennie (lub wszystkie dni w zakresie dat), można użyć koordynatora. Koordynator jest definiowany przez plik XML, na przykład `coordinator.xml`:

```
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

Jak widać, większość koordynatora po prostu przekazuje informacje o konfiguracji dla wystąpienia przepływu pracy. Istnieje jednak kilka istotnych elementów w celu wyróżnienia.

* Punkt 1: `start` i `end` atrybuty na `coordinator-app` samego elementu sterowania przedział czasu, w którym jest uruchamiany koordynatora.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordynator jest odpowiedzialna za planowanie akcji w ramach `start` i `end` zakres, dat, zgodnie z interwałem określonym przez `frequency` atrybutu. Każda akcja zaplanowane z kolei uruchamia przepływ pracy, zgodnie z konfiguracją. W powyższej definicji koordynator Koordynator jest skonfigurowany do uruchamiania działań od 1 stycznia 2017 r. do 5 stycznia 2017 r. Częstotliwość jest ustawiona na 1 dzień, [język wyrażeń Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) wyrażenie częstotliwość `${coord:days(1)}`. Skutkuje to koordynator planowania akcję (a tym samym przepływu pracy) raz dziennie. Dla zakresów dat, które znajdują się w przeszłości, jak w poniższym przykładzie akcja zostanie zaplanowane do uruchomienia bez opóźnień. Początek daty, w którym akcja jest zaplanowane do uruchomienia jest nazywany *nominalna czasu*. Na przykład do przetwarzania danych dla 1 stycznia 2017 r. koordynator będą planować akcji z nominalna czasem 2017-01-01T00:00:00 GMT.

* Punkt 2: W zakresie przepływu pracy `dataset` element określa, gdzie szukać w systemie plików HDFS dane dla zakresu określonej daty i konfiguruje się, jak Oozie Określa, czy dane są dostępne jeszcze do przetworzenia.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Ścieżka do danych w systemie plików HDFS jest tworzona dynamicznie zgodnie z wyrażeniem w `uri-template` elementu. W tym koordynatora częstotliwości równej jeden dzień również jest używany z zestawem danych. Podczas działania są zaplanowane (i definiuje ich czasy nominalna), podczas gdy daty rozpoczęcia i zakończenia w kontrolce elementu koordynator `initial-instance` i `frequency` w zestawie danych, kontrolować obliczanie daty, który jest używany podczas tworzenia `uri-template`. W tym przypadku wartość pierwszego wystąpienia jeden dzień przed rozpoczęciem koordynatora, aby upewnić się, wybiera się pierwszego dnia przez dane pomagają w zrealizowaniu (1/1/2017). Obliczenia daty typizowanego zestawu danych przenosi do przodu od wartości `initial-instance` przesuwania (12/31/2016), z przyrostem równym częstotliwość zestawu danych (1 dzień), aż do znalezienia Data ostatniego, które nie przekaże nominalna czas ustawiony przez koordynatora (2017-01-01T00:00:00 GMT dla pierwszej akcji).

    Pusty `done-flag` element wskazuje, że podczas Oozie sprawdza obecność danych wejściowych w wyznaczonym czasie, Oozie określa danych czy są dostępne przez obecność pliku lub katalogu. W tym przypadku jest obecność pliku csv. Jeśli plik csv jest obecny, Oozie zakłada danych jest gotowy i uruchamia wystąpienie przepływu pracy do przetwarzania pliku. Jeśli istnieje plik csv, nie jest obecny, Oozie przyjęto założenie, że dane nie są jeszcze gotowe i że przebieg przepływu pracy przechodzi w stan oczekiwania.

* Punkt 3: `data-in` Element określa określonej sygnatury czasowej do użycia jako nominalna czas podczas zastępowania wartości w `uri-template` dla skojarzonego zestawu danych.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    W tym przypadku Przypisz wystąpienie do wyrażenia `${coord:current(0)}`, co przekłada się przy użyciu nominalna czasu działania pierwotnie zaplanowane przez koordynatora. Oznacza to gdy koordynator planuje akcję do przeprowadzenia nominalna czas 2017-01-01, 01/01/2017 będzie wówczas co to jest używany do zastępowania roku (2017) i miesiąca (01) zmiennych w szablon identyfikatora URI. Gdy szablon identyfikatora URI jest obliczana dla tego wystąpienia, Oozie sprawdza, czy oczekiwany katalog lub plik jest dostępna i odpowiednio planuje następnego uruchomienia przepływu pracy.

Połącz trzech poprzednich punktach umożliwiające uzyskanie sytuacji, w którym koordynator planuje przetwarzania źródła danych w czasie każdego dnia. 

* Punkt 1: Koordynator rozpoczyna się od nominalna dnia 2017-01-01.

* Punkt 2: Oozie szuka danych dostępnych w `sourceDataFolder/2017-01-FlightData.csv`.

* Punkt 3: Jeśli Oozie znajdzie się ten plik, planuje wystąpienie przepływu pracy, który przetworzy dane do 2017-01-01. Oozie następnie kontynuuje przetwarzanie 2017-01-02. Tego okresu ewaluacji powtarza się maksymalnie z wyjątkiem 2017-01-05.

Jak przy użyciu przepływów pracy, konfigurację Koordynator jest zdefiniowany w `job.properties` pliku, który jest nadzbiorem ustawieniami używanymi przez przepływ pracy.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Tylko nowe właściwości wprowadzone w tym `job.properties` pliku są:

| Właściwość | Wartość źródła |
| --- | --- |
| oozie.coord.application.path | Wskazuje lokalizację `coordinator.xml` plik zawierający koordynatora programu Oozie do uruchomienia. |
| hiveDailyTableNamePrefix | Prefiks używany podczas tworzenia dynamicznie Nazwa tabeli tymczasowej. |
| hiveDataFolderPrefix | Prefiks ścieżki, w którym będą przechowywane wszystkie tabele przemieszczania. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Wdrażanie i uruchamianie koordynatora programu Oozie

Aby uruchomić potok przy użyciu koordynatora, przejdź w podobny sposób jak w przypadku przepływu pracy, z wyjątkiem pracować z folderem o jeden poziom wyżej folder, który zawiera przepływ pracy. Ta konwencja folderu oddziela koordynatorów od przepływów pracy na dysku, więc jednego koordynatora można skojarzyć z inną podrzędne przepływy pracy.

1. Użyj punkt połączenia usługi na maszynie lokalnej, aby skopiować plików koordynator o rozmiarze do magazynu lokalnego węzła głównego klastra.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. Nawiąż połączenie z węzłem głównym.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Skopiuj pliki koordynatora do systemu plików HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Uruchom koordynatora.

    ```bash
    oozie job -config job.properties -run
    ```

5. Sprawdź stan za pomocą konsoli sieci Web programu Oozie, wybierając ten czas **koordynator zadań** kartę, a następnie **wszystkie zadania**.

    ![Oozie Web Console Coordinator Jobs](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Wybierz wystąpienie koordynatora, aby wyświetlić listę zaplanowanych akcji. W tym przypadku powinny być widoczne cztery akcje za pomocą nominalna razy w zakresie od 1/1/2017 do 1/4/2017.

    ![Oozie Web Console Coordinator Job](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Każda akcja na tej liście odnosi się do wystąpienia przepływu pracy, który przetwarza jednego dnia, przez które danych, gdzie początku tego samego dnia jest wskazywany przez nominalna czasu.

## <a name="next-steps"></a>Kolejne kroki

* [Apache Oozie dokumentacji](https://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
